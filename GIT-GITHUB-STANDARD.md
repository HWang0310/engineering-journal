# Git & GitHub Standard

## 1. Canonical truth

- GitHub remote 是长期 source of truth。
- 本地 checkout / worktree 是执行环境，不是最终事实源。
- Agent handoff 中写出的 branch、SHA、测试结果需要在重要任务中独立核验。
- 高风险 Review 应针对 **exact commit SHA**，而不是模糊的“最新代码”“当前分支”或本地未提交状态。

## 2. 开始任务前

执行 Agent 应确认：

- 正确 repository。
- 正确 base branch / base SHA。
- 自己负责的 branch。
- 自己负责的独立 worktree（并行 Writer 时必须独立）。
- 工作区没有混入上一任务的未授权修改。

如果前置 SHA 是验收基线，不得仅凭聊天记录假定它正确；需要以 remote 可见状态为准。

## 3. Branch / worktree 隔离

- 两个 Writer 不得同时写同一个 worktree。
- 同一仓库需要并行写入时，使用独立 branch + 独立 worktree。
- 不允许两个 Agent 同时修改同一关键文件区域。
- 对共享关键区域采用一个 Writer，其他 Agent 只读 Review。
- 依赖另一个分支输出的任务应等待前置分支验收后再开始，除非 Curator 明确设计了安全的临时接口。

## 4. Commit 纪律

- 一个 commit 尽量表达一个可审查目的。
- 不混入无关格式化、重命名、依赖升级或个人环境文件。
- commit 前先检查实际 diff。
- commit message 应说明变更意图，不写无法解释内容的模糊消息。
- 需要交付给下一阶段时，push 到 remote，并回传 exact SHA。

## 5. 最低 Git 验证

根据项目适用性执行：

```text
git diff --check
git status --short
```

并配合项目自身的 tests / lint / typecheck / build。

完成后需要干净工作树的任务，应确认 `git status` clean。若不 clean，必须解释剩余变化的来源和是否属于本任务。

## 6. exact-SHA Review

适用于：

- 核心 runtime。
- Contract / schema / API 边界。
- 跨仓库集成。
- 发布、pin、merge 前的高风险检查。
- 多 Agent 串接后的最终基线。

流程：

1. Writer push commit。
2. 回传 branch + exact SHA。
3. Reviewer/Curator 从 remote 独立确认该 SHA 存在且对应正确 branch。
4. Review 针对该 SHA 的 diff、测试与行为。
5. 只有 Review `PASS` 后，才能把该 SHA 标记为后续阶段的 accepted / pinned baseline。

不得把“Agent 说这是某 SHA”当作已验证事实。

## 7. Merge

- 只有 Curator 已验收为 `PASS` 的变更才能进入 merge。
- `HOLD` 不 merge；先补证据或依赖。
- `NEEDS_CORRECTION` 退回原 Writer 修正，除非 Curator 明确完成 Writer 交接。
- merge 前重新确认目标 branch 与 head SHA，避免审查后 branch 已漂移。
- 高风险 PR 的 Review 对象必须和最终 merge head 对齐。

## 8. 不进入 Git 的内容

- 密钥、token、密码、Cookie、真实账号凭据。
- 客户数据、敏感业务数据、私有日志 dump。
- 本地机器专属临时文件。
- 与项目交付无关的大型生成物，除非项目规范明确要求版本化。
