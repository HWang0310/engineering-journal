# Prompt & Handoff Standard

本文件定义 Curator 给 Axiom / Mason / Rivet 派发工程任务，以及执行 Agent 向 Curator 回传结果的统一格式。

## 1. TeleAgent Prompt 必须高度结构化

Mason / Rivet 的 Prompt 默认采用低歧义、分步骤、可验证写法。不要只写“帮我修一下”“把这个做完”“你看着处理”。

一个完整任务至少应包含：

1. **Task ID**：达到正式任务门槛时必须使用，规则见 `TASK-LIFECYCLE-STANDARD.md`。
2. **角色与上下文**：你是谁、当前项目/阶段是什么。
3. **唯一目标**：本轮只完成什么。
4. **已确认事实**：branch、SHA、已有实现、前置结论。
5. **允许范围**：可修改仓库、目录、文件。
6. **禁止范围**：明确不得修改的区域和行为。
7. **幂等预检**：先确认同一 Task ID 是否已经完成或部分执行；已完成则返回 `ALREADY_COMPLETED`，不得重复施工。
8. **执行步骤**：按顺序给出；复杂任务拆成可验证的小步骤。
9. **验收标准**：什么条件全部满足才算完成。
10. **验证命令**：tests / build / lint / diff / status 等。
11. **Git 要求**：branch、commit、push、exact SHA。
12. **完成信号 / handoff 格式**：要求 Agent 给出 Curator 能直接定位工程事实的最小必要信息。

Prompt 的目标不是教 Agent“尽量做好”，而是把任务转换成**边界明确、可验证、可恢复、可幂等执行的工程 Contract**。

## 2. Scope 写法

推荐显式写出：

- `必须修改`：本轮一定要落地的内容。
- `允许修改`：为完成目标可以触碰的范围。
- `禁止修改`：本轮绝对不要碰的模块。
- `不要做`：不要顺手重构、升级依赖、改格式、改无关命名等。

发现 scope 外问题时，只在完成信号或 handoff 中报告，不自行扩大任务。

## 3. 验收必须可执行

避免：

- “确保没有问题”。
- “尽量完善”。
- “检查一下是否合理”。

优先：

- 指定测试名称或命令。
- 指定预期输出或行为。
- 指定哪些文件应该/不应该变化。
- 指定 `git diff --check`、`git status`、remote SHA 等 Git 证据。

## 4. Formal prompt

当 Curator 给 Owner 一段需要原样转发给 Agent 的正式任务时：

- 先用一句话明确发给谁、Task ID 和本轮目标。
- 随后只给**一个完整代码块**承载整段 Prompt。
- 不把一个正式任务拆成多个互相依赖的代码块。
- 不在代码块中途结束后再补关键要求。
- 达到正式任务门槛时，Prompt 必须包含 Task ID 与幂等预检规则。

## 5. GitHub-native handoff：默认优先

当 Curator 能直接访问目标 GitHub repository 时，默认采用 **GitHub-native handoff**，不要让 Owner 搬运大段技术结果。

推荐流程：

```text
Agent 完成实现
→ tests / validation
→ commit
→ push remote
→ Owner 只报告 Agent + Task ID 已完成
→ Curator 自行读取 branch / exact SHA / diff / source / CI
→ Curator 独立 Review
```

例如 Owner 只需要告诉 Curator：

- `Mason 完成 APP-AUTH-001。`
- `Rivet 跑完了，Task ID CORE-TEST-003。`

如果 Agent 能直接提供一句简短完成信号，至少包含：

- Agent 来源；
- Task ID；
- branch；
- exact commit SHA；
- remote 已 push；
- 验证摘要；
- 已知阻塞/风险（如有）。

Curator 应自行从 GitHub 核验详细 diff、源码和提交事实，不要求 Owner 重复搬运这些内容。

## 6. 什么时候仍需要完整 handoff

以下情况可以使用完整人工 handoff：

- Curator 无法访问目标 remote / repository；
- 关键证据不在 GitHub，例如本地运行环境、专有系统结果或临时外部依赖；
- Agent 没有权限 push，但需要交付必要技术证据；
- 任务属于非 Git 工程，remote exact SHA 不能承载主要事实；
- Curator 明确要求补充 GitHub 无法验证的上下文。

完整 handoff 必须先标明来源和 Task ID，推荐：

`来源：Mason（TeleAgent｜APP-AUTH-001）`

然后提供：

- 完成了什么；
- 修改了哪些文件；
- 关键实现/决策；
- 实际执行的验证及结果；
- branch；
- exact commit SHA（如适用）；
- remote 是否已 push / 可见；
- 工作树状态；
- 已知风险、未完成项或需要 Curator 判断的事项。

**完整 handoff 是兜底机制，不是 Owner 默认承担的技术信息搬运工作。**

## 7. Curator 验收方式

Curator 不把 Agent 完成信号或 handoff 文本本身当作事实证明。重要任务应结合 GitHub remote、diff、tests、CI 或 exact SHA 独立核验。

验收后明确给出：

- `PASS`：目标、验证和 remote 证据足够；任务可进入 `ACCEPTED`。
- `HOLD`：先补证据或等待依赖。
- `NEEDS_CORRECTION`：退回具体修正项。

若 `NEEDS_CORRECTION`，下一轮 Prompt 应沿用原 Task ID 修正当前验收失败点，除非修正已经构成独立的新工程目标；不要无故创建新 Task ID 或重新打开整个任务边界。

## 8. 发送状态不确定时不要重发

Prompt 和真实任务状态必须分开。写过 Prompt 不等于 Owner 已经发送。

如果无法确认某个正式任务是否已发送或执行：

- 标记为 `SEND_STATUS_UNKNOWN`；
- 先执行 `STATUS_PROBE_ONLY`；
- 检查 branch、remote HEAD、worktree、未 push commit、Task ID 和已有 exact SHA；
- 未确认前不要重新发送完整任务。

详细规则见 `TASK-LIFECYCLE-STANDARD.md`。

## 9. 避免上下文污染

- 一个 Agent 正在执行完整任务时，不再向它追加另一个完整任务。
- 新发现的问题先由 Curator 排队，除非它阻塞当前任务。
- 每轮 Prompt 明确当前阶段、Task ID 和已通过的基线，避免 Agent 自行重做历史工作。
- 不依赖“你应该还记得上次”作为工程事实；关键上下文必须来自仓库、Task ID、remote 或当前 Prompt。
