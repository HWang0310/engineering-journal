# Agent Operating Model

本文件定义跨项目稳定的角色类型、engineer identity、backend routing 与 staffing。目标是防止身份混乱，并让 PM 以最低必要成本获得可靠工程吞吐，而不是建立组织架构管理系统。

## 1. 全局角色类型

| Role | 默认职责 |
| --- | --- |
| **Project Manager Role** | 技术判断、拆解、routing、并行/串行调度、Review、merge gate、Owner-facing closure |
| **Deep Engineering Role** | 高风险架构、Contract、复杂调试、深 Review |
| **Execution Role** | 常规实现、测试、Git/docs/config |
| **Owner** | 产品目标、优先级、风险接受、不可逆业务决定 |

项目可以使用 Primary / Secondary 等更细 Execution Role，但不是所有项目必需。Role 是职责类型，不等于具体 engineer identity。

## 2. Engineer identity != backend（canonical）

**工程师身份**与**执行 backend / model**是两个不同概念。

- 真正的 identity add / remove / rename 属于人员变化，需要 Owner 决定。
- 同一个既有 engineer 改用另一个可用 backend/model，原则上属于 PM capability routing，**不自动构成新增人员**，不需要 Owner 逐次批准。
- backend mapping 是 operational metadata，PM 可以更新；只有 identity 本身发生增删改名时才进入 Owner personnel decision。
- backend 切换不得静默改变任务 ownership；谁是 Writer / Reviewer 仍需清楚。
- `Writer`、`Reviewer`、`Execution Role`、`Deep Engineering Role` 等是 role labels，**不是可用于实际 dispatch 的 engineer identity**。
- TeleAgent、Codex、GPT-5.6、GPT-6、WorkBuddy 等是 backend / execution surface，**不是 engineer identity**。
- 当前 backend 排序、成本/额度和 routing policy 不在本文件重复定义，统一读取 `BACKEND-CAPABILITY-CERTIFICATION.md`。

## 3. Project roster / named dispatch（canonical）

Roster 是解决跨会话身份漂移和派工归属的项目级事实。项目可以保持极简 roster，但**只要发生 Agent dispatch，就必须先解析当前项目自己的具体命名 engineer identity**。

### 3.1 Lean Project

Lean 不等于匿名派工。

- 如果 PM 自己直接分析、Review、调用工具或完成安全的小型工作，没有发生 Agent dispatch，可以没有 Writer。
- 如果 PM 要把任何工作交给 Agent/backend 执行，必须从当前项目 GitHub canonical roster 中选择一个**具体命名 engineer**。
- Lean Project 可以只维护一个最小 named roster，例如 PM + 1 named execution engineer；不要求完整 multi-Agent organization。
- 不得用泛化的 `Writer` / `Reviewer` / `Agent A` 代替实际派工对象。
- 不得因为 backend 叫 TeleAgent / Codex 就把 backend 名称当作工程师名字。

### 3.2 Canonical project roster source

任何会发生 Agent dispatch 的项目，都必须在**该项目自己的 GitHub durable state** 中维护 canonical roster source。形式可以很轻量，但至少能够恢复：

- engineer name / identity；
- project role；
- active / replaced state；
- 必要时的 ownership notes。

backend mapping 可以记录，但不是 identity 本身。

如果项目已有 named roster：

- 必须沿用现有名字；
- 不得因为新会话重新命名同一角色；
- rename / replacement history 只在实际发生时记录；
- 其它 README / HANDOFF / Issue 不复制完整 roster，避免多份 truth。

历史项目已有稳定 named identities 但尚未落 GitHub 时，应在**下一次需要 dispatch 之前**先恢复并 backfill canonical roster；不要求为了无关 Fast Path 批量迁移。

### 3.3 Project-scoped Engineer Identity Boundary（canonical）

Engineer identity 是 **project-scoped durable identity**。一个 named engineer 只有属于**当前正在管理的 project scope**，并存在于该项目的 canonical roster，才可以作为当前项目 engineer 参与 staffing / dispatch。

> **Engineer identity is project-scoped. A named engineer recorded in project A does not automatically become an engineer of project B, even when A is a dependency, plugin, subproject, parent project, or sibling project of B. Reading another project's repository or roster does not change the current project's staffing. A named engineer may only be treated as a current-project engineer when that identity exists in the current project's canonical roster, or when the Owner explicitly approves adding it.**

#### Project scope 先于 roster resolution

Repository boundary 与 project boundary 不是同一个概念。一个 project 可以显式包含多个 repositories，并共享一个 declared canonical roster；但**不能仅凭 repository dependency / plugin / parent-child / sibling / product-ecosystem 关系推断 roster 共享**。

因此在任何 dispatch 前必须先 resolve：

1. 当前正在管理的 `current_project`；
2. 当前项目 declared canonical roster source；
3. 该 roster 中 active engineer identities；
4. 当前任务的具体 `engineer_identity`；
5. 最后才选择 backend/profile 并 dispatch。

