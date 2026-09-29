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

如果 Fast Path 被 dispatch 给 Agent，Prompt 必须绑定当前项目 canonical roster 中一个具体 named engineer；如果由 PM 直接完成，则不需要虚构 Agent identity。

### 1.5 Issue as durable contract, Prompt as execution delta

当 Agent 可以访问目标 GitHub 时：

> **Issue is the durable task contract; Prompt is the execution delta.**

Default execution Prompt 只携带 Agent 无法从 Issue / current project facts 可靠推导的内容：

- project;
- concrete named engineer;
- backend/profile（Owner 需要手动 relay 时）;
- Task ID（达到门槛时）;
- TASK_RISK;
- Issue / PR reference;
- fresh-base requirement;
- critical task-specific delta / safety boundary;
- stop / merge boundary;
- expected deliverable.

默认不把完整 Issue 复制进 Prompt。完整 self-contained Prompt 仅在 GitHub / Issue 访问不可用或当前执行面确实需要时使用。

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

无论 Fast / Standard / High-risk，只要发生 dispatch，`Writer` / `Reviewer` 等 role label 都不能代替具体 named engineer identity。

### 2.1 Prompt delivery integrity / Markdown boundary safety

当完整 Agent Prompt 以 Markdown fenced block 作为**单一可复制容器**交付时，外层 fence 是 Prompt 的 copy boundary，必须保持完整。

- Prompt 正文不得直接输出能够被 Markdown parser 识别为**当前 outer fence 结束边界**的 nested fence。
- Prompt 内需要说明 SQL、JSON、Python、shell、YAML、Mermaid、自定义语言标记或其它 fenced block 时，默认优先用自然语言描述内部代码块，而不真正嵌套同级 fence。
- 如果确实必须展示 literal fence syntax，应使用不会关闭 outer fence 的 Markdown-safe 结构，例如更长的 outer fence、不同 delimiter，或把 fence 写成文字/转义表达。
- 本规则约束的是 **copy integrity**，不是强制所有 Prompt 都使用 Markdown fence；如果产品提供 writing block、prompt editor、附件或原生 Copy 容器，可以使用该原生边界。
- Prompt 外的 PM 解释与 Prompt 本体应分离；不要把非 Prompt 说明意外混入可复制容器。
- 对长 Prompt，Markdown boundary 完整性属于交付质量，而不是纯排版问题。

**Anti-pattern：** outer Prompt 使用 three-backtick fence，正文中的 SQL/JSON 等示例又直接输出同级 three-backtick fenced block，导致内部 closing fence 被解释为 outer Prompt 的结束位置，后续正文跳出 copy boundary。

**Preferred pattern：** 保持一个连续 outer Prompt boundary；正文改为“在普通 SQL fenced block 中放入 `SELECT ...`”之类的自然语言说明，而不实际嵌套同级 fence。

输出可复制 Prompt 前做一次轻量 boundary self-check：

- outer copy boundary 只有预期的 opening / closing；
- Prompt 正文不存在能够提前关闭 outer boundary 的 fence；
- 全部 Prompt 内容仍位于预期容器内；
- 非 Prompt 的 PM 说明仍位于容器外；
- 一次复制可以获得完整 Prompt。


## 3. Dispatch announcement

任何实际 dispatch 都必须先符合 `AGENT-OPERATING-MODEL.md` 的 project-scoped named dispatch invariant。

如果 Owner 需要手动把 Prompt 发给某个 Agent，PM 必须明确告诉 Owner：

- 当前 project；
- 发给当前项目哪个**具体 named engineer / Agent**；
- backend/profile；
- Task ID（达到门槛时）；
- 一句话目标。

不得只说“交给 Writer”“交给 Reviewer”“交给 TeleAgent/Codex”。Role 和 backend 都不是 engineer identity。

如果当前项目没有可用的 canonical roster identity，PM 不能借用其它项目的名字，也不能用 generic Agent 顶替。此时只有两种合法路径：

