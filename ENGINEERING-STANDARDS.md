# Engineering Standards

本文件定义跨项目的 canonical governance model。目标是：用最小必要治理成本保持正确性、安全、可恢复性和可追溯性。

> **Default Lean. Escalate by risk.**

具体项目可以增加更严格的项目级约束，但不应把高风险流程机械下放到所有普通任务。`RESTRICTED-CONTENT-STANDARD.md` 对 **ACTIVE SURFACE** 的 hard gate 不可被项目级规则放宽。

## 1. 决策与责任边界

- **Project Manager Role（PM）**：技术方案、任务拆分、执行路径、Agent/backend routing、验证深度、Review、merge gate。
- **Owner**：产品目标、优先级、风险接受、不可逆业务选择和其它明确保留的业务决策。
- Owner 默认不承担 Task ID、Agent 分配、Git/worktree、Reviewer 编排、测试方案或工程验收。
- 多个技术方案都可行时，由 PM 基于质量、风险、依赖、成本和可恢复性作出工程决定；只有真正超出授权边界时才升级 Owner。

### 1.1 Owner-facing Communication Closure

substantive Owner-facing reply 在存在行动歧义时必须回答三件事：

1. 当前是 PM 结论，还是需要 Owner 决策；
2. Owner 现在是否需要行动；如需要，只提出最小明确决策；
3. 下一步由谁负责。

如果信息足够，PM 应明确说明 Owner 无需操作并自动继续，不制造“请确认我再继续”的伪 gate。内部 Task ID、SHA、branch、tests、Evidence Package 等作为 supporting evidence 按需展开，不替代 Owner-facing 结论。

本节是 Owner-facing Communication Closure 的 canonical 定义。

## 2. Governance cost 与反官僚原则

Governance has a cost。一个流程步骤只有在它降低**有意义的工程风险**时才合理。

> If removing a governance step does not materially reduce correctness, recoverability, safety or traceability, the lighter process SHOULD be preferred.

禁止因为“规范里有这个流程”就无视任务风险机械执行。特别禁止：

- 为普通小修复机械创建 Task → Issue → Handoff → Reviewer → Cleanup Task → Second Review 链条；
- 为了使用一个模型而建立不必要的长期组织结构；
- 为 docs-only / formatting / trivial config 改动运行与风险不相称的完整回归；
- 为了记录一次性执行细节反复同步 README / ROADMAP / HANDOFF / Project Memory；
- 创建一个 governance task 去管理另一个 governance task，除非存在具体的恢复、并行、审计或高风险需要。

## 3. Project Profile：Lean 是默认值

Project Profile 只提供默认治理假设，不是新的状态机，也不要求项目登记 profile。

### Lean Project（默认）

典型特征：single repo、PM + 0–1 Writer、最多临时 Reviewer、低外部 blast radius、无 production migration、无受监管发布流程。

Lean Project 默认：

- 不要求完整 multi-Agent organization；
- 不要求拟人化工程师名字；
- 不要求每个任务有 Issue / Task ID；
- 不要求每个 PR 有独立 Reviewer；
- 不要求每次 project-memory refresh；
- 不要求每轮更新完整 HANDOFF；
- 不要求全 repo historical scan；
- 单 Writer 串行工作不要求额外 worktree；
- docs-only 不跑完整 regression suite。

### Persistent higher-risk project

只有项目长期具有明显更高风险（例如生产迁移、强发布控制、长期 multi-repo / multi-writer、敏感数据、安全关键系统）时，才应把更强约束作为项目默认。

无论项目 profile 如何，**task-level risk 可以单项升级**。一个 Lean Project 中的 migration 仍走 High-risk Path；高风险项目中的纯 typo 也可以走 Fast Path。

## 4. Task execution depth（canonical）

任务执行深度只有一个原则：

> **Use the lightest process that preserves correctness, recoverability, safety and traceability.**

### 4.1 Fast Path

适用于小、明确、低风险、可逆、低 blast radius 的一次性工作，例如 docs fix、small bug、small config、small refactor、formatting、simple test fix。

默认流程：

`Prompt / PM instruction → Execute → Verify → Commit/PR（需要时）→ PM Review`

