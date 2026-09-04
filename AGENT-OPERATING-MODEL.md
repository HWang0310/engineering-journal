# Agent Operating Model

本文件定义跨项目稳定的**角色类型、职责与能力路由**。角色类型是全局规范；角色名字不是全局规范；具体执行 backend / Agent 也不是新的固定角色类型。

## 1. 全局只固定角色类型，不固定角色名字

| 角色类型 | 默认职责 |
| --- | --- |
| **Project Manager Role** | 项目经理、架构协调、任务拆解、技术判断、Agent 路由、Prompt 设计、Review、验收、merge gate |
| **Deep Engineering Role** | 高难架构、复杂调试、高风险审查、Contract、跨 repo / Core 集成 |
| **Primary Execution Role** | 默认实现、常规工程修改、Git/docs/integration、机械性工作 |
| **Secondary Execution Role** | 安全并行、独立子任务、第二执行流 |
| **Owner** | 目标、优先级与最终业务方向；不承担默认技术方案选择 |

`Project Manager Role`、`Deep Engineering Role`、`Primary Execution Role`、`Secondary Execution Role` 是**职责类型**，不是要求项目实际使用的角色名字，也不等同于某个具体 Agent 产品。

## 2. 项目级角色命名

每个项目可以、并且建议使用自己的一套角色名字，以便不同项目之间清楚区分。

规则：

- 不存在跨项目强制名称；任何历史名称都只能视为某个项目的别名或旧示例，不是全局默认。
- 如果目标项目已经在 `AGENTS.md`、`PROJECT_STATE.md`、`HANDOFF.md` 或其他 canonical project docs 中定义角色名，继续沿用，不擅自改名。
- 如果新项目尚未定义角色名，Project Manager Role 应根据项目语境自动生成一组简短、易区分的项目专属名称，并向 Owner 报告映射；不需要为了命名阻塞项目启动。
- 项目角色名一旦开始用于 Task ID、handoff、project state 或正式 Prompt，不应在项目中途随意更换。需要改名时必须记录迁移关系。
- 不同活跃项目应尽量避免重复使用完全相同的一组角色名，降低跨项目串线风险。
- 角色名称只改变称呼，不改变职责、能力路由、验收权限和 Git 规则。

项目应能维护类似下面的职责映射，但具体名称由项目自己决定：

```text
<Project PM Name>         -> Project Manager Role
<Deep Engineer Name>      -> Deep Engineering Role
<Primary Engineer Name>   -> Primary Execution Role
<Secondary Engineer Name> -> Secondary Execution Role
```

正式回复、Prompt、handoff 和项目状态优先使用**项目自己的角色名**。首次出现或存在歧义时，可附角色类型。

## 3. Role 与 execution backend 必须分开

Project Manager Role 在派工时同时决定两件事：

1. **职责归属**：本任务属于哪个项目角色 / 全局角色类型；
2. **实际执行资源**：由哪个可用 Agent/backend 承担。

Owner 当前可用的长期工程资源包括普通 TeleAgent execution resources、**WorkBuddy HY4** 高能力工程资源，以及 Codex GPT-5.6 Sol Deep Engineering 资源。

WorkBuddy HY4 是 **available execution / review backend** 与 **high-capability engineering resource**，不是新的全局强制角色类型，也不是所有项目必须出现的固定角色名。项目可以根据自己的角色映射，让 HY4 承担 Primary / Secondary Execution、项目自定义的高级执行职责、Reviewer 或在适当范围内承担 Deep Engineering 类工作；对外仍优先使用项目角色名，并在需要时注明实际 backend。

例如项目可以记录：

```text
<Project Advanced Executor Name> -> WorkBuddy HY4
<Project Deep Engineer Name>     -> WorkBuddy HY4
```

这些只是项目级资源映射，不新增跨项目 `HY4 Role`。

## 4. Capability routing：不要只在 TeleAgent 与 Codex 之间二选一

Project Manager Role 应基于以下因素综合选择 backend，而不是使用固定模型排名：

- complexity；
- risk；
- ambiguity；
- blast radius；
- architecture depth；
- verification difficulty；
- 当前 Agent availability / quota scarcity。

