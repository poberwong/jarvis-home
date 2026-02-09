# OpenClaw Guide (OpenClaw 指南)

This document provides a comprehensive overview of OpenClaw's core capabilities, system configuration, and PoberWong's personalized AI assistant setup.

本文全面介绍了 OpenClaw 的核心能力、系统配置以及 PoberWong 的个性化 AI 助手配置。

---

## 1. Core Capabilities & Structure (核心能力与结构)

OpenClaw is an agentic AI runtime that lives on your local machine, giving it direct access to your tools, files, and environment.

OpenClaw 是运行在您本地机器上的智能 AI 运行时，能够直接访问您的工具、文件和环境。

### 📂 The `.openclaw/` Directory (`.openclaw/` 目录)
This hidden directory in your home folder is the "brain" and "workspace" of the agent.

这个隐藏目录是 AI 代理的“大脑”和“工作区”。

*   **`workspace/`**: The primary working directory where the agent reads/writes files (like this document).
    *   **`workspace/`**: 代理读写文件的主要工作目录（就像这份文档）。
    *   **`AGENTS.md`**: The system prompt and identity file. (系统提示词和身份设定文件)
    *   **`MEMORY.md`**: Long-term memory storage (user preferences, facts, learnings). (长期记忆存储：偏好、事实、经验)
    *   **`USER.md`**: Specific details about the user (identity, schedule, habits). (用户特定信息：身份、日程、习惯)
    *   **`memory/`**: Folder for granular logs and state files (e.g., `presence-state.json` for home detection). (详细日志和状态文件夹，如回家检测)
*   **`skills/`**: A directory where custom skills (tools) are installed.
    *   **`skills/`**: 自定义技能（工具）的安装目录。
    *   **`feishu-bridge/`**: Custom integration to connect a Feishu bot. (连接飞书机器人的自定义集成)
    *   **`feishu-message/`**: Utilities for sending complex messages (audio, group chats). (发送复杂消息的工具)
*   **`media/`**: Storage for generated assets like screenshots, audio files, or images.
    *   **`media/`**: 存储生成的资源，如截图、音频或图片。

### 🛠️ Key Built-in Skills (核心内置技能)
*   **Browser Control (`browser`)**: Can launch a headless/visible browser to take screenshots, scrape data, or automate web tasks (e.g., generating a stock dashboard).
    *   **浏览器控制 (`browser`)**：启动无头或可视浏览器进行截图、爬取数据或自动化 Web 任务（如生成股票仪表盘）。
*   **System Execution (`exec`)**: Runs shell commands directly (e.g., `ping`, `curl`, `git`).
    *   **系统执行 (`exec`)**：直接运行 Shell 命令（如 `ping`, `curl`, `git`）。
*   **Network Scanning**: Can scan local network (ARP/Ping) to detect devices (used for "Welcome Home" feature).
    *   **网络扫描**：扫描局域网（ARP/Ping）检测设备（用于“欢迎回家”功能）。
*   **Cron Scheduler (`cron`)**: Manages timed tasks.
    *   **Cron 调度器 (`cron`)**：管理定时任务。
    *   *Isolated Mode*: Runs tasks in a separate, lightweight process (sub-agent) to avoid interrupting main chat. (隔离模式：在独立进程中运行，不打断主聊天)
    *   *Model Override*: Assigns cheaper models (e.g., Gemini Flash) to simple monitoring tasks. (模型覆盖：为简单监控任务分配更便宜的模型)

---

## 2. Personalized Configuration (PoberWong's Setup)
(PoberWong 的个性化配置)

This agent has been highly customized to fit PoberWong's daily routine and preferences.

该代理已根据 PoberWong 的日常习惯和偏好进行了高度定制。

### 🗣️ Communication Style (TTS & Text) (沟通风格：双语输出)
*   **Voice Out + Text**: Every response is delivered **twice**:
    *   **语音 + 文字**：每一次回复都包含两种形式：
    1.  **Audio**: Spoken aloud via the system's TTS (`say` command). (通过系统 TTS 朗读)
    2.  **Text**: Sent simultaneously to iMessage (`poberwong@gmail.com`) or the current chat interface. (同步发送文字到 iMessage 或当前聊天窗口)
