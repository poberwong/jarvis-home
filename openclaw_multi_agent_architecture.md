# OpenClaw 功能型多 Agent 架构与实现（Pober）

> 更新时间：2026-03-02
> 目标：前台保持单一入口（main/Jarvis），后台按功能拆分 agent，提升并行能力与边界清晰度。

## 1. 架构总览

前台：
- `main`（你直接对话的 Jarvis）

后台功能 Agent：
- `moltbook`：社区互动与巡检
- `trader`：交易建议与风险提示（不执行交易）
- `xhs`：小红书选题/文案/复盘
- `macro`：宏观与新闻情报
- `ops`：执行与监控（脚本/自动化）

核心原则：
- **主人格不变**：你一直对话的是 main
- **功能分工**：后台 agent 专注单一职责
- **最小权限**：每个 agent 只拥有必要能力

---

## 2. 目录与隔离

每个 agent 使用独立 workspace + agentDir：

- `~/.openclaw/workspace-moltbook`
- `~/.openclaw/workspace-trader`
- `~/.openclaw/workspace-xhs`
- `~/.openclaw/workspace-macro`
- `~/.openclaw/workspace-ops`

- `~/.openclaw/agents/moltbook/agent`
- `~/.openclaw/agents/trader/agent`
- `~/.openclaw/agents/xhs/agent`
- `~/.openclaw/agents/macro/agent`
- `~/.openclaw/agents/ops/agent`

这样可以做到：
- 会话隔离
- 技能/提示词按 agent 管理
- 降低串线风险

---

## 3. 关键配置点（openclaw.json）

### agents.list（功能 agent）
- 配置 5 个后台 agent（不再额外引入 coordinator）

### bindings
- iMessage / WebChat 均绑定回 `main`
- 保证你看到的是单入口统一体验

### session
- `session.dmScope = "main"`
- `session.mainKey = "main"`

作用：
- 私聊统一折叠到 `agent:main:main`
- 避免同一个人从 iMessage 又开一条独立 direct session

### tools.agentToAgent
- 当前默认 `enabled: false`
- 先稳定后再按需开放 agent 间直连

---

## 4. 已实施动作

1) 输出了多 agent 方案文档与模板（本地）
2) 创建了 5 个功能 agent workspace + agentDir
3) 将前台入口恢复为 `main`
4) 调整 session 策略为 DM 合并主会话
5) 清理了活跃 session，仅保留 `agent:main:main`

---

## 5. 运行建议

- 第一阶段：main + moltbook + trader
- 第二阶段：接入 xhs + macro
- 第三阶段：启用 ops 自动化与故障恢复

上线原则：
- 先人审，再自动化
- 先只读，再写入
- 先低风险，再高权限

---

## 6. 风险与边界

- `trader` 只给建议，不自动下单
- `ops` 默认不对外发言
- 对外发布动作（评论/发帖）建议保留可追溯日志
- 定期清理 sessions 索引，避免历史垃圾条目影响 UI

---

## 7. 后续可扩展

- 增加 `x-broadcast`（跨平台内容分发）
- 增加 `risk-guardian`（风控守门 agent）
- 增加统一评分系统（建议有效性、互动质量）

---

如果你想复用这套结构到其他项目，建议先复制本文件，再替换：
- 业务职能
- 绑定渠道
- 权限矩阵
- 自动化阈值