如果多个 repositories 确实属于同一个 project scope，必须由当前项目的 GitHub project-control facts 明确表达 shared project / shared canonical roster；没有这种明确声明时，默认按**独立 project scopes**处理。

#### 不自动继承 engineer identity 的来源

以下来源中的 named engineer **不得自动加入当前项目 roster**：

- dependency repository；
- plugin repository；
- 独立 subproject；
- sibling project；
- parent project；
- 当前 project-control scope 之外的其它独立 project/repository；
- 历史聊天、其它 ChatGPT session 或其它项目 handoff 中出现的 roster。

项目 A 依赖项目 B，不意味着 B 的 engineer 成为 A 的 engineer。PM / Agent 为了解决 A 的问题打开 B 的 repository、读取 B 的 `AGENTS.md` / project memory / roster，也不会改变 A 的 staffing。

#### Dispatch invariant

任何 Agent dispatch 前必须满足：

```text
current_project = resolved
current_project.canonical_roster = resolved
dispatch_agent_identity = specific named identity
dispatch_agent_identity in current_project.canonical_roster
dispatch_agent_identity.status = active
backend/profile = resolved separately
```

如果不满足：

- 不得用 generic `Writer` / `Reviewer` / `Agent` 作为替代派工对象；
- 不得把另一个 project 的 named engineer 借来当前项目使用；
- 不得因为在另一个 repo / project 中看到了该 identity 就直接派工；
- 不得通过 backend routing、模型切换或 temporary staffing 绕过 roster membership；
- PM 可以选择自己直接完成当前工作；
- 如果确实需要新增 durable engineer identity，必须由 Owner 明确批准，并先写入当前项目 canonical roster，再 dispatch。

#### Cross-project work

Cross-project work 必须区分：

- **current project engineer**：属于 `current_project.canonical_roster`；
- **external / dependency project engineer**：属于另一个 project scope，不因此成为 current project engineer。

当当前项目发现 dependency/plugin project 的问题时，推荐链路：

```text
Current Project PM
→ cross-project handoff / issue to External Project
→ External Project PM resolves its own roster and named staffing
→ External Project implements / verifies
→ handback result / PR / release fact
→ Current Project PM performs integration review
```

不得静默简化为：

```text
Current Project → External Project Named Engineer
```

除非当前上下文已经**明确切换到 external project scope**，并按该项目自己的 roster 派工；或者 Owner 已把该 engineer 正式加入 current project roster。

Temporary external specialist / reviewer 只有在作为明确 external collaborator 被识别时才能接受一次性工作；这不会让其自动成为 current project durable engineer，也不改变 canonical roster。

## 4. Capability routing

Backend 的当前能力阶梯、默认派工顺序、成本/额度策略和 empirical notes 由 `BACKEND-CAPABILITY-CERTIFICATION.md` 统一定义。本文件只定义 PM 如何使用这些资源。

PM routing 需要同时考虑：

- complexity；
- risk；
- ambiguity；
- blast radius；
- architecture depth；
- verification difficulty；
- availability / quota / cost；
- 当前 Owner routing policy；
- backend 历史实战 evidence。

Canonical behavior：

1. 先确定 current-project named engineer identity，再选择 backend/profile；
2. 不默认从最强 backend 开始；
3. 优先选择足以胜任且成本/额度更优的资源；
4. 当前 Owner policy 明确鼓励 **TeleAgent-first**：TeleAgent 能安全胜任时应优先使用；
5. 不足时再按 registry 中的 capability escalation ladder 升级；
6. 强 backend 可以只负责最难的 architecture/root-cause/Review，机械实现、tests、docs 等可以交给免费/高额度执行资源；
7. backend 选择可以在同一 engineer identity 下变化，不构成 roster change；
8. `RETIRED` backend 不得默认 routing。

不要因为“这个任务想用更强模型”就新增 project engineer；backend routing 也不得把另一个 project scope 的 named engineer 变成当前项目 engineer。

## 5. Staffing

PM 只启用当前阶段真正需要的人：

- **0 Writer**：PM 直接分析、Review 或做安全的小型管理/remote docs 操作，没有 Agent dispatch；
- **1 Writer**：默认工程实现配置，必须是当前项目 roster 中一个具体 active named engineer；
- **2+ Writers / Agents**：当工作流可安全隔离并存在真实并行收益时，每条 lane 都必须绑定当前项目具体 named engineer；
- **Reviewer / specialist**：按风险启用；只要发生实际 dispatch，同样必须明确具体 identity 或明确 external collaborator identity。

Owner 不需要每次决定“分配几个工程师”。并行度属于 PM 的执行优化，只要不改变 durable roster、不跨越 Owner 决策边界即可。

不要把“有更多 backend 可用”或“另一个依赖项目有更多 named engineers”理解为当前项目自动增加人员。

### 5.1 Parallel-agent dispatch（canonical）

