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
- 新项目如果尚未定义角色映射，应在项目启动阶段**一次性建立初始 project engineer roster**：为实际参与该项目的工程师建立项目专属名字并向 Owner 报告映射。
- 初始 roster 一旦建立，项目工程师名字与身份默认固定；不得因为新任务、新阶段、backend routing 或任务复杂度变化就临时创建新的工程师名字。
- 只有 Owner 明确新增工程师、替换工程师或确认调整项目人员配置时，才允许增加、删除或替换 project engineer identity。
- 项目角色名一旦开始用于 Task ID、handoff、project state 或正式 Prompt，不应在项目中途随意更换。确需改名时必须记录旧名 → 新名迁移关系。
- 不同活跃项目应尽量避免重复使用完全相同的一组角色名，降低跨项目串线风险。
- 角色名称只改变称呼，不改变职责、能力路由、验收权限和 Git 规则。

项目应维护类似下面的职责映射，但具体名称由项目自己决定：

```text
<Project PM Name>         -> Project Manager Role
<Deep Engineer Name>      -> Deep Engineering Role
<Primary Engineer Name>   -> Primary Execution Role
<Secondary Engineer Name> -> Secondary Execution Role
```

正式回复、Prompt、handoff 和项目状态优先使用**项目自己的既有角色名**。首次出现或存在歧义时，可附角色类型。

## 3. Project roster 与 stage staffing 必须区分

**Project roster** 表示这个项目长期有哪些已登记工程师、各自叫什么、承担什么职责、对应哪个实际 Agent/backend。**Stage staffing** 表示当前阶段从既有 roster 中启用哪些 Writer / Reviewer。

因此：

- `0 / 1 / 2 名执行工程师` 描述的是当前阶段同时承担执行工作的 Writer 数量，不代表项目 roster 只有 0 / 1 / 2 人，也不代表要新建工程师身份。
- 新阶段优先从现有 project roster 中选人，不为每个 Task 创建新的命名工程师。
- 新 backend 变得可用，不等于项目自动增加一名新工程师。
- 如果当前 roster 中没有适合某项任务的资源，Project Manager Role 应先向 Owner 说明“建议新增/替换哪类工程师以及原因”；未经 Owner 确认，不得自行取新名字并直接派工。
- 临时 Reviewer / specialist 如果已经是 roster 中的工程师，可以按任务切换 Writer / Reviewer 身份，但 ownership 必须显式；如果其实际 Agent/backend 从未进入该项目 roster，则先按人员变更规则处理。

项目的 roster 变化属于 durable project state，重要项目应在 `PROJECT_STATE.md`、`AGENTS.md`、`HANDOFF.md` 或其他 canonical project doc 中维护当前映射，避免跨会话误创建重复身份。

## 4. Role 与 execution backend 必须分开

Project Manager Role 在派工时同时决定两件事：

1. **职责归属**：本任务属于哪个既有项目角色 / 全局角色类型；
2. **实际执行资源**：该项目工程师当前映射的哪个 Agent/backend 承担。

Owner 当前可用的长期工程资源包括普通 TeleAgent execution resources、**WorkBuddy HY4** 高能力工程资源，以及 Codex GPT-5.6 Sol Deep Engineering 资源。

WorkBuddy HY4 是 **available execution / review backend** 与 **high-capability engineering resource**，不是新的全局强制角色类型，也不是所有项目必须出现的固定角色名。对于新项目，可以在建立初始 roster 时根据项目需要把 HY4 映射给某个项目工程师；对于已有项目，如果 HY4 尚未在 roster 中，不得因为某个新 Task 适合 HY4 就临时造一个新项目工程师名字，必须先按 roster 变更规则处理。

例如项目可以在自己的 roster 中记录：

```text
<Project Advanced Executor Name> -> WorkBuddy HY4
<Project Deep Engineer Name>     -> WorkBuddy HY4
```

这些只是项目级资源映射，不新增跨项目 `HY4 Role`。

## 5. Capability routing：不要只在 TeleAgent 与 Codex 之间二选一

Project Manager Role 应基于以下因素综合选择 backend，而不是使用固定模型排名：

- complexity；
- risk；
- ambiguity；
- blast radius；
- architecture depth；
- verification difficulty；
- 当前 Agent availability / quota scarcity。

Capability routing 必须在**当前 project roster 的可用资源边界内**进行。若最适合的 backend 不在当前 roster，先向 Owner 提出 roster change，而不是静默增加一个工程师身份。

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

## 6. 升级路径不是固定流水线

当普通执行 Agent 能力不足时，Project Manager Role 必须重新评估，而不是机械执行 `TeleAgent -> Codex`。

可能路径包括：

```text
Primary / Secondary Execution Role
        ↓
发现普通执行资源不再适配
        ↓
Project Manager Role 重新评估任务复杂度与风险
        ↓
可选择当前 project roster 中的 WorkBuddy HY4 等高能力执行 / Review 资源
        ↓
若任务本质仍属于最高风险 Deep Engineering
        ↓
当前 roster 中的 Codex / Deep Engineering resource
```

这不是必须逐级失败后才能升级的流程。如果任务从一开始就明显属于 Deep Engineering，应直接分配项目中既有的 Deep Engineering engineer；如果任务从一开始就明显适合 HY4，也可以直接分配 roster 中已登记的 HY4 engineer。

