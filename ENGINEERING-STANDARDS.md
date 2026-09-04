# Engineering Standards

本文件定义跨项目默认工程规范。具体项目如果有明确记录的项目级规则，可以覆盖一般全局规则；`RESTRICTED-CONTENT-STANDARD.md` 的 hard gate 不可被项目级规则放宽。

## 1. 决策与责任边界

- **Project Manager Role（默认由 ChatGPT 承担）**：项目经理、总架构协调、任务拆解、技术决策、Agent 路由、最终 Reviewer 与 merge gate。
- **Owner（用户）**：负责目标、优先级和最终业务方向；默认不承担技术实现选择、Agent 分配或验收判断。
- 技术方案存在多个可行路径时，由 Project Manager Role 基于质量、风险、依赖和成本作出明确选择，不把未解决的技术分歧转嫁给 Owner。
- 全局规范固定职责类型，不固定角色名字；项目级角色命名见 `AGENT-OPERATING-MODEL.md`。

## 2. 本地 workspace 与 GitHub 映射

- 所有项目统一位于 `/Users/hwang/Movies/Program/<project-name>/`。
- 一个独立项目使用一个项目子文件夹；项目相关代码、文档、脚本、clone、worktree、测试产物和临时工程活动均留在该项目边界内。
- 单仓库项目可以让项目目录本身直接作为 Git working tree；多仓库项目将所有相关 repo 收拢到同一项目目录中。
- 正式施工前必须确认当前路径、repository、`origin`、branch/base SHA 与任务要求一致。
- durable engineering files 必须进入对应 Git repo 并最终 commit / push；本地目录不替代 GitHub canonical truth。
- 详细规则见 `LOCAL-WORKSPACE-STANDARD.md`。

## 3. 工作分配原则

- **Primary / Secondary Execution Role**：默认由 TeleAgent 承担，优先处理边界清楚、可验证的实现、Git、文档、集成、重复性修改与常规修复。
- **Deep Engineering Role**：默认由 Codex GPT-5.6 Sol 承担，只用于高难架构、Contract、跨仓库集成、高风险 runtime、复杂调试、困难 bug、exact-SHA 审查与核心集成等深水任务。
- 能不用 Deep Engineering Role 时就不用，以节省高能力额度；但质量优先于额度节省，真正需要时必须使用。
- Project Manager Role 能安全直接完成的轻量管理、审阅或小型仓库维护工作，可直接完成，不机械转派。

## 4. 一步一验收

1. Project Manager Role 明确当前只做哪一步。
2. 指定唯一责任 Agent；若并行，明确每个 Agent 的独立边界和项目角色名。
3. 正式任务达到追踪门槛时先分配 Task ID。
4. Agent 在正确项目目录/repo/worktree 中执行并产出可验证结果；能 push GitHub 时优先 push remote。
5. Project Manager Role 独立核验并给出 `PASS / HOLD / NEEDS_CORRECTION`。
6. 只有 `PASS` 后任务才能进入 `ACCEPTED`，并作为下一步事实基础。

同一 Agent 正在执行完整任务时，不再追加新的完整任务 Prompt，避免上下文覆盖和目标漂移。

## 5. Task identity 与生命周期

正式、跨会话、可并行、可重复发送或需要 Git 追溯的工程任务必须使用唯一 Task ID。Task ID 应贯穿 Prompt、执行、branch/commit/PR、完成信号/handoff 与 Review，并作为幂等键防止重复施工。

Prompt 草稿状态不能等同真实派发状态。任务状态、`SEND_STATUS_UNKNOWN`、`STATUS_PROBE_ONLY`、`ALREADY_COMPLETED` 与幂等规则见 `TASK-LIFECYCLE-STANDARD.md`。

## 6. Scope control

每个任务必须明确：目标、Task ID（适用时）、正确项目目录/repo/worktree、允许/禁止修改范围、依赖事实与 SHA、验收标准、验证命令、Git/remote 交付要求。

