# Prompt & Handoff Standard

本文件定义 Project Manager Role 给执行 Agent 派发工程任务，以及执行 Agent 向 Project Manager Role 回传结果的统一格式。

所有正式 Prompt、handoff 和状态信息优先使用**当前项目自己的角色名**；全局规范只定义角色类型，不固定角色名字。项目角色映射见 `AGENT-OPERATING-MODEL.md`。

## 1. TeleAgent Prompt 必须高度结构化

Primary / Secondary Execution Role 的 Prompt 默认采用低歧义、分步骤、可验证写法。不要只写“帮我修一下”“把这个做完”“你看着处理”。

一个完整任务至少应包含：

1. **Task ID**：达到正式任务门槛时必须使用。
2. **项目角色名与职责**：明确本次接任务的是当前项目中的哪个 Agent，以及其角色类型。
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

## 4. Formal prompt

当 Project Manager Role 给 Owner 一段需要原样转发给 Agent 的正式任务时：

- 先用一句话明确**项目角色名 + Task ID + 本轮目标**。
- 随后只给一个完整代码块承载整段 Prompt。
- 不拆成多个相互依赖的代码块。
- 不在代码块结束后再补关键要求。
- 正式任务必须包含 Task ID 与幂等预检。

## 5. GitHub-native handoff：默认优先

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

- `<Primary Agent Name> 完成 APP-AUTH-001。`
- `<Secondary Agent Name> 跑完 CORE-TEST-003。`

如果 Agent 能直接给完成信号，至少包含：项目角色名、Task ID、branch、exact SHA、remote 已 push、验证摘要、阻塞/风险（如有）。

## 6. 什么时候仍需要完整 handoff

只有以下情况才要求完整人工 handoff：

- Project Manager Role 无法访问目标 remote；
- 关键证据不在 GitHub；
- Agent 无权限 push；
- 任务不是以 Git 为主要事实载体；
- Project Manager Role 明确要求补充 GitHub 无法验证的上下文。

完整 handoff 必须先标明**项目角色名 + 角色类型 + Task ID**，例如：

`来源：<Project Agent Name>（Primary Execution Role｜APP-AUTH-001）`

随后提供完成内容、改动文件、关键决策、验证结果、branch、exact SHA、remote 状态、工作树状态和风险。

完整 handoff 是兜底机制，不是 Owner 默认承担的技术信息搬运工作。

## 7. Project Manager Review

Project Manager Role 不把 Agent 完成信号或 handoff 自述当作事实证明。重要任务必须结合 remote、diff、tests、CI 或 exact SHA 独立核验。

验收后明确给出：

- `PASS`
- `HOLD`
- `NEEDS_CORRECTION`

若 `NEEDS_CORRECTION`，下一轮 Prompt 默认沿用原 Task ID 修正当前验收失败点，除非已经构成独立新目标。

## 8. 发送状态不确定时不要重发

如果无法确认正式任务是否已发送或执行：

- 标记为 `SEND_STATUS_UNKNOWN`；
- 先执行 `STATUS_PROBE_ONLY`；
- 检查 branch、remote HEAD、worktree、未 push commit、Task ID 和 exact SHA；
- 未确认前不要重新发送完整任务。

## 9. 避免上下文污染

- 一个 Agent 正在执行完整任务时，不追加另一个完整任务。
- 新发现问题先由 Project Manager Role 排队，除非阻塞当前任务。
- 每轮 Prompt 明确当前阶段、项目角色名、Task ID 和已通过基线。
- 不依赖“你应该还记得上次”；关键上下文来自仓库、Task ID、remote 或当前 Prompt。