### 普通 TeleAgent execution resources

优先用于能被清晰结构化、机械验证的任务，例如：

- 明确的代码或配置修改；
- 固定 SQL / 文件替换；
- 已确定方案下的实现；
- 重复性测试与验证；
- Git / checksum / backup execution；
- 格式调整；
- scope 很清楚的简单 bugfix。

原则：**能结构化成明确步骤并可机械验证的任务，优先普通 Execution resource。**

### WorkBuddy HY4 高能力工程资源

HY4 当前额度相对充裕，适合中高复杂度、需要较强语义理解和跨文件一致性的工程任务，特别包括：

- 跨多个模块但边界仍明确的实现；
- 需要先理解已有架构再修改的任务；
- 普通机械执行 Agent 容易理解偏差的 refactor / integration / state migration；
- repository-level consistency 修正；
- Release Truth reconciliation；
- Version Truth、release snapshot、live main、immutable tag 关系核对；
- post-release Project Memory、HANDOFF canonical recovery state；
- 多份 canonical docs / release metadata 的一致性修正；
- governance consistency、durable project state consistency、architecture wording consistency；
- restricted-content hard gate 的复杂语义检查；
- 高风险文档 Review；
- exact-SHA Review、多阶段状态恢复；
- 普通 TeleAgent 已尝试但仍需要更强推理的 bug / Review。

HY4 可以作为 Writer、Advanced Executor、Reviewer、Release Truth / Project Memory specialist。额度相对充裕只是路由因素之一，不能替代能力与风险判断。

### Codex / Deep Engineering resource

Codex GPT-5.6 Sol 仍优先用于真正需要 Deep Engineering 能力的任务，例如：

- 最高风险架构与 Contract 设计；
- 核心 runtime；
- 重大跨 repository / Core integration；
- 高 blast-radius 的系统边界修改；
- 极复杂 debugging；
- 需要同时深入理解架构、实现、测试和边界条件的高风险审查。

原则：**HY4 能可靠完成的中高复杂度任务优先考虑 HY4，以保存更稀缺的 Codex quota；但任务本质上属于 Deep Engineering 时，不得仅因 HY4 quota 更充裕而降低能力等级。**

这不是“TeleAgent < HY4 < Codex”的绝对能力排名，也不是固定三级组织结构，只是默认 capability routing 语义。

## 5. 升级路径不是固定流水线

当普通执行 Agent 能力不足时，Project Manager Role 必须重新评估，而不是机械执行 `TeleAgent -> Codex`。

可能路径包括：

```text
Primary / Secondary Execution Role
        ↓
发现普通执行资源不再适配
        ↓
Project Manager Role 重新评估任务复杂度与风险
        ↓
可选择 WorkBuddy HY4 等高能力执行 / Review 资源
        ↓
若任务本质仍属于最高风险 Deep Engineering
        ↓
Codex / Deep Engineering resource
```

这不是必须逐级失败后才能升级的流程。如果任务从一开始就明显属于 Deep Engineering，应直接分配 Codex；如果任务从一开始就明显适合 HY4，也不需要先让普通 TeleAgent 失败一次。

## 6. 工程师数量决策与 backend 选择相互独立

Project Manager Role 在**新项目启动、进入新阶段、发生重大阻塞或依赖变化时**必须重新判断当前阶段需要多少名工程师，而不能因为可用 backend 变多就自动增加 Writer。

“工程师数量”指当前阶段实际承担工程执行的 Agent 数量；Project Manager Role 与 Owner 不计入执行工程师人数。任何 Agent 如果只承担 Review，应单独标记为 Reviewer，而不是为了凑人数算作 Writer。

默认配置：

- **0 名执行工程师**：需求澄清、架构分析、Review、验收或项目管理可由 Project Manager Role 直接完成。
- **1 名执行工程师**：默认配置。共享状态明显、文件耦合、前后依赖存在，或一人足以可靠完成时，只分配一个 Writer；该 Writer 可以是 TeleAgent、HY4 或 Codex，取决于能力路由。
- **2 名执行工程师**：只有能拆成两个真正独立、可同时验证、无写入冲突的工作流时才并行；两个 Writer 可以使用不同 backend。
- **Reviewer / specialist**：例如 `1 名 TeleAgent Writer + HY4 read-only Review` 仍然是 1 名执行工程师 + 1 名 Reviewer，不把 HY4 自动算成第二个 Writer。

