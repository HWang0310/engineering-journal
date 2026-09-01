# Engineering Journal

`engineering-journal` 是跨项目工程规范与可复用工程知识的长期仓库。

它不是任务清单、项目看板、聊天记录归档或代码仓库。它只保存未来项目仍值得复用的工程规则、协作方式、质量门槛、Git/GitHub 规范和经验模式。

## Source of truth

工程事实默认按以下优先级判断：

1. GitHub remote 上可验证的 branch / commit / exact SHA。
2. 具体项目仓库中的代码、测试和项目级文档。
3. 本地 checkout / worktree，仅视为工作副本。
4. ChatGPT、Codex、TeleAgent 的口头说明或 handoff；在 remote 与 exact SHA 未核验前，不视为最终事实。

因此：**GitHub 是长期 canonical memory，本地目录只是 workspace。**

## 核心文档

| 文件 | 用途 |
| --- | --- |
| `ENGINEERING-STANDARDS.md` | 跨项目总工程原则、执行节奏与 Definition of Done |
| `AGENT-OPERATING-MODEL.md` | Curator / Axiom / Mason / Rivet 的角色、路由与并行规则 |
| `PROMPT-HANDOFF-STANDARD.md` | 给执行 Agent 的任务 Prompt 与回传格式 |
| `GIT-GITHUB-STANDARD.md` | branch、worktree、commit、remote、exact-SHA、merge 规则 |
| `KNOWLEDGE-ACCUMULATION.md` | 什么值得沉淀、如何沉淀、如何淘汰过期经验 |
| `CODEX-RULES.md` | Axiom（Codex GPT-5.6 Sol）的专项使用规则 |
| `AGENTS.md` | Agent 进入本仓库时必须遵循的入口说明 |
| `JOURNAL.md` | 只记录这套工程规范本身的重大变更 |
| `decisions/` | 需要长期保留理由的高影响工程决策 |
| `patterns/` | 已验证、可跨项目复用的工程模式 |

## 核心原则

- 质量优先，不为了并行而并行，也不为了节省高能力模型额度而牺牲质量。
- 默认让 Mason / Rivet 承担大部分明确、可执行的工程工作；Axiom 只进入真正需要深水能力的任务。
- Curator 负责项目经理、架构协调、任务拆解、技术判断、最终 Review 与合并决策。
- 用户作为项目负责人，主要负责传递任务与结果，不需要在 Agent 之间替系统做技术方案选择。
- 每次只推进清晰的一步；上一阶段未验收前，不把下一阶段当成既成事实。
- 多 Agent 并行必须满足无共享可变状态、无文件重叠、无分支依赖；同一关键区域坚持一个 Writer。
- 高风险工作以 remote exact SHA 为 Review 对象；Agent 自报“已完成”不能替代核验。

## 本仓库记录什么

记录：长期协作规范、稳定工程习惯、可复用经验、质量门槛、重要架构/流程决策。

不记录：每月个人计划、单项目状态表、原始聊天、完整 Prompt 历史、普通 commit 日志、临时实验、客户/业务数据、账号、密钥或敏感日志。

当一条新习惯被证明具有跨项目价值时，更新对应标准；当一个经验已经经过验证并可复用时，写入 `patterns/`；当一个决策未来很可能再次被问“为什么这样选”时，写入 `decisions/`。
