# 记忆系统修复复盘（2026-03-03）

## 背景问题
- 现象：会话很多，但“昨天聊了什么”经常召回失败。
- 根因拆解：
  1. `memory/2026-03-02.md` 缺失，导致关键日期断档。
  2. 记忆索引层未生效（`Provider: none`，`Indexed: 0 chunks`）。
  3. `sessions/*.jsonl` 是日志，不是默认长期记忆主数据源。

---

## 修复过程（按时间线）

### 1) 找回多 agent 架构与历史证据
- 校验蓝图：`config/multi-agent.blueprint.md`
- 校验实施文档：`jarvis-home/openclaw_multi_agent_architecture.md`
- 校验落地配置快照：
  - `jarvis-home/records/openclaw/openclaw.redacted.20260303-010351.json`
- 结论：最终架构为 `main + moltbook + trader + xhs + macro + ops`，且 `agentToAgent` 已启用。

### 2) 恢复 3/2 全量聊天记录
- 跨 `main/moltbook/trader/xhs/macro` 扫描 sessions。
- 导出全量时间线：
  - `jarvis-home/records/chat-2026-03-02-all.md`
- 其中确认 xhs MCP 链路测试发布成功（“连通性测试（可忽略）”）。

### 3) 回填缺失日记与长期记忆
- 新增：`memory/2026-03-02.md`（回填关键事实）
- 更新：`MEMORY.md`（写入多 agent 基线、关键配置路径、xhs 验证、决策变更）
- 作用：把“日志事实”变成“可检索记忆源”。

### 4) 修复 RAG 索引引擎
- 初始状态：`openclaw memory status --deep` 显示 `Provider: none`，索引 0。
- 试过本地 QMD 方案，因本机依赖兼容问题未稳定跑通，回滚。
- 改为 Gemini embedding 方案：
  - 在 `openclaw.json` 配置 `agents.defaults.memorySearch` 为 gemini
  - 模型从不可用值修正为 `gemini-embedding-001`
- 结果：
  - `Provider: gemini`
  - `Indexed: 14/14 files · 22 chunks`
  - `memory_search` 可命中 3/2 记录。

### 5) 提升长期可用性（防止再断档）
- 在 `AGENTS.md` 增加 **High-Coverage Memory Policy**：
  - 活跃时每 6–10 轮或 30–60 分钟落盘
  - 活跃日目标 ≥10 条有效记忆
  - 日终将稳定事实提炼入 `MEMORY.md`
- 在 `memory/2026-03-03.md` 记录该策略生效。

---

## 最终状态

### 现在已经成立
- 会话日志：`agents/*/sessions/*.jsonl`（审计/追溯）
- 记忆真源：`MEMORY.md` + `memory/*.md`
- 检索层：本地 `~/.openclaw/memory/*.sqlite`（向量+关键词混合检索）

### 关键认知
- **sessions 不是默认长期记忆库**。
- **要想被稳定召回，必须写入 memory 文件并索引**。
- 当前已具备“写入 -> 索引 -> 召回”的完整闭环。

---

## 后续维护建议
1. 每天持续高覆盖写 `memory/YYYY-MM-DD.md`。
2. 每 1–3 天把稳定事实合并到 `MEMORY.md`。
3. 周期检查：`openclaw memory status --agent main --deep`。
4. 若索引异常，优先检查 provider/model/key 有效性。

---

## 参考文件清单
- `config/multi-agent.blueprint.md`
- `jarvis-home/openclaw_multi_agent_architecture.md`
- `jarvis-home/records/openclaw/openclaw.redacted.20260303-010351.json`
- `jarvis-home/records/chat-2026-03-02-all.md`
- `MEMORY.md`
- `memory/2026-03-02.md`
- `memory/2026-03-03.md`
- `AGENTS.md`
