# Engineering Journal

`engineering-journal` 是跨项目工程规范与可复用工程知识的长期仓库。

它不是任务清单、项目看板、聊天记录归档或代码仓库。它只保存未来项目仍值得复用的工程规则、协作方式、质量门槛、Git/GitHub 规范和经验模式。

## 给新 ChatGPT 会话：最短启动方式

以后启动一个新项目时，用户可以只说：

> 请参考 https://github.com/HWang0310/engineering-journal 的工程规范，开发 XXX 项目。

这句话应被视为一个完整的**工程协作启动指令**，而不是普通的“参考资料”请求。

新的 ChatGPT 会话在继续项目工作前，必须先读取 [`NEW-SESSION-BOOTSTRAP.md`](NEW-SESSION-BOOTSTRAP.md)，并按照其中的启动协议自动进入 **Curator 项目经理模式**。

### 新会话启动语提醒

用户不需要自己记住上面的启动语。只要 Curator 判断接下来适合**新开一个 ChatGPT 项目会话、为新项目建立独立会话、或把当前项目 handoff 到一个全新 ChatGPT 会话**，Curator 必须主动给 Owner 一条可直接复制的新会话启动语：

> 请参考 https://github.com/HWang0310/engineering-journal 的工程规范，开发 XXX 项目。

如果项目名已知，必须把 `XXX` 直接替换成真实项目名，不要让 Owner 自己改占位符。如果目标项目仓库 URL 已知，可在下一句一并附上项目仓库地址。

这个提醒属于 Curator 的项目管理职责：**需要新开会话时主动提醒，不依赖 Owner 自己记忆。**

进入该模式后，不需要用户重新解释以下内容：

- Curator / Axiom / Mason / Rivet 的角色与职责；
- Mason / Rivet 为默认主力、Axiom 负责深水任务的能力路由；
- 当前阶段应使用 0 / 1 / 2 名执行工程师，还是额外加入 Axiom；
- TeleAgent Prompt 必须结构化、低歧义、分步骤并带验收；
- 正式任务的 Task ID、任务状态与幂等执行规则；
- restricted-content hard gate；
- 一步一验收与 `PASS / HOLD / NEEDS_CORRECTION`；
- 多 Agent 只有在真正独立时才并行；
- one Writer / isolated worktree；
- GitHub remote 与 exact SHA 为工程事实基线；
- GitHub-native handoff 优先，不让 Owner 搬运长技术 handoff；
- 高风险工作需要 exact-SHA Review；
- 用户主要负责目标、优先级和任务/结果传递，技术判断由 Curator 负责。

如果目标项目的信息已经足够，Curator 应直接开始分析并决定第一步，而不是再次询问用户想采用哪套协作方式。

## Source of truth

工程事实默认按以下优先级判断：

1. GitHub remote 上可验证的 branch / commit / exact SHA。
2. 具体项目仓库中的代码、测试和项目级文档。
3. 本地 checkout / worktree，仅视为工作副本。
4. ChatGPT、Codex、TeleAgent 的口头说明或 handoff；在 remote 与 exact SHA 未核验前，不视为最终事实。

因此：**GitHub 是长期 canonical memory，本地目录只是 workspace。**

## 新会话必读顺序

当本仓库被引用为某个项目的工程规范时，至少按以下顺序读取：

1. `README.md`
2. `NEW-SESSION-BOOTSTRAP.md`
3. `ENGINEERING-STANDARDS.md`
4. `RESTRICTED-CONTENT-STANDARD.md`
5. `AGENT-OPERATING-MODEL.md`
6. `TASK-LIFECYCLE-STANDARD.md`
7. `PROMPT-HANDOFF-STANDARD.md`
8. `GIT-GITHUB-STANDARD.md`
9. `CODEX-RULES.md`

涉及知识沉淀、工程流程演进或长期决策时，再读取 `KNOWLEDGE-ACCUMULATION.md`、`decisions/` 和相关 `patterns/`。

## 核心文档

| 文件 | 用途 |
| --- | --- |
| `NEW-SESSION-BOOTSTRAP.md` | 新 ChatGPT / Agent 会话如何自动进入 Curator 项目经理模式 |
| `ENGINEERING-STANDARDS.md` | 跨项目总工程原则、执行节奏与 Definition of Done |
| `RESTRICTED-CONTENT-STANDARD.md` | Owner 指定的跨项目不可放宽内容禁令与验收 gate |
| `AGENT-OPERATING-MODEL.md` | Curator / Axiom / Mason / Rivet 的角色、路由、工程师数量与并行规则 |
| `TASK-LIFECYCLE-STANDARD.md` | 正式任务的 Task ID、状态机、幂等执行与重复发送恢复规则 |
| `PROMPT-HANDOFF-STANDARD.md` | 给执行 Agent 的 Prompt、GitHub-native handoff 与人工 handoff 兜底规范 |
| `GIT-GITHUB-STANDARD.md` | branch、worktree、commit、remote、exact-SHA、merge 规则 |
| `KNOWLEDGE-ACCUMULATION.md` | 什么值得沉淀、如何沉淀、如何淘汰过期经验 |
| `CODEX-RULES.md` | Axiom（Codex GPT-5.6 Sol）的专项使用规则 |
| `AGENTS.md` | Agent 使用本仓库作为规范来源时必须遵循的入口说明 |
| `JOURNAL.md` | 只记录这套工程规范本身的重大变更 |
| `decisions/` | 需要长期保留理由的高影响工程决策 |
| `patterns/` | 已验证、可跨项目复用的工程模式 |

## 核心原则

- 质量优先，不为了并行而并行，也不为了节省高能力模型额度而牺牲质量。
- 默认让 Mason / Rivet 承担大部分明确、可执行的工程工作；Axiom 只进入真正需要深水能力的任务。
- Curator 负责项目经理、架构协调、任务拆解、工程师配置、技术判断、最终 Review 与合并决策。
- 用户作为项目负责人，主要负责传递任务与结果，不需要在 Agent 之间替系统做技术方案选择。
- restricted-content hard gate 是不可被项目级规则放宽的全局约束，任何命中都不能 `PASS`。
- 正式工程任务用 Task ID 串联 Prompt、执行、Git 结果与 Review，并以 Task ID 做幂等键避免重复施工。
- Prompt 草稿、实际发送、Agent 执行和 Curator 验收是不同状态，不允许混为一谈。
- 每次只推进清晰的一步；上一阶段未验收前，不把下一阶段建立在未确认结果上。
- 多 Agent 并行必须满足无共享可变状态、无文件重叠、无分支依赖；同一关键区域坚持一个 Writer。
- 能直接从 GitHub 恢复工程事实时，Owner 只需提供简短完成信号，Curator 自行核验 remote。
- 高风险工作以 remote exact SHA 为 Review 对象；Agent 自报“已完成”不能替代核验。

## 本仓库记录什么

记录：长期协作规范、稳定工程习惯、可复用经验、质量门槛、重要架构/流程决策。

不记录：每月个人计划、单项目状态表、原始聊天、完整 Prompt 历史、普通 commit 日志、临时实验、客户/业务数据、账号、密钥或敏感日志。

当一条新习惯被证明具有跨项目价值时，更新对应标准；当一个经验已经经过验证并可复用时，写入 `patterns/`；当一个决策未来很可能再次被问“为什么这样选”时，写入 `decisions/`。
