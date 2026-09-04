# Git & GitHub Standard

## 1. Canonical truth

- GitHub remote 是长期 source of truth。
- 本地 checkout / worktree 是执行环境，不是最终事实源。
- 所有本地项目统一位于 `/Users/hwang/Movies/Program/<project-name>/`，目录规则见 `LOCAL-WORKSPACE-STANDARD.md`。
- Agent handoff 中写出的 branch、SHA、测试结果需要在重要任务中独立核验。
- 高风险 Review 应针对 **exact commit SHA**，而不是模糊的“最新代码”“当前分支”或本地未提交状态。

## 2. 开始任务前

执行 Agent 应确认：

- 当前路径位于正确的 `/Users/hwang/Movies/Program/<project-name>/` 项目目录内；
- 当前 repository 是目标 repository；
- `git remote -v` 的 `origin` 指向预期 GitHub repository；
- 正确 base branch / base SHA；
- 自己负责的 branch；
- 自己负责的独立 worktree（并行 Writer 时必须独立，且 worktree 仍位于当前项目目录内）；
- 工作区没有混入上一任务或另一个项目的未授权修改。

如果前置 SHA 是验收基线，不得仅凭聊天记录假定正确；以 remote 可见状态为准。

如果发现当前目录是旧 clone、重复 clone、错误 remote 或项目目录之外的临时 clone，应先停止正式修改并确认迁移/废弃方案。

## 3. Branch / worktree 隔离

- 两个 Writer 不得同时写同一个 worktree。
- 同一仓库并行写入时使用独立 branch + 独立 worktree。
- worktree 必须放在对应项目目录内部，推荐：`/Users/hwang/Movies/Program/<project-name>/worktrees/<repo-name>/<task-id-or-branch>/`。
- 不允许两个 Agent 同时修改同一关键文件区域。
- 共享关键区域采用一个 Writer，其他 Agent 只读 Review。
- 依赖另一个分支输出的任务应等待前置分支验收后再开始，除非 Project Manager Role 明确设计了安全临时接口。
- worktree 清理前必须确认没有未提交、未 push 的有效成果。

## 4. Commit 纪律

- 一个 commit 尽量表达一个可审查目的。
- 不混入无关格式化、重命名、依赖升级或个人环境文件。
- commit 前检查实际 diff。
- commit message 应说明变更意图。
- 需要长期保留的工程成果必须进入对应 Git repo，不得只留在项目根目录未受 Git 管理的散文件里。
- 需要交付给下一阶段时 push remote，并回传 exact SHA。

## 5. 最低 Git 验证

根据项目适用性执行：

```text
pwd
git remote -v
git diff --check
git status --short
```

并配合项目自身 tests / lint / typecheck / build。

其中：

- `pwd` 用于确认当前工作路径处于正确项目边界内；
- `git remote -v` 用于确认 repo ↔ GitHub remote 映射；
- 要求干净工作树时确认 `git status` clean；不 clean 必须解释剩余变化来源。

## 6. exact-SHA Review

适用于核心 runtime、Contract/schema/API 边界、跨仓库集成、发布/pin/merge 前高风险检查、多 Agent 串接后的最终基线。

流程：

1. Writer push commit。
2. 回传 branch + exact SHA。
3. Reviewer / Project Manager Role 从 remote 独立确认 SHA 存在且对应正确 branch/repository。
4. Review 针对该 SHA 的 diff、测试与行为。
5. 只有 Review `PASS` 后，才能把该 SHA 标记为后续阶段 accepted / pinned baseline。

不得把“Agent 说这是某 SHA”当作已验证事实，也不得因为本地某个 clone 有提交就假定对应 remote 已同步。

## 7. Merge

- 只有 Project Manager Role 已验收为 `PASS` 的变更才能进入 merge。
- `HOLD` 不 merge；先补证据或依赖。
- `NEEDS_CORRECTION` 退回原 Writer 修正，除非 Project Manager Role 明确完成 Writer 交接。
- merge 前重新确认目标 repository、目标 branch 与 head SHA，避免审查后 branch 漂移或在错误 clone 上操作。
- 高风险 PR 的 Review 对象必须和最终 merge head 对齐。
- 在项目沟通中优先使用该项目自己的角色名；全局这里的 `Project Manager Role` 只是职责类型。

## 8. 不进入 Git 的内容

- 密钥、token、密码、Cookie、真实账号凭据。
- 客户数据、敏感业务数据、私有日志 dump。
- 本地机器专属临时文件。
- 与项目交付无关的大型生成物，除非项目规范明确要求版本化。
- `RESTRICTED-CONTENT-STANDARD.md` 禁止的内容。

这类本地-only 内容如果项目确实需要，应留在 `/Users/hwang/Movies/Program/<project-name>/` 项目边界内的 local/temp/artifacts 类目录，并确保不会误提交。