默认**不要求**：

- Issue；
- Task ID；
- 完整 lifecycle 状态机；
- 独立 Reviewer；
- 完整人工 handoff；
- 单 Writer 的额外 worktree；
- Evidence Package 大清单。

Fast Prompt 只需任务特有信息：目标、必要事实/base、scope、验证、交付方式、特殊风险。

### 4.2 Standard Path

用于普通工程任务：有一定实现范围或恢复价值，但不触及高风险边界。

默认：

- one Writer；
- Task ID **仅在达到 `TASK-LIFECYCLE-STANDARD.md` §1 门槛时**使用；
- branch / PR 按项目习惯和追踪价值使用；
- targeted validation；
- PM 确认 PR head / diff / applicable tests 后 Review；
- Issue、完整 handoff、独立 Reviewer、worktree 仅在确有价值时增加。

### 4.3 High-risk / Deep Path

以下类型通常升级：

- architecture / Contract / API / schema；
- release / tag / immutable baseline；
- migration / production destructive operation；
- security-sensitive change；
- core runtime / high blast-radius refactor；
- cross-repo integration；
- multi-Agent fan-in；
- history rewrite / force push；
- complex recovery；
- 失败模式正在扩大 scope 的 debugging。

High-risk Path 可使用：Task ID、详细 Task Contract、独立 branch/worktree、Restatement、broader regression、exact-SHA Review、独立 read-only Reviewer、pre-authorization、recovery plan。

### 4.4 不随路径降低的硬边界

任何路径均保留：

- secrets / credentials 不入库；
- customer / production sensitive data protection；
- destructive / irreversible action pre-authorization；
- one Writer per shared mutable area；
- concurrent Writers isolation；
- architecture / Contract major changes 深 Review；
- GitHub durable truth；
- meaningful recovery capability；
- Agent 不得自行宣布最终验收；
- Owner 保留产品方向和不可逆业务决策；
- restricted-content **ACTIVE SURFACE** hard gate。

## 5. Task identity

Task ID threshold 的唯一 canonical 定义在 `TASK-LIFECYCLE-STANDARD.md` §1。

“派给 Agent”本身**不是** Task ID trigger。未达到门槛的 Fast Path 可以没有 Task ID；达到门槛后，Task ID 才作为跨会话恢复和幂等键贯穿 branch / PR / Review。

## 6. Scope control

每个任务都必须有足够清楚的目标与边界，但格式随路径缩放。

- Fast：一句目标 + 变更边界 + 验证即可。
- Standard / High-risk：补充必要事实、依赖、禁止范围、验收、恢复要求。

禁止未授权的顺手重构、升级或范围扩张。发现额外问题时，先判断是否阻塞当前任务；不阻塞则留给独立后续工作。

## 7. Concurrency 与 ownership

并行只在有真实收益且边界独立时使用。

- 同一共享可变区域只有一个 Writer。
- 并行 Writers 必须隔离 branch/worktree 或其它等价工作区。
- Reviewer 默认只读；临时 Reviewer 不需要因此加入长期 roster。
- 单 Writer 串行小任务不因为“规范要求”额外创建 worktree。
- A 的输出是 B 的输入时默认串行。

## 8. GitHub truth 与 handoff

GitHub remote 是 durable engineering truth。GitHub-native handoff 的 canonical 规则见 `PROMPT-HANDOFF-STANDARD.md`。

Owner 默认不搬运长篇技术 handoff。PM 能读取 remote 时，应自行读取 PR / branch / diff / tests / CI / exact SHA（适用时）。

## 9. Restricted Content

Restricted-content 的唯一 canonical 定义在 `RESTRICTED-CONTENT-STANDARD.md`。

当前 Review 必须区分：

- `ACTIVE_SURFACES: PASS | FAIL`
- `LEGACY_EVIDENCE_DISPOSITION: COMPLETE | INCOMPLETE`

普通历史 immutable evidence 命中不再永久阻塞当前项目 PASS；但任何**新产生或当前可控 Active Surface** 的命中仍必须修正。

## 10. Definition of Done 与 scope-appropriate testing

