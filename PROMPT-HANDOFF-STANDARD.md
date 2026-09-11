# Prompt & Handoff Standard

本文件定义 PM → Agent 的任务表达和 Agent → PM 的结果回传。Prompt/Handoff 的复杂度必须与 `ENGINEERING-STANDARDS.md` §4 的执行路径匹配。

## 1. Fast Prompt

Fast Path 不要求 12 项模板。足够清楚即可，通常只需要：

- **目标**：这轮完成什么；
- **必要事实/base**：只有会影响执行的事实；
- **scope**：改什么 / 不改什么；
- **验证**：如何证明完成；
- **交付**：commit/PR/文件输出（需要时）；
- **特殊风险**：只有实际存在时写。

通用 workspace / Git / lifecycle / STOP / handoff 规则默认引用当前 `engineering-journal`，不在每个 Prompt 里重复。

## 2. Standard / High-risk Prompt

只有风险和恢复价值增加时，才逐步加入：

- Task ID（达到门槛时）；
- branch/base/exact SHA；
- 幂等预检；
- allow/deny scope；
- acceptance criteria；
- validation commands；
- recovery / rollback；
- Writer / Reviewer ownership；
- Evidence Package；
- pre-authorization boundary。

High-risk Prompt 应低歧义，但不把规范全文复制进去。

## 3. Dispatch announcement

只有 Owner 需要转发 Prompt 时，PM 才需要明确告诉 Owner：

- 发给哪个现有 engineer / Agent；
- backend（如果这决定实际发送对象）；
- Task ID（有时）；
- 一句话目标。

backend 变化不等于人员变化；规则见 `AGENT-OPERATING-MODEL.md`。

PM 能直接调用工具或直接完成 Fast Path 时，不制造“先告诉 Owner → Owner 再转发”的额外 relay。

## 4. Evidence

Evidence 必须可复核，但按风险缩放：

- Fast：changed files + applicable validation + Git/PR reference（需要时）。
- Standard：targeted tests / diff / branch / PR / known risk。
- High-risk：完整 Evidence Package，包括 exact SHA、广回归、边界验证、CI/contract/data checks 等适用证据。

Agent 自述“完成/测试正常”不是 PM `PASS`。

## 5. GitHub-native handoff（canonical）

PM 能读取目标 GitHub 时，默认：

`Agent execute/verify → commit/push → PM read remote → Review`

Owner 不搬运长 handoff。

Fast Path 完成信号可以只是：

`已完成；PR=<ref>` 或 `已完成；branch=<ref>`。

有 Task ID 时可附 Task ID；无 Task ID 时不补造一个。

## 6. 什么时候需要完整 handoff

只在关键事实无法从 GitHub/交付物恢复时，例如：

- Agent 无法 push；
- 关键本地证据不在 remote；
- complex debugging circuit breaker；
- data/migration execution 需要现场 evidence；
- PM 明确要求。

完整 handoff 也只包含决策和恢复真正需要的内容，不作为固定格式仪式。

## 7. Correction

`NEEDS_CORRECTION` 默认修当前变更，不因为 correction round 自动创建新 Task/Issue/Reviewer。

只有修正本身已经成为独立目标或明显扩大风险边界时，才重新拆任务。

## 8. Context hygiene

- 同一 Agent 正在执行一个完整任务时，不再追加第二个完整任务。
- 关键上下文来自 repository / PR / Task ID（有时），不依赖“你应该记得”。
- 不把历史完整 Prompt 当作 durable project memory。

## 9. Session Rollover / Control-Plane Compaction（canonical）

长期 PM 会话可以在 accumulated execution history 已明显大于 current active state 时进行 session rollover。**没有固定 token、消息轮数或时间阈值。**

PM 应区分：

### CURRENT CONTROL-PLANE STATE

只保留继续当前项目真正需要的活跃事实，例如：

- current milestone / stage；
- live branch / PR / Task references；
- active lanes / ownership；
- unresolved blockers / decisions；
- current validation / merge state；
- immediate next action。

### SUPERSEDED / LEGACY EXECUTION HISTORY

包括已经失效、但仍应保留 audit / provenance / recovery 价值的历史，例如：

- merged / superseded SHA；
- 已收口 correction rounds；
- stale metrics；
- obsolete routing decisions；
- 已结束的 parallel lanes；
- 不再决定当前行动的旧 handoff。

这些历史继续保留在 GitHub durable history 中，但默认不继续携带到 PM active working context。

当 important milestone / PR 已 merge、多轮 correction 已收口、active lanes 明显减少，且 current state 已远小于 accumulated historical state 时，PM 可以：

1. 确认 durable truth 已存在于 GitHub；
2. 当现有 PR / Issue / Project Memory 不足以直接恢复时，生成或更新一个 compact recovery / session-transfer state；
3. 只记录 current active state、unresolved items 和 next action；
4. 结束旧会话；
5. 新 PM 按 `NEW-SESSION-BOOTSTRAP.md` 从 remote 恢复。

compact recovery / session-transfer state 是 **current GitHub facts 的恢复索引**，不是新的并行 truth，也不能覆盖 remote branch / PR / commit / current canonical project state。

不要为每个 milestone 强制 rollover；不要设置固定 token / turn threshold；如果当前会话仍清晰、current state 没有被历史噪声淹没，可以继续使用。