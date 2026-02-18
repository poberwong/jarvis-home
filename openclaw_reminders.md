# OpenClaw 主动提醒机制（Cron / Heartbeat）

> 生成时间：2026-02-18 11:01

## 一、Cron 定时提醒（精确时间）
- Gateway 内置 Cron Scheduler，到点触发 agentTurn 或 systemEvent。
- 适合固定时间提醒、简报、日程触发。

常用命令：
```
openclaw cron list
openclaw cron add
openclaw cron update
openclaw cron remove
```

## 二、Heartbeat 心跳提醒（轻量轮询）
- Gateway 定期触发心跳；Agent 读取 HEARTBEAT.md。
- 无事返回 HEARTBEAT_OK；有事才发送提醒。
- 适合多项检查合并、对时间不敏感的任务。

## 三、消息链路（统一出口）
```
Cron/Heartbeat → Agent → message 工具 → iMessage/WhatsApp/Telegram
```

## 四、排查要点
- Cron 不触发：cron list 看 enabled、runs；确认 model 允许。
- Heartbeat 不生效：HEARTBEAT.md 是否为空、是否夜间策略屏蔽。

## 五、当前任务列表（含禁用）
```json
{
  "musk-mirror-task": {
    "enabled": false,
    "schedule": "every 2h",
    "sessionTarget": "isolated"
  },
  "market-open-briefing": {
    "enabled": true,
    "schedule": "每天 22:00 (Asia/Shanghai)",
    "sessionTarget": "isolated",
    "model": "openai-codex/gpt-5.2-codex"
  },
  "morning-briefing": {
    "enabled": true,
    "schedule": "每天 08:00 (Asia/Shanghai)",
    "sessionTarget": "isolated",
    "model": "google-antigravity/gemini-3-pro-high"
  }
}
```