*   **Reasoning**: Ensures accessibility and redundancy (hear it if nearby, read it if away).
    *   **理由**：确保可达性和冗余（在附近时听语音，离开时看文字）。

### 🏠 "Welcome Home" Automation ("欢迎回家" 自动化)
*   **Goal**: Detect when Pober returns home from work.
    *   **目标**：检测 Pober 何时下班回家。
*   **Mechanism**:
    *   **机制**：
    *   Runs a cron job every 10 minutes between **20:30 and 23:00**. (每晚 20:30-23:00 每 10 分钟运行一次)
    *   Scans the local network for a specific **iPhone MAC address** (`ee:a9:4a:2e:3b:6e`). (扫描局域网特定的 iPhone MAC 地址)
    *   Uses a lightweight model (`gemini-3-flash`) to save costs. (使用轻量级模型 `gemini-3-flash` 节省成本)
*   **Action**: If detected after being away, triggers a warm "Welcome Home" greeting via audio and text.
    *   **动作**：如果发现用户归来，触发温馨的语音和文字问候。

### 📰 Daily Briefings (每日简报)
*   **Morning (08:00)**:
    *   **早报 (08:00)**：
    *   Weather forecast (Beijing). (北京天气预报)
    *   News on **AI**, **Tesla**, **Gold**, **Google**. (AI, Tesla, 黄金, Google 的新闻)
    *   Uses high-intelligence model (`gemini-3-pro-high`) for synthesis. (使用高智商模型进行综合分析)
*   **Evening (22:00)**:
    *   **晚报 (22:00)**：
    *   Welcome home greeting. (欢迎回家问候)
    *   US Market Pre-market data (TSLA, Tech stocks). (美股盘前数据：Tesla, 科技股)
    *   Next day's weather. (次日天气)

### 💰 Investment Persona (投资人设)
*   **Identity**: Aggressive Growth / Tech-Heavy investor. (进取型/重仓科技投资者)
*   **Key Holdings**: **Tesla (1.25x Leveraged)**, Arm, Apple, Nvidia, Google. (核心持仓：Tesla 1.25倍杠杆, Arm, Apple, Nvidia, Google)
*   **Strategy**: "Barbell" approach (Stable Tech Giants + Speculative AI Bets). ("杠铃策略"：稳健科技巨头 + 投机性 AI 押注)
*   **Agent Role**: Acts as a financial assistant, monitoring portfolio health and market sentiment. (代理角色：作为财务助手，监控组合健康和市场情绪)

### 🤖 Model Strategy (模型策略)
*   **Dynamic Model Selection**:
    *   **动态模型选择**：
    *   **Complex Analysis** (Briefings, Coding): `gemini-3-pro-high` / `claude-opus`. (复杂分析：使用 Pro/Opus 模型)
    *   **Simple Monitoring** (Presence Check, GitHub Issue Monitor): `gemini-3-flash`. (简单监控：使用 Flash 模型)

---

## 3. Notable Projects & Integrations (主要项目与集成)

### 📱 Feishu (Lark) Bot (飞书机器人)
*   **Bridge**: A custom `feishu-bridge` skill connects a Feishu enterprise bot to OpenClaw via WebSocket.
    *   **桥接**：自定义 `feishu-bridge` 技能通过 WebSocket 连接飞书企业机器人。
*   **Capabilities**: Allows the agent to receive images and messages from Feishu, enabling visual recognition tasks (since iMessage currently restricts image reading).
    *   **能力**：允许代理接收飞书的图片和消息，从而具备视觉识别能力（弥补 iMessage 无法读取图片的限制）。

### 📊 Web App Generation (Web 应用生成)
*   **Antigravity Dashboard**: A generated HTML/JS dashboard visualizing real-time stock data (TSLA, Portfolio Value) and weather.
    *   **Antigravity 仪表盘**：生成的 HTML/JS 仪表盘，可视化实时股票数据（TSLA, 组合价值）和天气。
*   **Browser Automation**: The agent can spin up a local server, render the page in a headless browser, screenshot it, and send the image to the user.
    *   **浏览器自动化**：代理可以启动本地服务器，在无头浏览器中渲染页面，截图并发送给用户。

---

*Generated by OpenClaw (Antigravity) for PoberWong | 2026*
