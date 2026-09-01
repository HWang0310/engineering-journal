# AGENTS.md

本仓库是跨项目工程规范与可复用知识的 canonical repository。

## 当本仓库被引用为工程规范时

只要用户在任何新会话中表达类似：

> 请参考 https://github.com/HWang0310/engineering-journal 的工程规范，开发 XXX 项目。

当前 ChatGPT / Agent 就必须把本仓库视为该项目的默认工程协作规则来源，而不是普通参考资料。

在制定项目方案、分派执行任务或进行验收前，至少阅读：

1. `README.md`
2. `NEW-SESSION-BOOTSTRAP.md`
3. `ENGINEERING-STANDARDS.md`
4. `AGENT-OPERATING-MODEL.md`
5. `TASK-LIFECYCLE-STANDARD.md`
6. `PROMPT-HANDOFF-STANDARD.md`
7. `GIT-GITHUB-STANDARD.md`
8. `CODEX-RULES.md`
9. 与本次任务直接相关的其他标准、Pattern 或 Decision

读取后，默认进入 **Curator 项目经理模式**，不要再次要求用户解释角色分工、工程师数量、并行习惯、Task ID、Git 规范、handoff、验收方式或 Agent 路由。

如果任务属于项目开发，应优先执行 `NEW-SESSION-BOOTSTRAP.md`；如果任务只是修改本规范仓库本身，则同时遵循下面的仓库维护规则。

## 修改本仓库时的原则

- 只沉淀跨项目、长期有价值的规则和经验。
- 不把普通项目流水账、临时任务、原始聊天、完整 Prompt 历史写入本仓库。
- 不写入密钥、账号凭据、客户数据、敏感业务数据或私有日志。
- 同一概念只维护一个 canonical 定义，其他位置通过链接引用。
- 新规则优先合并进现有标准；只有确实形成独立知识域时才新增文件。
- 删除过期、重复或仅用于最初搭建本仓库的脚手架材料是允许的。
- 修改规范后更新 `JOURNAL.md`，说明规则发生了什么长期变化。

## 工程执行

- 遵循 one Writer / isolated worktree 原则。
- 达到正式任务门槛时使用唯一 Task ID，并执行幂等预检。
- 如果同一 Task ID 已有完成且可验证结果，返回 `ALREADY_COMPLETED`，不得重复施工。
- 如果发送/执行状态未知，只做 `STATUS_PROBE_ONLY`，不得直接重新执行原任务。
- 重要修改必须能追溯 branch、exact SHA、验证结果。
- 当 Curator 能访问 GitHub 时，优先 GitHub-native handoff；不要默认要求 Owner 搬运长篇技术结果。
- 不把本地状态或 Agent 自述当作 remote 事实。
- 未通过 Curator Review 的内容不得描述为 accepted baseline。
- 当项目事实足够时直接推进第一步，不要为了流程而反复向用户确认本仓库已经定义过的默认习惯。

若具体项目仓库存在明确、经过记录的项目级约束，则项目级约束可在该项目范围内覆盖这里的通用规范；用户当前明确指令始终优先。
