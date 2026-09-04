# AGENTS.md

本仓库是跨项目工程规范与可复用知识的 canonical repository。

## 当本仓库被引用为工程规范时

只要用户在任何新会话中表达类似：

> 请参考 https://github.com/HWang0310/engineering-journal 的工程规范，开发 XXX 项目。

当前 ChatGPT / Agent 就必须把本仓库视为该项目的默认工程协作规则来源，而不是普通参考资料。

在制定项目方案、分派任务或验收前，至少阅读：

1. `README.md`
2. `NEW-SESSION-BOOTSTRAP.md`
3. `ENGINEERING-STANDARDS.md`
4. `RESTRICTED-CONTENT-STANDARD.md`
5. `LOCAL-WORKSPACE-STANDARD.md`
6. `AGENT-OPERATING-MODEL.md`
7. `TASK-LIFECYCLE-STANDARD.md`
8. `PROMPT-HANDOFF-STANDARD.md`
9. `GIT-GITHUB-STANDARD.md`
10. `CODEX-RULES.md`
11. 与本次任务直接相关的其他 Standard / Pattern / Decision

读取后，当前 ChatGPT 默认承担 **Project Manager Role**，不要再次要求用户解释角色职责、本地项目目录、工程师数量、并行、Task ID、Git、handoff、验收或 Agent 路由。

## 本地 workspace 是固定规则

- 所有正式项目统一位于 `/Users/hwang/Movies/Program` 下。
- 一个项目使用一个 Project workspace：`/Users/hwang/Movies/Program/<project-name>/`。
- Agent 可以拥有自己的默认 workspace，但它只用于 Agent 产品自身的 session、缓存、工具状态等运行数据，不是项目代码或项目事实存放位置。
- 正式项目任务开始前，Agent 必须离开自己的默认 workspace，进入正确 Project workspace 后再执行代码、脚本、构建、测试、Git 和文件操作。
- 如果正式 Project workspace 已存在，Agent 禁止在自己的 workspace 中再次 clone、复制或 `git init` 同一项目副本。
- 如果 Agent workspace 已经存在同项目旧 clone，不得直接继续施工；先检查未提交/未 push 成果并报告，再回到正式 Project workspace。
- 单仓库项目可以直接让项目目录作为 Git working tree；多仓库项目把各 repo 放在同一项目目录中。
- 项目相关 clone、worktree、脚本、临时工程文件和测试产物不得长期散落到项目目录之外。
- 正式施工前确认 `pwd`、repository、`origin`、branch/base SHA 与任务要求一致。
- durable code/docs/scripts/config/tests 必须进入对应 Git repo 并最终 commit / push；Agent workspace 或本地散文件不是长期事实源。
- 并行 worktree 也必须建在对应 Project workspace 内，规则见 `LOCAL-WORKSPACE-STANDARD.md`。

## 项目角色名不是全局固定值

- 全局只固定角色类型：Project Manager Role、Deep Engineering Role、Primary Execution Role、Secondary Execution Role、Owner。
- 每个项目可以并建议使用自己的角色名，以便不同项目区分。
- 如果项目已有角色映射，必须沿用。
- 如果没有，由 Project Manager Role 自动生成项目专属名称并向 Owner 报告，不因命名阻塞启动。
- 后续 Prompt、handoff、project state、工程师配置优先使用项目自己的角色名。
- 不得把其他项目的角色名机械复制到当前项目。
- 项目中途改名时必须记录迁移映射，避免历史 Task/Handoff 失联。

`RESTRICTED-CONTENT-STANDARD.md` 是 Owner 指定的跨项目 hard gate，任何项目级规则不得取消或放宽。

## 修改本仓库时的原则

- 只沉淀跨项目、长期有价值的规则和经验。
- 不写普通项目流水账、临时任务、原始聊天、完整 Prompt 历史。
- 不写密钥、账号凭据、客户数据、敏感业务数据或私有日志。
- 不在规范文件中明文写出 restricted organization identifier；按 `RESTRICTED-CONTENT-STANDARD.md` 的定义引用。
- 同一概念只维护一个 canonical 定义。
- 新规则优先合并进现有标准；只有形成独立知识域时才新增文件。
- 修改规范后更新 `JOURNAL.md`。

## 工程执行

- 遵循 one Writer / isolated worktree。
- 正式任务使用唯一 Task ID，并执行幂等预检。
- 同一 Task ID 已有完成且可验证结果时返回 `ALREADY_COMPLETED`，不得重复施工。
- 发送/执行状态未知时只做 `STATUS_PROBE_ONLY`。
- 正式施工前确认 Agent 已进入正确 Project workspace，并确认 repo/remote 映射；错误目录、Agent workspace 副本或错误 clone 不得直接继续。
- 重要修改必须能追溯 branch、exact SHA、验证结果。
- Project Manager Role 能访问 GitHub 时优先 GitHub-native handoff；Owner 不默认搬运长篇技术结果。
- 提交、Review、发布或 handoff 前检查 restricted-content gate；命中必须 `NEEDS_CORRECTION`。
- 不把本地状态或 Agent 自述当作 remote 事实。
- 未通过 Project Manager Review 的内容不得描述为 accepted baseline。
- 项目事实足够时直接推进第一步，不为全局已定义的工程习惯反复向 Owner 提问。

若目标项目存在明确项目级规则，可在项目范围内覆盖一般工程规范；用户当前明确指令优先。但 restricted-content hard gate 不允许被放宽，本地 Project workspace 与 GitHub canonical truth 原则也不得被项目级默认规则静默破坏。