DoD 只要求**适用项**：

- 目标与验收满足；
- diff/scope 可解释，无未授权变化；
- GitHub 上可定位最终变更；
- 适用的验证通过；
- Active Surface restricted-content gate 通过；
- 高风险边界完成相应 Review / pre-authorization；
- 施工方式符合 `LOCAL-WORKSPACE-STANDARD.md`。

### 10.1 Testing / validation depth（canonical）

测试强度由 change risk 决定，不以“跑得越多越规范”为目标：

- **docs-only**：diff / format / link / content consistency；没有 runtime 影响时不跑完整 regression。
- **small code change**：targeted tests + 与改动直接相关的 lint/typecheck/build。
- **core / high-risk**：full relevant regression + boundary checks。
- **release**：release-grade verification、artifact/version/source-of-truth 检查。

项目已有强制 CI 时可以继续运行，但 PM 不应额外制造与变更无关的人工回归仪式。

### 10.2 Evidence depth

Fast Path 只需要足以 Review 的证据；Standard 按需增加；High-risk 才要求完整 Evidence Package。

Evidence 是 Review 输入，不是 Agent self-approval。

## 11. Review depth（canonical）

PM 始终保留最终 `PASS / HOLD / NEEDS_CORRECTION` 权限。

- **普通小 PR**：确认当前 PR head、diff、scope 和 applicable validation 即可。
- **Standard**：diff + targeted tests + 必要回归。
- **High-risk**：exact-SHA Review + broader regression + boundary verification；必要时增加独立 read-only Reviewer。

exact-SHA 深 Review 主要用于 Contract、API/schema、release、core runtime、高风险 refactor、cross-repo integration、migration、multi-Agent fan-in 和 security-sensitive changes。

不要求普通 docs / small bug 默认经历 Writer → Reviewer → second Reviewer → PM。

## 12. Risk escalation

### 12.1 Debugging Circuit Breaker

当重复修复失败、root-cause confidence 下降、scope 扩大、触碰 Architecture/Contract/core boundary、测试破坏扩大或验证不支持当前方向时，Writer STOP 并把证据交给 PM。无固定失败次数阈值。

### 12.2 Irreversible / high blast-radius pre-authorization

force push、history rewrite、删除重要 tag/release、生产数据删除、DROP/destructive schema、destructive migration、大规模不可逆删除等，执行前必须有明确 plan、blast-radius 判断和 pre-authorization。

技术层面可逆性判断由 PM 负责；涉及真实生产数据重大影响、法律/安全删除、Owner 保留业务决定或明确物理历史清除时，必须升级 Owner。

## 13. Durable Project Memory（canonical）

Project Memory 只记录**未来会改变工程判断的 durable truth**：

- architecture decision；
- milestone / project stage；
- release；
- canonical Contract；
- durable roster identity change；
- major recovery fact；
- 其它会影响未来恢复或技术决策的长期事实。

以下默认由 GitHub Issue / PR / commit history 承载，不要求同步 Project Memory / README / HANDOFF / ROADMAP / CHANGELOG：

- ordinary bugfix；
- ordinary docs fix；
- correction round；
- temporary Reviewer；
- temporary backend switch；
- ordinary refactor；
- 一次性执行日志。

老项目不因本规则立即批量迁移。下一次自然维护相关 canonical state 时再逐步收敛。

## 14. Canonical-definition discipline

同一规则只能有一个完整 canonical definition。其它文件只保留入口、触发条件和 cross-reference，不复制整套规则。

当前 canonical owners：

- governance depth / Lean Project / testing / Review / Project Memory：本文件；
- Task ID threshold：`TASK-LIFECYCLE-STANDARD.md`；
- roster identity：`AGENT-OPERATING-MODEL.md`；
- Prompt / handoff：`PROMPT-HANDOFF-STANDARD.md`；
- local / SAFE_REMOTE_FIRST：`LOCAL-WORKSPACE-STANDARD.md`；
- Git mechanics：`GIT-GITHUB-STANDARD.md`；
- restricted active/legacy gate：`RESTRICTED-CONTENT-STANDARD.md`。