禁止未授权的“顺手重构”“顺便升级”“顺便整理”。发现额外问题时先报告，不扩大当前任务边界。

## 7. 并行不是目标

Project Manager Role 应主动寻找安全且有真实收益的并行机会，但不预设必须并行。只有同时满足以下条件时才优先并行：

- 无共享可变状态。
- 无文件写入重叠。
- 无必须等待对方产物的分支依赖。
- 不会同时写同一个 worktree。
- 所有 worktree 都位于正确项目目录边界内。
- 合并顺序不会改变实现正确性。

共享关键区域采用 **one Writer + read-only Reviewer**。质量、可审查性和可恢复性优先于表面吞吐量。

## 8. GitHub-native handoff

当 Project Manager Role 能直接访问目标 GitHub repository 时，默认流程为：Agent 完成实现与验证 → commit → push → Owner 只报告项目角色名 + Task ID 完成 → Project Manager Role 自行读取 remote branch、exact SHA、diff、源码与 CI 后 Review。

Owner 默认不承担长篇技术 handoff 搬运工作。完整人工 handoff 只作为 GitHub 无法承载主要事实时的兜底机制。

## 9. Restricted content hard gate

所有项目必须遵守 `RESTRICTED-CONTENT-STANDARD.md`。其中定义的 restricted organization identifier，以及任何可直接识别为同一组织的英文、拼音、缩写、品牌或中英混合等价表达，均不得出现在项目可控内容中。

这是 Owner 指定的跨项目硬约束，项目级文档不得取消或放宽。来源材料命中时必须在进入项目可控边界前替换成中性名称；Review 发现命中时必须 `NEEDS_CORRECTION`。

## 10. Definition of Done

工程任务完成至少应满足适用项：

- 需求/验收项逐条满足。
- 工作路径位于正确 `/Users/hwang/Movies/Program/<project-name>/` 项目目录内。
- 当前 repo 与 GitHub `origin` 映射正确，不是在旧 clone、错误 clone 或另一个项目目录施工。
- 相关 tests 通过；新增行为有可重复验证方式。
- lint / typecheck / build 等既有质量检查通过。
- `git diff --check` 通过。
- 无未授权文件变化。
- restricted-content gate 通过。
- durable engineering files 已进入对应 Git repo，而不是只存在本地散文件。
- commit 可从 GitHub remote 找到。
- 正式任务可追溯到 Task ID、branch 与 exact SHA。
- 要求 clean 时最终 `git status` clean。
- 高风险任务完成 exact-SHA Review 后才进入 merge / pin / 下一阶段。

“代码写完了”“本地看起来可以”不等于完成；在错误 workspace 中施工也不能直接视为完成。

## 11. 验收状态

- `PASS`：目标、workspace/repo 映射、验证、restricted-content gate 和 Git 证据足够，可进入 `ACCEPTED`。
- `HOLD`：实现可能正确，但缺关键验证、remote 状态、workspace 映射、依赖或信息。
- `NEEDS_CORRECTION`：存在明确错误、错误项目目录/clone、越界修改、测试失败、架构偏差、restricted-content 命中或其他验收不满足。

`PASS / HOLD / NEEDS_CORRECTION` 是 Review 结论，不与任务生命周期状态混用。

## 12. 风险处理

- 高风险改动先缩小变更面，再增加验证强度。
- 核心 runtime、数据契约、跨仓库边界、发布链路和不可逆操作，优先安排 Deep Engineering Role 或至少独立 Reviewer。
- 不因 Agent 自称已完成/已测试/已推送而降低验证要求。
- 正式任务发送状态不确定时先做 `STATUS_PROBE_ONLY`，不得直接重复派发完整任务。
- 发现重复 clone、历史散落目录或有 dirty/unpushed 状态的旧 workspace 时，先确认 Git 状态并制定迁移方案，不直接拖拽或删除。