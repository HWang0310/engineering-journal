# Prompt & Handoff Standard

本文件定义 Project Manager Role 给执行 Agent 派发工程任务，以及执行 Agent 向 Project Manager Role 回传结果的统一格式。

所有正式 Prompt、handoff 和状态信息优先使用**当前项目自己的角色名**；全局规范只定义角色类型，不固定角色名字。项目角色映射与 backend routing 见 `AGENT-OPERATING-MODEL.md`。

## 1. 正式 Prompt 必须高度结构化

Primary / Secondary Execution Role、WorkBuddy HY4 或其他执行 backend 的 Prompt 默认采用低歧义、分步骤、可验证写法。不要只写“帮我修一下”“把这个做完”“你看着处理”。

一个完整任务至少应包含：

1. **Task ID**：达到正式任务门槛时必须使用。
2. **项目角色名 + 实际 Agent/backend**：明确本次接任务的是当前项目中的哪个 Agent，以及实际把 Prompt 发给哪个 backend。
3. **唯一目标**：本轮只完成什么。
4. **已确认事实**：branch、SHA、已有实现、前置结论。
5. **允许范围**：可修改仓库、目录、文件。
6. **禁止范围**：明确不得修改的区域和行为。
7. **幂等预检**：检查同一 Task ID 是否已经完成或部分执行；已完成则返回 `ALREADY_COMPLETED`。
8. **执行步骤**：按顺序给出。
9. **验收标准**：什么条件全部满足才算完成。
10. **验证命令**：tests / build / lint / diff / status 等。
11. **Git 要求**：branch、commit、push、exact SHA。
12. **完成信号 / handoff 格式**：给出 Project Manager Role 能定位工程事实的最小必要信息。

Prompt 的目标是把任务转换成边界明确、可验证、可恢复、可幂等执行的工程 Contract。

## 2. Scope 写法

推荐显式写出：

- `必须修改`
- `允许修改`
- `禁止修改`
- `不要做`

发现 scope 外问题时只报告，不自行扩大任务。

## 3. 验收必须可执行

避免“确保没有问题”“尽量完善”等模糊措辞。优先指定测试名称或命令、预期输出、哪些文件应/不应变化、`git diff --check`、`git status`、remote SHA 等证据。

## 4. Dispatch announcement：每次派工先告诉 Owner 是哪个 Agent

当 Project Manager Role 准备给 Owner 一段需要原样转发给工程 Agent 的正式任务时，必须先用一句简短说明明确：

- 当前项目角色名；
- 实际 Agent / backend；
- Task ID；
- 本轮目标；
- 简短 routing 原因（当 backend 选择存在意义时）。

例如：

`本任务交给 <Project Agent Name>（backend: WorkBuddy HY4），Task ID: APP-STATE-004。原因：需要跨多份 canonical docs 做语义一致性修正。`

或：

`本任务交给 <Project Deep Engineer Name>（backend: Codex GPT-5.6 Sol），Task ID: CORE-RUNTIME-009。原因：属于高风险核心 runtime。`

不得只说“发给工程师”或只给抽象角色名，让 Owner 自己猜实际应该把 Prompt 发给哪个 Agent。

随后只给**一个完整代码块**承载整段 Prompt：

- 不拆成多个相互依赖的代码块；
- 不在代码块结束后再补关键要求；
- 正式任务必须包含 Task ID 与幂等预检；
- 涉及本地施工时必须包含正确 Project workspace/repo/worktree；
- 适用时明确 Writer / Reviewer 身份以及实际 backend。

## 5. Backend 与角色名不混用

WorkBuddy HY4、TeleAgent、Codex 等是实际可调度资源，不自动成为跨项目固定角色名。

正式 Prompt 中应同时保留：

- 项目角色名：用于项目长期记忆和跨会话辨识；
- 实际 backend：用于 Owner 知道 Prompt 应发送给谁，以及 Project Manager Role 追踪 capability routing。

项目可以在 project state 中维护自己的角色 ↔ backend 映射，但不得因此创建新的全局强制 Role。

## 6. GitHub-native handoff：默认优先

当 Project Manager Role 能直接访问目标 GitHub repository 时，默认采用 GitHub-native handoff：

```text
Agent 完成实现
→ tests / validation
→ commit
→ push remote
→ Owner 只报告 <Project Agent Name> + Task ID 已完成
→ Project Manager Role 自行读取 branch / exact SHA / diff / source / CI
→ 独立 Review
```

Owner 最多需要类似：

- `<Project Agent Name> 完成 APP-AUTH-001。`
- `<Project Reviewer Name> 完成 CORE-REVIEW-003。`

如果 Agent 能直接给完成信号，至少包含：项目角色名、实际 backend、Task ID、branch、exact SHA、remote 已 push、验证摘要、阻塞/风险（如有）。

## 7. 什么时候仍需要完整 handoff

只有以下情况才要求完整人工 handoff：

- Project Manager Role 无法访问目标 remote；
- 关键证据不在 GitHub；
- Agent 无权限 push；
- 任务不是以 Git 为主要事实载体；
- Project Manager Role 明确要求补充 GitHub 无法验证的上下文。

完整 handoff 必须先标明**项目角色名 + 实际 backend + 角色类型 + Task ID**，例如：

`来源：<Project Agent Name>（backend: WorkBuddy HY4｜Primary Execution Role｜APP-AUTH-001）`

随后提供完成内容、改动文件、关键决策、验证结果、branch、exact SHA、remote 状态、工作树状态和风险。

完整 handoff 是兜底机制，不是 Owner 默认承担的技术信息搬运工作。

## 8. Writer / Reviewer 身份必须显式

无论实际 backend 是 TeleAgent、HY4 还是 Codex，都遵守 **one Writer + read-only Reviewer**。

- Reviewer 默认不修改其正在 Review 的同一关键区域。
- Reviewer 发现问题后退回原 Writer，或由 Project Manager Role 显式完成 Writer ownership transfer。
- 不允许 Reviewer 因具备实现能力而静默变成 Writer。
- ownership transfer 应在下一份 Prompt / task state 中明确记录。

## 9. Project Manager Review

Project Manager Role 不把 Agent 完成信号或 handoff 自述当作事实证明。重要任务必须结合 remote、diff、tests、CI 或 exact SHA 独立核验。

验收后明确给出：

- `PASS`
- `HOLD`
- `NEEDS_CORRECTION`

若 `NEEDS_CORRECTION`，下一轮 Prompt 默认沿用原 Task ID 修正当前验收失败点，除非已经构成独立新目标。

## 10. 发送状态不确定时不要重发

如果无法确认正式任务是否已发送或执行：

- 标记为 `SEND_STATUS_UNKNOWN`；
- 先执行 `STATUS_PROBE_ONLY`；
- 检查 branch、remote HEAD、worktree、未 push commit、Task ID 和 exact SHA；
- 未确认前不要重新发送完整任务。

## 11. 避免上下文污染

- 一个 Agent 正在执行完整任务时，不追加另一个完整任务。
- 新发现问题先由 Project Manager Role 排队，除非阻塞当前任务。
- 每轮 Prompt 明确当前阶段、项目角色名、实际 backend、Task ID 和已通过基线。
- 不依赖“你应该还记得上次”；关键上下文来自仓库、Task ID、remote 或当前 Prompt。