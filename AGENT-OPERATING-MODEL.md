# Agent Operating Model

本文件定义跨项目稳定的**角色类型与职责**。角色类型是全局规范；角色名字不是全局规范。

## 1. 全局只固定角色类型，不固定角色名字

| 角色类型 | 默认实体 | 默认职责 |
| --- | --- | --- |
| **Project Manager Role** | ChatGPT | 项目经理、架构协调、任务拆解、技术判断、Agent 路由、Prompt 设计、Review、验收、merge gate |
| **Deep Engineering Role** | Codex GPT-5.6 Sol | 高难架构、复杂调试、高风险审查、Contract、跨 repo / Core 集成 |
| **Primary Execution Role** | 主 TeleAgent | 默认实现、常规工程修改、Git/docs/integration、机械性工作 |
| **Secondary Execution Role** | 第二 TeleAgent | 安全并行、独立子任务、第二执行流 |
| **Owner** | 用户 | 目标、优先级与最终业务方向；不承担默认技术方案选择 |

`Project Manager Role`、`Deep Engineering Role`、`Primary Execution Role`、`Secondary Execution Role` 是**职责类型**，不是要求项目实际使用的角色名字。

## 2. 项目级角色命名

每个项目可以、并且建议使用自己的一套角色名字，以便不同项目之间清楚区分。

规则：

- 不存在跨项目强制名称；任何历史名称都只能视为某个项目的别名或旧示例，不是全局默认。
- 如果目标项目已经在 `AGENTS.md`、`PROJECT_STATE.md`、`HANDOFF.md` 或其他 canonical project docs 中定义角色名，继续沿用，不擅自改名。
- 如果新项目尚未定义角色名，Project Manager Role 应根据项目语境自动生成一组简短、易区分的项目专属名称，并向 Owner 报告映射；不需要为了命名阻塞项目启动。
- 项目角色名一旦开始用于 Task ID、handoff、project state 或正式 Prompt，不应在项目中途随意更换。需要改名时必须记录迁移关系。
- 不同活跃项目应尽量避免重复使用完全相同的一组角色名，降低跨项目串线风险。
- 角色名称只改变称呼，不改变职责、能力路由、验收权限和 Git 规则。

项目应能维护类似下面的映射，但具体名称由项目自己决定：

```text
<Project PM Name>        -> Project Manager Role
<Deep Engineer Name>     -> Deep Engineering Role
<Primary Engineer Name>  -> Primary Execution Role
<Secondary Engineer Name>-> Secondary Execution Role
```

正式回复、Prompt、handoff 和项目状态优先使用**项目自己的角色名**。首次出现或存在歧义时，可附角色类型，例如：`<项目角色名>（Deep Engineering Role）`。

## 3. 默认能力路由

优先交给 Primary / Secondary Execution Role：

- 明确范围内的代码实现。
- 文档、Git、测试补充、配置、集成和重复性修改。
- 已有方案下的执行性工作。
- 可通过清晰验收标准验证的常规 bugfix。

升级给 Deep Engineering Role：

- 架构边界或 Contract 设计。
- 多仓库依赖与 Core integration。
- 高风险 runtime 修改。
- 长时间无法定位的复杂 bug。
- 需要从实现中识别系统性问题的审查。
- exact-SHA 高风险 Review / pin 前审查。
- 常规执行 Agent 已按规范尝试但仍无法可靠解决的问题。

Deep Engineering Role 是稀缺深水能力，不承担可以被结构化 Prompt 明确交给 TeleAgent 的机械工作；但任务真正需要时不能为了节省额度而回避使用。

## 4. 工程师数量决策

Project Manager Role 在**新项目启动、进入新阶段、发生重大阻塞或依赖变化时**必须重新判断当前阶段需要多少名工程师，而不能因为有多个 Agent 可用就默认全部派出。

“工程师数量”指当前阶段实际承担工程执行的 Agent 数量；Project Manager Role 与 Owner 不计入执行工程师人数。Deep Engineering Role 如果只承担 Review，应单独标记为 Reviewer，而不是为了凑人数算作 Writer。

默认配置：

- **0 名执行工程师**：需求澄清、架构分析、Review、验收或项目管理可由 Project Manager Role 直接完成。
- **1 名执行工程师**：默认配置。共享状态明显、文件耦合、前后依赖存在，或一人足以可靠完成时，只分配一个执行 Agent。
- **2 名执行工程师**：只有能拆成两个真正独立、可同时验证、无写入冲突的工作流时，才让 Primary + Secondary Execution Role 并行。
- **Deep Engineering Role 专项介入**：可以作为独立高难 Writer 或专项 Reviewer；不得自动当成第三个普通 Writer。

Project Manager Role 必须向 Owner 明示本阶段配置，并使用当前项目角色名，例如：

- `本阶段工程师配置：1 名执行工程师（<Primary Name>）。原因：当前任务有前后依赖，不适合并行。`
- `本阶段工程师配置：2 名执行工程师（<Primary Name> + <Secondary Name>）并行。原因：两个工作流真正独立。`
- `本阶段工程师配置：1 名执行工程师（<Primary Name>）+ <Deep Name> exact-SHA Review。`
- `本阶段工程师配置：0 名执行工程师。先由 <PM Name> 完成架构与任务拆解。`

如果配置变化，应说明原因，不静默增减 Agent。

## 5. 并行规则

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

共享关键区域采用 **one Writer + read-only Reviewer**。Reviewer 不直接修同一区域；发现问题后由 Project Manager Role 决定交回原 Writer 修正或显式交接 Writer。

## 6. Worktree / branch 隔离

- 不同 Writer 不得同时写同一个 worktree。
- 同一仓库并行写入时，每个 Writer 使用独立 branch + 独立 worktree。
- Agent 开始前必须知道自己的 branch/worktree；结束时回传 exact SHA。
- 不把另一个 Agent 尚未验收的本地状态当作依赖事实。

## 7. 执行节奏

1. Project Manager Role 先判断依赖图，而不是先决定并行数量。
2. 确认或建立本项目角色命名映射。
3. 明确本阶段工程师数量、项目角色名与是否需要 Deep Engineering Review。
4. 每个执行 Agent 收到一个边界完整的任务。
5. Agent 执行期间不追加新的完整任务 Prompt。
6. 完成后按统一 handoff / GitHub-native handoff 进入 Review。
7. Project Manager Role 给出 `PASS` / `HOLD` / `NEEDS_CORRECTION`。
8. 阶段结束后重新评估下一阶段的人数和能力路由。

## 8. 反模式

- 不把全局规范里的历史角色名机械复制到所有项目。
- 不因为两个 TeleAgent 都可用就固定同时派两人。
- 不把强依赖任务人为切成两份制造假并行。
- 不让两个 Writer 竞争同一事实源或同一关键文件。
- 不为了省 Deep Engineering 额度，在明显需要深水能力时强行堆更多低能力 Agent。
- 不为了显示项目“人员充足”增加没有独立产出的 Reviewer 或 Writer。

最终优化目标是**质量、可验证性、工程吞吐和高能力额度消耗的综合最优**，不是单一指标最大化。