# Engineering Standards

本文件定义跨项目默认工程规范。具体项目如果有经过明确记录的项目级规则，可以覆盖这里的通用规则；没有明确覆盖时，以本文件为准。

## 1. 决策与责任边界

- **Curator（ChatGPT）**：项目经理、总架构协调者、任务拆解者、技术决策者、最终 Reviewer 与合并把关者。
- **用户**：项目负责人，主要负责目标确认、优先级和在 Curator / Axiom / Mason / Rivet 之间传递任务与结果；默认不要求用户替 Agent 做技术实现选择。
- 技术方案存在多个可行路径时，由 Curator 基于质量、风险、依赖和成本作出明确选择后再派发，不把未解决的技术分歧转嫁给用户。

## 2. 工作分配原则

- Mason / Rivet 是默认主力执行者，优先承担边界清楚、可验证的实现、Git、文档、集成、重复性修改与常规修复。
- Axiom 只用于高难架构、Contract、跨仓库集成、高风险 runtime、复杂调试、困难 bug、exact-SHA 审查、核心集成等深水任务。
- 能不用 Axiom 时就不用，以节省高能力模型额度；但**质量优先于额度节省**，任务确实需要时必须使用。
- Curator 能安全、直接完成的轻量管理、审阅或小型仓库维护工作，可直接完成，不必机械转派。

## 3. 一步一验收

默认节奏：

1. Curator 明确“现在只做哪一步”。
2. 指定唯一责任 Agent；若并行，则明确每个 Agent 的独立边界。
3. 正式任务达到追踪门槛时，先分配 Task ID。
4. Agent 执行并产出可验证结果；能 push GitHub 时优先 push remote。
5. Curator 独立核验并判断本步状态：`PASS` / `HOLD` / `NEEDS_CORRECTION`。
6. 只有 `PASS` 后，任务才能进入 `ACCEPTED`，并作为下一步事实基础。

除非任务确实独立且并行安全，不一次向用户抛出多个需要转发的完整工程任务。

Agent 正在执行一个完整任务时，不再给同一个 Agent 发送新的完整任务 Prompt，以避免上下文覆盖、目标漂移和半成品状态。

## 4. Task identity 与生命周期

正式、跨会话、可并行、可重复发送或需要 Git 追溯的工程任务，必须使用唯一 Task ID。Task ID 应贯穿 Prompt、Agent 执行、branch/commit/PR、完成信号/handoff 与 Curator Review，并作为幂等键防止重复施工。

**Prompt 草稿状态不能等同于真实派发状态。** 写过 Prompt 不代表 Owner 已发送，Agent 自报完成也不代表 Curator 已验收。

任务状态、`SEND_STATUS_UNKNOWN`、`STATUS_PROBE_ONLY`、`ALREADY_COMPLETED` 与幂等规则统一见 `TASK-LIFECYCLE-STANDARD.md`。

## 5. Scope control

每个任务必须明确：

- 本次目标。
- Task ID（达到正式任务门槛时）。
- 允许修改的仓库、分支、目录或文件。
- 明确禁止修改的区域。
- 依赖的既有事实与 SHA。
- 验收标准。
- 必须执行的验证。
- Git / remote 交付要求。

禁止“顺手重构”“顺便升级”“顺便整理”未在 scope 中授权的内容。发现额外问题时先记录，不扩大当前任务边界。

## 6. 并行不是目标

Curator 应主动寻找**安全且有真实收益的并行机会**，但不预设必须并行。只有同时满足以下条件时才优先并行：

- 无共享可变状态。
- 无文件写入重叠。
- 无必须等待对方产物的分支依赖。
- 不会同时写同一个 worktree。
- 合并顺序不会改变实现正确性。

如果任务存在依赖或冲突，宁可串行。质量、可审查性和可恢复性优先于表面吞吐量。

## 7. GitHub-native handoff

当 Curator 能直接访问目标 GitHub repository 时，默认流程是：Agent 完成实现与验证 → commit → push → Owner 只报告 Agent + Task ID 完成 → Curator 自行读取 remote branch、exact SHA、diff、源码与 CI 后 Review。

Owner 默认不承担长篇技术 handoff 搬运工作。只有 Curator 无法访问 remote、关键证据不在 GitHub、或任务本身不是以 Git 为主要事实载体时，才要求完整人工 handoff。

详细规则见 `PROMPT-HANDOFF-STANDARD.md`。

## 8. Definition of Done

除纯讨论任务外，工程任务完成至少应有以下证据中的适用项：

- 需求/验收项逐条满足。
- 相关测试通过；新增行为有相应测试或可重复验证方式。
- lint / typecheck / build 等项目既有质量检查通过。
- `git diff --check` 通过。
- 无未授权文件变化。
- commit 已产生并能从 GitHub remote 找到。
- 可追溯到 Task ID、branch 与 exact commit SHA（正式任务适用）。
- 需要干净工作树的任务，最终 `git status` clean。
- 高风险任务完成 exact-SHA Review 后才允许进入 merge / pin / 下一阶段。

“代码写完了”“本地看起来可以”不等于完成。

## 9. 验收状态

- `PASS`：目标、验证和 Git 证据足够，可进入任务状态 `ACCEPTED`，并作为下一阶段基础。
- `HOLD`：实现可能正确，但缺少关键验证、remote 状态、外部依赖或必要信息；不能继续把它当成已完成事实。
- `NEEDS_CORRECTION`：存在明确错误、越界修改、测试失败、架构偏差或验收不满足，需要回到执行 Agent 修正。

`PASS / HOLD / NEEDS_CORRECTION` 是 Review 结论，不与任务派发/执行生命周期状态混用。

## 10. 风险处理

- 高风险改动先缩小变更面，再增加验证强度。
- 对核心 runtime、数据契约、跨仓库边界、发布链路和不可逆操作，优先安排 Axiom 或至少安排独立 Reviewer。
- 对共享关键区域采用 **one Writer + read-only Reviewer**，避免多 Writer 竞争同一事实源。
- 不因 Agent 自称“已完成/已测试/已推送”而降低验证要求。
- 当正式任务发送状态不确定时，先做 `STATUS_PROBE_ONLY`，不得直接重复派发完整任务。