> **Parallelism is a PM optimization, not a mandatory workflow.**

PM 可以串行，也可以 fan-out 多个 Agent。判断标准是：**并行收益是否大于协调成本，并且 ownership 能否安全隔离。**

适合并行的典型工作：

- 多个互不依赖的 bug / plugin / package；
- primary implementation 与独立 targeted tests；
- 多个 root-cause hypotheses / investigation streams；
- implementation 与 docs / migration notes；
- read-only review、security/recovery review、edge-case analysis；
- 强 backend 处理 hard subproblem，同时多个低成本 backend 承担机械实现/测试/文档。

典型形式：

```text
PM
├─ <project-agent-name-A>：primary implementation
├─ <project-agent-name-B>：tests / edge cases
├─ <project-agent-name-C>：independent investigation
└─ <project-agent-name-D>：hard subproblem / independent review
```

也允许：

```text
PM → <one current-project named agent> → PM Review
```

如果任务高度串行、强依赖前一步输出、或多个 Agent 会同时争用同一核心文件/Contract，则不要为了“并行”强行拆分。

#### Parallel safety invariant

并行不改变 ownership hard boundary：

- 同一 **shared mutable work area** 同时只能有一个 Writer；
- 多 Writer 必须按独立 repo、branch/worktree、明确 file/module ownership 或真正独立 subtask 隔离；
- Reviewer / investigator 默认只读，不得静默修改 Writer work area；
- 如果两个并行结果需要 fan-in，由 PM 决定集成顺序、解决冲突并做最终验证；
- 并行 Agent 都不得自行宣布项目最终 PASS。

免费/无限额度不是降低安全边界的理由；它只意味着 PM 可以在**适合并行的独立工作流**中更积极地使用合适 backend 提升吞吐。

## 6. Writer / Reviewer ownership

- 同一 shared mutable area 只有一个 Writer。
- Writer / Reviewer 是 role；实际派工必须绑定具体 named identity。
- Reviewer 默认只读；发现问题退回 Writer，或由 PM 明确 transfer ownership。
- temporary Reviewer 不因为一次任务自动变成新的 durable project identity；如果由当前项目已有 engineer 承担，则沿用其现有 identity。
- ownership transfer 是 task-level 状态，不等于 roster change。
- cross-project ownership transfer 必须同时明确 project scope；不能只转移人名而不说明当前管理的是哪个 project。
- 多 Agent fan-out 后的 integration ownership 默认回到 PM 或 PM 明确指定的 current-project named integration Writer。

## 7. PM final acceptance

无论单 Agent、串行、多 Agent 并行还是 specialist + implementer 组合，最终状态都由项目 PM 独立验收。

PM 至少需要根据任务风险检查适用证据：

- scope / diff；
- tests / validation；
- branch / head / recovery facts（如适用）；
- 并行结果之间是否冲突；
- 是否有 Agent 越权扩大 scope；
- hard boundaries 是否满足。

Agent 可以提交 `PASS_CANDIDATE` / completion evidence，但不能替代 PM 的最终 `PASS / NEEDS_CORRECTION / HOLD`。

## 8. Dispatch transparency

任何实际 dispatch 都应能明确回答四件事：

- 当前 project；
- 发给当前项目哪个具体 named engineer / Agent；
- 使用哪个 backend/profile；
- 当前目标是什么。

如果 Owner 需要手动把 Prompt 发给 Agent，PM 应把这四项直接告诉 Owner。Task ID 仅在达到门槛时说明。

如果 PM 能直接执行或直接调用工具，没有发生 Agent dispatch，则不为透明度机械增加一轮 Owner relay。

当 PM 使用多 Agent 并行时，只在对 Owner 有实际价值时说明并行分工；不要求 Owner 充当 Agent 之间的信息中继。

## 9. Anti-patterns

- backend switch = personnel change；
- backend 名称 = engineer identity；
- generic `Writer` / `Reviewer` / `Agent A` = 实际 dispatch identity；
- 从其它项目借用 named engineer；
- 每个 Task 创建新名字；
- 小项目为了形式维护四人以上角色表；
- 因 quota 机械降级真正 Deep Engineering；
- 因可用 Agent 多就制造假并行；
- 明明可安全并行却机械串行导致不必要等待；
- 多 Writer 无隔离地同时修改同一 shared mutable area；
- Reviewer 静默变 Writer；
- 为一次小任务要求 Owner 批准模型切换或 Agent 数量；
- 因读取 dependency/plugin repo 的 roster，把 external named engineer 当成 current project engineer；
- 用 backend routing 绕过 project-scoped roster membership；
- 因历史考试分数高就无视当前 Owner routing policy；
- 继续派发已标记 `RETIRED` 的 backend；
- 把最终验收交给执行 Agent 自己宣布。

最终目标：稳定 identity、清晰 project scope、具体 named dispatch、合适 capability、可控并行、低沟通成本和风险相称的工程吞吐。
