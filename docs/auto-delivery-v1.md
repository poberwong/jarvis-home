# 自动化交付系统 v1（合规版）

## 1. 目标
把“支付后自动发货”做成一条稳定流水线：
- 用户下单后自动收到数字商品（Token / 激活码 / 账号凭据）
- 减少人工客服与重复操作
- 全程可审计、可追踪、可回滚

> 重点：做“自动化交付能力”，不做代实名/KYC/绕监管等高风险金融代办。

---

## 2. 业务边界（先定规则）

### 可做
- API Key / 订阅兑换码 / 软件 License 自动发放
- 账号池中“已合规可分发资源”的自动分配
- 自动重发、自动补单、自动退款审批流（低风险规则）

### 不做（红线）
- 代开户、代实名、代 KYC
- 绕过平台风控或地域限制
- 资金代收代付 / 可疑 U 进出
- 明显违反平台条款的账号交易

---

## 3. 核心流程（MVP）

1. 用户下单（Web/H5）
2. 发起支付（微信/支付宝/Stripe 等）
3. 支付回调（Webhook）
4. 订单服务校验签名与金额
5. 资源服务从库存池分配一个可用资源
6. 交付服务发货（站内 + 邮件 + IM）
7. 审计服务记录完整日志（订单号、资源ID、操作人、时间）
8. 风控服务异步扫描异常（高频购买、失败重试、设备异常）

---

## 4. 技术架构（单人可维护）

## 架构模块
- **API Gateway**：统一入口（鉴权、限流）
- **Order Service**：订单生命周期管理
- **Payment Webhook Service**：支付通知接收与验签
- **Inventory Service**：库存池（可发货资源）
- **Delivery Service**：消息投递（站内/邮件/IM）
- **Risk Engine**：规则风控（先规则，后模型）
- **Audit Log**：不可篡改审计日志

## 推荐技术栈
- 后端：Node.js (NestJS) 或 Python (FastAPI)
- 数据库：PostgreSQL
- 缓存：Redis
- 队列：Redis Stream / RabbitMQ（二选一，v1 可先 Redis）
- 前端：Next.js（管理后台 + 订单查询页）
- 部署：Docker + 1 台云主机（先单机）
- 监控：Prometheus + Grafana（或最简 Sentry + 日志告警）

---

## 5. 数据模型（最小集）

### `orders`
- id
- user_id
- product_id
- amount
- status (`pending/paid/delivered/failed/refunded`)
- payment_txn_id
- created_at / paid_at / delivered_at

### `inventory_items`
- id
- product_id
- secret_payload（加密存储）
- status (`available/locked/delivered/disabled`)
- lock_expire_at

### `deliveries`
- id
- order_id
- channel (`site/email/imessage/...`)
- status (`queued/sent/failed`)
- retry_count

### `audit_events`
- id
- event_type
- actor (`system/user/admin`)
- ref_id
- payload_json
- created_at

---

## 6. 关键控制点

1. **幂等性**
   - 同一个支付回调只处理一次（`payment_txn_id` 唯一约束）

2. **库存锁**
   - 分配资源前先加锁，防止并发超卖

3. **密钥安全**
   - `secret_payload` 用 KMS/环境密钥加密
   - 后台默认脱敏显示

4. **失败补偿**
   - 发货失败自动重试（指数退避）
   - 超阈值进人工工单

5. **审计可追溯**
   - 每一步写 `audit_events`

---

## 7. 风控规则（v1 规则引擎）

- 同设备/同IP 1小时内下单 > N 次 => 阻断并人工复核
- 支付成功但回调来源异常 => 暂停交付
- 同账号短时间触发大量“重发” => 限制 + 验证
- 高风险国家/代理IP => 提高审核等级

---

## 8. 上线里程碑（2~3周）

### Week 1
- 订单 + 支付回调 + 库存分配最小闭环
- 管理后台可查看订单状态

### Week 2
- 自动发货（邮件/IM）
- 重试队列 + 基础风控规则
- 审计日志可检索

### Week 3
- 退款/补发流程
- 告警与日报（失败率、交付时延、转化率）
- 灰度与备份策略

---

## 9. 成功指标（KPI）

- 自动交付成功率 >= 98%
- 支付后 60 秒内发货率 >= 95%
- 人工介入率 <= 10%
- 客诉率持续下降

---

## 10. 你可以直接开始的第一步

今天就做这三件事：
1. 定义 3 个可售卖的“合规数字商品”
2. 建立 `orders + inventory_items` 两张表
3. 跑通“模拟支付成功 -> 自动分配 -> 自动发货（控制台打印）”

跑通闭环后，再接真实支付与消息通道。