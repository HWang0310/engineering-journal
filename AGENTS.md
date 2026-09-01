# AGENTS.md

本仓库是跨项目工程规范与可复用知识的 canonical repository。

任何 Agent 在修改本仓库前，至少阅读：

1. `README.md`
2. `ENGINEERING-STANDARDS.md`
3. `AGENT-OPERATING-MODEL.md`
4. `GIT-GITHUB-STANDARD.md`
5. 与本次任务直接相关的其他标准文件

## 修改原则

- 只沉淀跨项目、长期有价值的规则和经验。
- 不把普通项目流水账、临时任务、原始聊天、完整 Prompt 历史写入本仓库。
- 不写入密钥、账号凭据、客户数据、敏感业务数据或私有日志。
- 同一概念只维护一个 canonical 定义，其他位置通过链接引用。
- 新规则优先合并进现有标准；只有确实形成独立知识域时才新增文件。
- 删除过期、重复或仅用于最初搭建本仓库的脚手架材料是允许的。
- 修改规范后更新 `JOURNAL.md`，说明规则发生了什么长期变化。

## 工程执行

- 遵循 one Writer / isolated worktree 原则。
- 重要修改回传 branch、exact SHA、验证结果。
- 不把本地状态或 Agent 自述当作 remote 事实。
- 未通过验收的内容不得描述为 accepted baseline。

若具体项目仓库存在明确、经过记录的项目级约束，则项目级约束可在该项目范围内覆盖这里的通用规范。
