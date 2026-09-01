# Prompt & Handoff Standard

本文件定义 Curator 给 Axiom / Mason / Rivet 派发工程任务，以及执行 Agent 向 Curator 回传结果的统一格式。

## 1. TeleAgent Prompt 必须高度结构化

Mason / Rivet 的 Prompt 默认采用低歧义、分步骤、可验证写法。不要只写“帮我修一下”“把这个做完”“你看着处理”。

一个完整任务至少应包含：

1. **角色与上下文**：你是谁、当前项目/阶段是什么。
2. **唯一目标**：本轮只完成什么。
3. **已确认事实**：branch、SHA、已有实现、前置结论。
4. **允许范围**：可修改仓库、目录、文件。
5. **禁止范围**：明确不得修改的区域和行为。
6. **执行步骤**：按顺序给出；复杂任务拆成可验证的小步骤。
7. **验收标准**：什么条件全部满足才算完成。
8. **验证命令**：tests / build / lint / diff / status 等。
9. **Git 要求**：branch、commit、push、exact SHA。
10. **回传格式**：要求 Agent 给出 Curator 能直接验收的证据。

Prompt 的目标不是教 Agent“尽量做好”，而是把任务转换成**边界明确的工程 Contract**。

## 2. Scope 写法

推荐显式写出：

- `必须修改`：本轮一定要落地的内容。
- `允许修改`：为完成目标可以触碰的范围。
- `禁止修改`：本轮绝对不要碰的模块。
- `不要做`：不要顺手重构、升级依赖、改格式、改无关命名等。

发现 scope 外问题时，只在回传中报告，不自行扩大任务。

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

## 4. Formal handoff

当 Curator 给用户一段需要原样转发给 Agent 的正式任务时：

- 先用一句话明确发给谁、做什么。
- 随后只给**一个完整代码块**承载整段 Prompt。
- 不把一个正式任务拆成多个互相依赖的代码块。
- 不在代码块中途结束后再补关键要求。

## 5. Agent 回传格式

执行 Agent 回传给 Curator 时，必须先标明来源。推荐：

`来源：Mason（TeleAgent｜<task>）`

或：

`来源：Rivet（TeleAgent｜<task>）`

`来源：Axiom（Codex｜<task>）`

随后使用：

`请将以下复制给ChatGPT：`

并给出一段完整、可复制的结果，其中至少包含：

- 完成了什么。
- 修改了哪些文件。
- 关键实现/决策。
- 实际执行的验证及结果。
- branch。
- commit exact SHA。
- remote 是否已 push / 可见。
- 工作树状态。
- 已知风险、未完成项或需要 Curator 判断的事项。

## 6. Curator 验收方式

Curator 不把 handoff 文本本身当作事实证明。重要任务应结合 GitHub remote、diff、tests、CI 或 exact SHA 独立核验。

验收后明确给出：

- `PASS`：可以继续。
- `HOLD`：先补证据或等待依赖。
- `NEEDS_CORRECTION`：退回具体修正项。

若 `NEEDS_CORRECTION`，下一轮 Prompt 应只修当前验收失败点，避免重新打开整个任务边界。

## 7. 避免上下文污染

- 一个 Agent 正在执行完整任务时，不再向它追加另一个完整任务。
- 新发现的问题先由 Curator排队，除非它阻塞当前任务。
- 每轮 Prompt 明确当前阶段和已通过的基线，避免 Agent 自行重做历史工作。
- 不依赖“你应该还记得上次”作为工程事实；关键上下文必须来自仓库或当前 Prompt。