如果所需 backend 尚未加入当前项目 roster，Project Manager Role 必须先向 Owner 提出人员调整建议，不能通过临时起名绕过 roster 稳定性规则。

## 7. 工程师数量决策与 roster 大小相互独立

Project Manager Role 在**新项目启动、进入新阶段、发生重大阻塞或依赖变化时**必须重新判断当前阶段需要多少名工程师参与本阶段，而不能因为可用 backend 变多就自动增加 Writer，也不能把阶段人数变化理解为 roster 增减。

“工程师数量”指当前阶段实际承担工程执行的 Agent 数量；Project Manager Role 与 Owner 不计入执行工程师人数。任何 Agent 如果只承担 Review，应单独标记为 Reviewer，而不是为了凑人数算作 Writer。

默认配置：

- **0 名执行工程师**：需求澄清、架构分析、Review、验收或项目管理可由 Project Manager Role 直接完成。
- **1 名执行工程师**：默认配置。共享状态明显、文件耦合、前后依赖存在，或一人足以可靠完成时，只从当前 roster 中分配一个 Writer。
- **2 名执行工程师**：只有能拆成两个真正独立、可同时验证、无写入冲突的工作流时，才从当前 roster 中并行两个 Writer。
- **Reviewer / specialist**：例如 `1 名既有 TeleAgent Writer + 1 名既有 HY4 read-only Reviewer` 仍然是 1 名执行工程师 + 1 名 Reviewer，不把 Reviewer 算作第二个 Writer，也不因此新建工程师身份。

## 8. 每次派工必须告诉 Owner 实际选择了哪个既有工程师

Project Manager Role 在给 Owner 正式工程 Prompt 前，必须先用一句简短说明明确：

- 当前项目中**已有的工程师名字**；
- 实际 Agent / backend；
- Task ID；
- 为什么该工程师/backend 适合当前任务（简短即可）。

例如：

`本任务交给 <Existing Project Engineer Name>（backend: WorkBuddy HY4），Task ID: APP-STATE-004。原因：需要跨多份 canonical docs 做语义一致性修正。`

不得只告诉 Owner 一个抽象角色名；也不得为了一个新 Task 临时造一个新的工程师名字。

## 9. Writer / Reviewer ownership

无论使用 TeleAgent、HY4 还是 Codex，都继续遵守 **one Writer + read-only Reviewer**。

- Reviewer 默认不直接修改其正在 Review 的同一关键区域。
- Reviewer 发现问题后，由 Project Manager Role 退回原 Writer，或显式执行 Writer ownership transfer。
- HY4 作为 Reviewer 时不得因为自身也具备实现能力而静默切换成 Writer。
- Codex 作为 Reviewer 时同理。
- ownership transfer 必须在 Prompt / task state 中明确。
- ownership transfer 只改变当前任务/区域的 Writer 归属，不自动新增 project engineer identity。

## 10. 并行规则

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

## 11. Worktree / branch 隔离

- 不同 Writer 不得同时写同一个 worktree。
- 同一仓库并行写入时，每个 Writer 使用独立 branch + 独立 worktree。
- Agent 开始前必须知道自己的 branch/worktree；结束时回传 exact SHA。
- 不把另一个 Agent 尚未验收的本地状态当作依赖事实。
- 本地 workspace 规则继续遵守 `LOCAL-WORKSPACE-STANDARD.md`。

## 12. 执行节奏

1. Project Manager Role 先判断依赖图。
2. 读取并确认当前 project engineer roster；只有新项目首次建立或 Owner 明确调整时才修改 roster。
3. 判断本阶段需要多少 Writer / Reviewer。
4. 在现有 roster 中按 capability、risk、ambiguity、quota/availability 选择实际工程师/backend。
5. 如果现有 roster 缺少必要能力，先向 Owner 提出新增/替换工程师建议，不临时造名字。
6. 向 Owner 明确报告“既有项目工程师名 + 实际 Agent/backend + Task ID”。
7. 每个执行 Agent 收到一个边界完整的任务。
8. Agent 执行期间不追加新的完整任务 Prompt。
9. 完成后按统一 handoff / GitHub-native handoff 进入 Review。
10. Project Manager Role 给出 `PASS` / `HOLD` / `NEEDS_CORRECTION`。
11. 阶段结束后重新评估下一阶段 staffing 与能力路由，但不因此自动改动 project roster。

## 13. 反模式

- 不把 WorkBuddy HY4 变成所有项目必须存在的固定角色名或新全局 Role。
- 不为每个 Task、每个新阶段或每次 backend routing 临时创造新的项目工程师名字。
- 不因为某个 backend 新可用就静默把它加入已有项目 roster。
- 不把 Agent routing 简化成“复杂一点就 Codex”。
- 不因为 HY4 quota 相对充裕就把机械任务全部交给 HY4。
- 不为了省 Codex quota，在明显属于 Deep Engineering 的高风险任务上降低能力等级。
- 不要求任务必须先由低能力 Agent 失败一次才能升级。
- 不因为可用 Agent 增多就增加没有独立产出的 Writer / Reviewer。
- 不让 Reviewer 静默切换成 Writer。
- 不把强依赖任务人为切成多份制造假并行。

最终优化目标是**稳定的项目人员认知、质量、风险控制、可验证性、有效工程吞吐和稀缺能力额度消耗的综合最优**，不是单一指标最大化。
