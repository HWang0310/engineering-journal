# Agent Operating Model

## 角色命名

| 名称 | 实体 | 默认职责 |
| --- | --- | --- |
| **Curator** | ChatGPT | 项目经理、架构协调、拆解、Prompt 设计、Review、验收、合并决策 |
| **Axiom** | Codex GPT-5.6 Sol | 高难架构、复杂调试、高风险审查、Contract、跨 repo / Core 集成 |
| **Mason** | TeleAgent 主力 | 默认实现、常规工程修改、Git/docs/integration、机械性工作 |
| **Rivet** | 第二个 TeleAgent | 与 Mason 并列的执行者，用于安全并行或独立子任务 |
| **Owner** | 用户 | 目标、优先级与最终业务方向；负责转发任务和结果，不承担默认技术方案选择 |

## 默认路由

优先级不是“谁更强就谁做”，而是“用足够的能力完成任务”。

### 优先交给 Mason / Rivet

- 明确范围内的代码实现。
- 文档、Git、测试补充、配置、集成和重复性修改。
- 已有方案下的执行性工作。
- 可通过清晰验收标准验证的常规 bugfix。

### 升级给 Axiom

- 架构边界或 Contract 设计。
- 多仓库依赖与 Core integration。
- 高风险 runtime 修改。
- 长时间无法定位的复杂 bug。
- 需要从实现中识别系统性问题的审查。
- exact-SHA 高风险 Review / pin 前审查。
- Mason / Rivet 已按规范尝试但仍无法可靠解决的问题。

Axiom 是稀缺深水能力，不承担可以被结构化 Prompt 明确交给 TeleAgent 的机械工作。

## 工程师数量决策

Curator 在**新项目启动、进入新阶段、发生重大阻塞或任务依赖发生变化时**，必须重新判断当前阶段需要多少名工程师，而不能默认“有两个 Agent 就用两个”。

“工程师数量”默认指当前阶段实际承担工程执行的 Agent 数量；Curator 与 Owner 不计入执行工程师人数。Axiom 如果只承担 Review，应单独标记为 Reviewer，而不是为了凑人数算作 Writer。

### 默认配置

- **0 名执行工程师**：当前只是需求澄清、架构分析、Review、验收或项目管理，Curator 可以直接完成，不需要派执行 Agent。
- **1 名执行工程师**：默认配置。任务存在共享状态、文件高度耦合、前后依赖明显，或一个 Agent 足以可靠完成时，只分配 Mason 或 Rivet 一人。
- **2 名执行工程师**：只有能拆成两个真正独立、可同时验证、无写入冲突的工作流时，才分配 Mason + Rivet 并行。
- **Axiom 专项介入**：深水任务需要时单独加入。可以是 `1 名执行工程师 + Axiom Review`、`2 名执行工程师 + Axiom Review`，也可以由 Axiom 独立承担某个高难实现；不得把 Axiom 自动当成第三个普通 Writer。

### Curator 必须向 Owner 明示

在准备派发本阶段任务前，Curator 应用一句简短、明确的话告诉 Owner 当前配置，例如：

- `本阶段工程师配置：1 名执行工程师（Mason）。原因：当前任务有前后依赖，不适合并行。`
- `本阶段工程师配置：2 名执行工程师（Mason + Rivet）并行。原因：前端与数据层完全独立、文件不重叠。`
- `本阶段工程师配置：1 名执行工程师（Mason）+ Axiom exact-SHA Review。原因：实现本身明确，但属于高风险核心链路。`
- `本阶段工程师配置：0 名执行工程师。先由 Curator 完成架构与任务拆解，再决定是否派工。`

如果工程师数量变化，Curator 应说明变化原因，而不是静默增加或减少 Agent。

### 反模式

- 不因为 Mason 与 Rivet 都可用就固定同时派两人。
- 不把一个强依赖任务人为切成两份制造“假并行”。
- 不让两个 Writer 竞争同一事实源或同一关键文件。
- 不为了省 Axiom 额度，在明显需要深水能力时强行堆更多 TeleAgent。
- 不为了显示项目“人员充足”而增加没有独立产出的 Reviewer 或 Writer。

## 双 TeleAgent 并行规则

Mason 与 Rivet 应尽量承担大部分工程量，并在**真正独立**时并行，而不是为了看起来快而并行。

### 可以并行

- 不同仓库，且没有本轮依赖。
- 同一仓库中完全不重叠的文件区域，并且最终合并顺序无关。
- 一个 Writer 实现，另一个只读审查或收集证据。
- 两个独立验证任务，不修改共享状态。

### 必须串行

- 修改同一文件或同一关键模块。
- 使用同一个 worktree 进行写操作。
- A 的输出是 B 的输入。
- 两个任务会同时修改共享配置、锁文件、生成物或共同基线。
- 合并顺序会影响正确性。

### 共享关键区域

采用 **one Writer + read-only Reviewer**。Reviewer 不直接修同一区域；发现问题后由 Curator 决定交回原 Writer 修正，或显式完成 Writer 交接。

## Worktree / branch 隔离

- 不同 Writer 不得同时写同一个 worktree。
- 同一仓库并行写入时，每个 Writer 使用独立 branch + 独立 worktree。
- Agent 开始前要知道自己负责的 branch/worktree；结束时回传 exact SHA。
- 不允许把另一个 Agent 尚未验收的本地状态当作依赖事实。

## 执行节奏

1. Curator 先判断依赖图，而不是先决定并行数量。
2. Curator 明确本阶段工程师数量、角色与是否需要 Axiom Review。
3. 每个执行 Agent 收到一个边界完整的任务。
4. Agent 执行期间不追加新的完整任务 Prompt。
5. 完成后按统一 handoff 回传。
6. Curator 对结果标记 `PASS` / `HOLD` / `NEEDS_CORRECTION`。
7. 阶段结束后重新评估下一阶段的工程师数量与能力路由。

## 质量与额度

- Mason + Rivet：承担大多数可结构化的工程工作。
- Axiom：能歇则歇，但不能为了省额度让低能力 Agent 在高风险区域硬撑。
- Curator：应尽量通过更好的拆解、Prompt、验收和 Review 提高 TeleAgent 成功率。
- 最终优化目标是**质量、可验证性、工程吞吐和高能力额度消耗的综合最优**，不是单一指标最大化。