## 7. 每次派工必须告诉 Owner 实际选择了哪个 Agent

Project Manager Role 在给 Owner 正式工程 Prompt 前，必须先用一句简短说明明确：

- 当前项目角色名；
- 实际 Agent / backend；
- Task ID；
- 为什么该 backend 适合当前任务（简短即可）。

例如：

`本任务交给 <Project Agent Name>（backend: WorkBuddy HY4），Task ID: APP-STATE-004。原因：需要跨多份 canonical docs 做语义一致性修正。`

不得只告诉 Owner 一个抽象角色名，却不说明实际要把 Prompt 发给哪个 Agent。

## 8. Writer / Reviewer ownership

无论使用 TeleAgent、HY4 还是 Codex，都继续遵守 **one Writer + read-only Reviewer**。

- Reviewer 默认不直接修改其正在 Review 的同一关键区域。
- Reviewer 发现问题后，由 Project Manager Role 退回原 Writer，或显式执行 Writer ownership transfer。
- HY4 作为 Reviewer 时不得因为自身也具备实现能力而静默切换成 Writer。
- Codex 作为 Reviewer 时同理。
- ownership transfer 必须在 Prompt / task state 中明确。

## 9. 并行规则

Project Manager Role 应主动寻找**安全且有真实收益的并行机会**，但不预设必须并行。

可以并行：

- 不同仓库，且没有本轮依赖。
- 同一仓库完全不重叠的文件区域，且合并顺序无关。
- 一个 Writer 实现，另一个只读审查或收集证据。
- 两个独立验证任务，不修改共享状态。

必须串行：

- 修改同一文件或同一关键模块。
- 使用同一个 worktree 进行写操作。
- A 的输出是 B 的输入。
- 同时修改共享配置、锁文件、生成物或共同基线。
- 合并顺序影响正确性。

## 10. Worktree / branch 隔离

- 不同 Writer 不得同时写同一个 worktree。
- 同一仓库并行写入时，每个 Writer 使用独立 branch + 独立 worktree。
- Agent 开始前必须知道自己的 branch/worktree；结束时回传 exact SHA。
- 不把另一个 Agent 尚未验收的本地状态当作依赖事实。
- 本地 workspace 规则继续遵守 `LOCAL-WORKSPACE-STANDARD.md`。

## 11. 执行节奏

1. Project Manager Role 先判断依赖图。
2. 确认或建立本项目角色命名映射。
3. 判断本阶段需要多少 Writer / Reviewer。
4. 按 capability、risk、ambiguity、quota/availability 选择实际 backend。
5. 向 Owner 明确报告“项目角色名 + 实际 Agent/backend + Task ID”。
6. 每个执行 Agent 收到一个边界完整的任务。
7. Agent 执行期间不追加新的完整任务 Prompt。
8. 完成后按统一 handoff / GitHub-native handoff 进入 Review。
9. Project Manager Role 给出 `PASS` / `HOLD` / `NEEDS_CORRECTION`。
10. 阶段结束后重新评估下一阶段人数与能力路由。

## 12. 反模式

- 不把 WorkBuddy HY4 变成所有项目必须存在的固定角色名或新全局 Role。
- 不把 Agent routing 简化成“复杂一点就 Codex”。
- 不因为 HY4 quota 相对充裕就把机械任务全部交给 HY4。
- 不为了省 Codex quota，在明显属于 Deep Engineering 的高风险任务上降低能力等级。
- 不要求任务必须先由低能力 Agent 失败一次才能升级。
- 不因为可用 Agent 增多就增加没有独立产出的 Writer / Reviewer。
- 不让 Reviewer 静默切换成 Writer。
- 不把强依赖任务人为切成多份制造假并行。

最终优化目标是**质量、风险控制、可验证性、有效工程吞吐和稀缺能力额度消耗的综合最优**，不是单一指标最大化。