- PM 自己直接完成当前工作；
- 确实需要新增 durable engineer 时，先按 `AGENT-OPERATING-MODEL.md` 走 Owner-approved roster add，再 dispatch。

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

### 5.1 Single active execution thread

同一个实现任务默认只维护一个 **active execution / review thread**，避免 Issue 与 PR 同时复制完整 handoff、correction 和 validation history。

- 尚无 implementation PR 时，Issue 可以承载 Task Contract、设计讨论、执行指令和当前 handoff；
- implementation PR 创建后，PR 默认成为该实现工作的 active execution / review thread；
- Issue 继续保留任务目标、why、scope、architecture / Owner decisions 等 durable task context，并可用简短状态或 PR reference 指向当前实现；
- Agent handoff、PM correction、correction result、current validation 默认留在 active PR，不再向 Issue 重复复制同一全文；
- design-only、multi-PR coordination、无 PR 任务或项目明确采用 issue-centric workflow 时，可以继续以 Issue 为 active thread。

本规则不删除历史，也不改变 GitHub durable truth；它只减少同一 current state 在多个 GitHub surface 上的重复传播。

### 5.2 Tiered handoff

Handoff 按风险分层，保持简洁：

- **FAST**: status + PR/commit reference + validation summary + blockers;
- **STANDARD**: add non-obvious validation, deviations / risks, unresolved items;
- **HIGH_RISK**: add exact head, boundary / security / data evidence, recovery / rollback, approval / local-only evidence when applicable.

PM 能查询 GitHub / CI 时不手动重复 branch / files / SHA / test counts 等 facts，除非这些 facts locally 不可得或 risk-relevant。

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

### 7.1 Correction delta handoff

同一 PR 的第二轮及后续 correction 默认使用 **delta handoff**，只报告相对上一轮已审状态的新事实。

建议包含：

- `PREVIOUS_REVIEWED_HEAD`（已知时）；
- `CURRENT_HEAD`；
- 本轮逐项解决了哪些 PM correction；
- `FILES_CHANGED_THIS_ROUND`；
- 本轮新增/变化的 validation evidence；
- 仍然不变但对安全/边界有必要确认的关键 boundary；
- blockers、status、next owner。

默认不要重复粘贴完整任务背景、前几轮 handoff、未变化的文件清单、未变化的长测试日志或已经由 GitHub durable history 保存的 correction transcript。

如果上一轮 reviewed head 不可确定、scope 已明显扩大、关键假设发生变化、出现 cross-cutting / high-risk 影响，PM 或 Agent 可以扩大 handoff 内容；delta handoff 不是隐藏变化的理由。

## 8. Context hygiene

- 同一 Agent 正在执行一个完整任务时，不再追加第二个完整任务。
- 关键上下文来自 repository / PR / Task ID（有时），不依赖“你应该记得”。
- 不把历史完整 Prompt 当作 durable project memory。
- dispatch identity 必须来自当前 project roster，不从其它项目会话或 handoff 中借名字。

- fresh recovery 的历史读取遵守 `NEW-SESSION-BOOTSTRAP.md` 的 History Traversal / Recovery Budget；旧 handoff、旧 correction transcript、superseded design 默认不是 active context。

### 8.1 Active correction / historical handoff recovery

- active PR 多轮 correction 后，fresh Reviewer 默认只恢复 current head、latest unresolved review / current handoff，以及 delta review 必需的 previous reviewed SHA；
- 已收口的早期 review/correction transcript 不重新全文加载，除非出现明确 `HISTORY_READ_TRIGGER`；
- merged PR 的 correction history 属于 durable audit trail；
- historical handoff / session-transfer 默认 provenance only；
- repository 中存在多个 handoff 时，必须通过 current project facts 明确哪个是 current canonical control-plane source。

完整 recovery traversal 规则只在 `NEW-SESSION-BOOTSTRAP.md` 定义，本文件不复制 trigger/budget 全表。

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
