# WordPocket 产品需求文档（PRD）

**版本**：1.1（细化版）  
**日期**：2026-02-18  
**状态**：Draft

---

## 📋 目录
1. [产品概述](#产品概述)
2. [用户研究](#用户研究)
3. [核心功能](#核心功能)
4. [技术架构](#技术架构)
5. [UI/UX 设计](#uiux-设计)
6. [开发路线图](#开发路线图)
7. [商业模式](#商业模式)
8. [成功指标](#成功指标)
9. [风险与挑战](#风险与挑战)
10. [附录](#附录)

---

## 产品概述
### 产品愿景
WordPocket 是一款面向中高级学习者的 **AI 驱动生词本**，让“阅读即收集、通勤即复习”成为英语能力提升的日常习惯。

### 核心价值主张
**“极低成本积累生词 + 场景化复习提升”**
- ⚡ **一键入库**：阅读时划词即入库
- 🧠 **AI 自动分类**：按领域与场景组织词汇
- 📚 **上下文记忆**：保留原文 Context，形成语义记忆
- 🔁 **碎片复习**：通勤/休闲场景轻量回顾

### 解决的痛点
- 外文资料阅读中 **领域生词占比高**（约 25%）
- 翻译工具只解决当下理解，不形成长期词汇资产
- 缺少“可持续、低摩擦”的积累与复习机制

---

## 用户研究
### 目标用户画像
**核心用户：知识密集型阅读者**
```
年龄：28-40
职业：研发/金融/咨询/科研/产品经理
英语水平：CET-6 / IELTS 6.5+ / TOEFL 90+
典型场景：
- 阅读英文论文、行业报告、技术文档
- 跟进海外资讯与前沿动态
- 需要长期沉淀领域术语
```

### 用户需求优先级
| 需求 | 重要性 | MVP包含 |
|------|--------|---------|
| 划词一键入库 | ⭐⭐⭐⭐⭐ | ✅ |
| 自动分类（领域/场景） | ⭐⭐⭐⭐⭐ | ✅ |
| 上下文保留（Context） | ⭐⭐⭐⭐⭐ | ✅ |
| 生词卡片（音标/翻译/读音） | ⭐⭐⭐⭐ | ✅ |
| 多场景词库切换 | ⭐⭐⭐⭐ | ✅ |
| 复习提醒（艾宾浩斯） | ⭐⭐⭐ | v1.1 |
| 数据清洗/自定义 | ⭐⭐⭐ | v1.2 |

---

## 核心功能
### 1. 划词入库（浏览器插件）
**输入示例**：用户在阅读时划词 `diffusion` 点击“一键入库”

**输出结构**：
```
word: diffusion
source_url: https://example.com/paper
context: "... diffusion models have shown remarkable progress ..."
category: AI / ML
translation: 扩散
phonetic: /dɪˈfjuːʒən/
audio: [TTS]
```

**功能要点**：
- 自动保存 source URL
- 自动截取上下文段落（前后 1–2 句）
- 支持多语言（v2.0）

---

### 2. AI 自动分类与解释
**逻辑**：AI 读取上下文，输出领域分类 + 简明解释

**输出示例**：
```
分类：科技 / AI
解释：指一种生成式模型，通过逐步加噪再去噪来生成数据。
```

---

### 3. 生词卡片（手机端输出）
**结构**：
- 单词/短语
- 上下文原句
- 音标 + TTS 读音
- 翻译 + 解释
- 标签（领域/场景）

---

### 4. 场景词库切换
用户可按 **科技 / 人文 / 医学 / 生活** 等领域切换词库。

---

### 5. 复习系统（v1.1）
- 艾宾浩斯复习曲线提醒（1/3/7/14 天）
- 通勤时段推送“今日复习词”

---

## 技术架构
### 技术栈
**前端（移动端）**
```yaml
框架: Flutter 3.x
状态管理: Riverpod 2.x
本地存储: Hive / Drift
```

**浏览器插件**
```yaml
Chrome Extension (Manifest v3)
```

**后端服务**
```yaml
API: FastAPI / Node.js
数据库: PostgreSQL
缓存: Redis
对象存储: S3 / R2
```

**AI 能力**
```yaml
模型: GPT-4 Turbo / Claude 3.5 (备用)
TTS: ElevenLabs / Google TTS
```

---

### 系统架构图
```
┌──────────────┐
│ Browser Ext. │
└──────┬───────┘
       │
       ▼
┌────────────────┐   ┌────────────┐
│ API Gateway    │→→│ AI Service │
└──────┬─────────┘   └────────────┘
       │
       ▼
┌────────────────┐
│ PostgreSQL     │
└──────┬─────────┘
       ▼
┌────────────────┐
│ Mobile Client  │
└────────────────┘
```

---

### 数据库设计（核心表）
**words（生词表）**
```sql
CREATE TABLE words (
  id UUID PRIMARY KEY,
  user_id UUID NOT NULL,
  word TEXT NOT NULL,
  translation TEXT,
  phonetic TEXT,
  audio_url TEXT,
  category VARCHAR(50),
  source_url TEXT,
  context TEXT,
  created_at TIMESTAMP,
  updated_at TIMESTAMP
);
```

**reviews（复习记录）**
```sql
CREATE TABLE reviews (
  id UUID PRIMARY KEY,
  word_id UUID REFERENCES words(id),
  user_id UUID NOT NULL,
  review_count INT DEFAULT 0,
  last_reviewed_at TIMESTAMP,
  mastery_level INT DEFAULT 0
);
```

**user_preferences（用户偏好）**
```sql
CREATE TABLE user_preferences (
  user_id UUID PRIMARY KEY,
  preferred_categories JSONB,
  daily_review_limit INT DEFAULT 20
);
```

---

## UI/UX 设计
### 设计原则
1. **极简操作**：一键入库
2. **低负担复习**：卡片化 + 轻量提示
3. **场景可切换**：按领域快速筛选

### 关键页面
- 插件浮窗：划词后弹出“一键入库”
- 移动端首页：今日复习 + 场景切换
- 生词卡片：上下文 + 翻译 + TTS

---

## 开发路线图
### MVP (8周)
- 插件划词入库
- AI 自动分类
- 生词卡片
- 场景切换

### v1.1 (复习系统)
- 艾宾浩斯提醒
- 复习统计

### v1.2 (偏好清洗)
- 用户自定义分类
- 词条清洗与批量编辑

### v2.0
- 多语言支持
- 社区分享词库

---

## 商业模式
- **免费版**：每天 20 条入库 + 基础复习
- **Pro**：无限入库 + 高级分类 + 复习提醒
- **企业版**：团队词库 + 行业场景包

---

## 成功指标
- 北极星指标：每周有效复习次数
- 复习完成率 > 60%
- 7日留存 > 25%
- 付费转化 > 5%

---

## 风险与挑战
- **AI 误分类** → 支持手动纠正
- **采集合规** → 仅保存必要文本
- **冷启动不足** → 预置通用词库

---

## 附录
- 竞品：传统生词本、翻译软件、AI 阅读工具
