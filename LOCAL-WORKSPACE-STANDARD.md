# Local Workspace Standard

本文件定义所有项目在 Owner 本地 Mac 上的统一目录组织、GitHub repository 映射和 worktree 管理规则。

## 1. 唯一本地项目根目录

所有项目统一放在：

`/Users/hwang/Movies/Program`

每一个独立项目必须拥有且只使用该目录下的一个项目子文件夹：

`/Users/hwang/Movies/Program/<project-name>/`

除非 Owner 明确指定例外，项目代码、项目文档、工程脚本、clone、worktree、临时工程文件、测试产物和项目相关操作都必须发生在该项目子文件夹内部。

不得为了方便把同一项目的工程文件长期散落在 Desktop、Downloads、Documents、其他临时目录或另一个项目文件夹中。

## 2. 单仓库项目

如果一个项目只对应一个 GitHub repository，推荐让项目目录本身直接作为该 repository 的 working tree：

```text
/Users/hwang/Movies/Program/<project-name>/
├── .git/
├── source...
├── docs...
└── ...
```

项目目录名应尽量与 GitHub repository 名一致。若因历史原因不同，Project Manager Role 必须明确知道本地目录与 remote repository 的映射，不得靠猜测。

## 3. 多仓库项目

如果一个项目包含多个 GitHub repositories，则项目目录作为容器，各 repository checkout 放在其中，并优先使用与 GitHub repository 完全一致的目录名：

```text
/Users/hwang/Movies/Program/<project-name>/
├── <repo-a>/
├── <repo-b>/
├── <repo-c>/
└── worktrees/
```

例如一个项目包含 Core、Plugin-A、Plugin-B 三个 repository 时，它们都属于同一个 `<project-name>` 目录，不应散落成 `/Program` 下三个互不关联的项目目录。

## 4. GitHub repository 一一映射

每个本地 Git working tree 必须能明确映射到一个预期 GitHub repository。

开始正式工程任务前，执行 Agent 至少确认：

- 当前路径位于 `/Users/hwang/Movies/Program/<project-name>/` 内；
- 当前 repository 是目标 repository；
- `git remote -v` 中的 `origin` 指向预期 GitHub repository；
- 当前 branch / base SHA 与任务要求一致；
- 没有误在另一个项目、旧 clone 或临时 clone 中工作。

如果本地 clone 与 GitHub repository 的映射不清楚，先解决映射问题，再开始修改代码。

## 5. Durable files 必须回到 GitHub

本地项目目录是 workspace，不替代 GitHub canonical truth。

需要长期保留或被后续工程依赖的内容，例如：

- source code；
- project docs；
- migration / SQL / scripts；
- tests / fixtures；
- configuration；
- architecture / Contract；
- project state / handoff；
- 需要复用的工具代码；

必须放入对应 Git repository 的合理位置，并按工程规范 commit / push。

不得把关键工程成果只保存在项目根目录下未受 Git 管理的散文件中，然后依赖本地目录作为长期事实源。

## 6. 本地-only 内容

确实不应进入 Git 的本地内容可以留在项目目录内部，例如：

- 临时下载；
- 大型本地生成物；
- 一次性调试输出；
- 本地缓存；
- 本地环境文件；
- 不可提交的私有输入。

这类内容应放在项目目录内清晰的 local/temp/artifacts 类子目录，并确保不会误 commit。任务完成后，不再需要的临时内容应清理，不制造长期垃圾目录。

敏感内容仍遵守 Git/GitHub 与 restricted-content 相关规范；“放在本地项目目录内”不代表允许提交或长期保留不应保存的内容。

## 7. Worktree 管理

并行 Writer 需要独立 worktree 时，worktree 也必须放在当前项目目录内部。

推荐结构：

```text
/Users/hwang/Movies/Program/<project-name>/worktrees/<repo-name>/<task-id-or-branch>/
```

规则：

- 不把项目 worktree 建到 `/tmp`、Desktop、Downloads 或其他项目目录；
- 每个 Writer 使用独立 branch + 独立 worktree；
- worktree 名称应能关联 Task ID 或 branch；
- 任务结束、分支 merge 且确认不再需要后，应清理废弃 worktree；
- 删除 worktree 前先确认没有未提交、未 push 的有效成果。

## 8. 新项目初始化

新项目启动时，Project Manager Role 必须确认本地工作区规划：

1. 项目目录为 `/Users/hwang/Movies/Program/<project-name>/`。
2. 判断项目是单仓库还是多仓库。
3. 明确每个 GitHub repository 对应的本地路径。
4. 如果 repository 尚未 clone，应 clone 到该项目目录规划的位置，而不是任意当前目录。
5. 确认 `origin`、default branch、remote HEAD。
6. 检查是否存在历史重复 clone 或散落文件；发现时先决定迁移/废弃方案，避免两个本地副本同时被当成当前工程事实。

Project Manager Role 应在首次项目恢复/启动时把本地 workspace 映射作为项目事实的一部分确认，但不要求 Owner 每次重复说明统一根目录。

## 9. 现有项目整理

如果已有项目不符合本规则：

- 不立即粗暴移动仍在运行的 worktree 或有未提交修改的目录；
- 先确认 repository、remote、branch、未 push commit 和 dirty state；
- 再制定最小风险迁移方案；
- 迁移后重新验证 remote mapping、build/test 和 Git 状态；
- 确认新目录成为唯一活跃 workspace 后，再清理旧副本。

禁止在未确认 Git 状态时直接拖拽/删除旧项目目录。

## 10. Project Manager / Agent 验收

对于涉及本地代码执行、clone、worktree 或文件修改的正式任务，Project Manager Role 应检查适用项：

- 工作路径是否位于正确项目目录；
- repo ↔ GitHub remote 是否对应；
- 是否存在错误 clone / 错 worktree / 错项目目录施工；
- durable files 是否进入对应 Git repo；
- 重要成果是否已 commit / push；
- 临时内容是否留在项目边界内且没有误提交。

如果 Agent 在错误项目目录、错误 clone 或项目目录之外进行正式施工，默认不能直接 `PASS`；应先确认成果能否安全迁移并恢复正确 Git 事实链。

## 11. 核心原则

一句话：

> 一个项目，一个 `/Users/hwang/Movies/Program` 下的项目目录；所有本地工程活动留在该项目边界内，所有 durable engineering truth 回到与之对应的 GitHub repository。