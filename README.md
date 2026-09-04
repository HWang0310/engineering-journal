# Engineering Journal

`engineering-journal` 是跨项目工程规范与可复用工程知识的长期仓库。

它不是任务清单、项目看板、聊天记录归档或代码仓库。它只保存未来项目仍值得复用的工程规则、协作方式、质量门槛、Git/GitHub 规范和经验模式。

## 给新 ChatGPT 会话：最短启动方式

以后启动一个新项目时，用户可以只说：

> 请参考 https://github.com/HWang0310/engineering-journal 的工程规范，开发 XXX 项目。

这句话是完整的**工程协作启动指令**。新会话必须读取 `NEW-SESSION-BOOTSTRAP.md` 并自动承担 Project Manager Role。

### 新会话启动语提醒

需要新开项目会话、进行 handoff 或为了隔离上下文新开会话时，Project Manager Role 必须主动给 Owner 完整可复制启动语；已知项目名和仓库时直接补全，不依赖 Owner 自己记忆。

## 本地项目目录原则

所有项目的本地统一根目录为：

`/Users/hwang/Movies/Program`

一个项目对应该目录下一个项目子文件夹。所有项目相关代码、文档、脚本、clone、worktree、测试产物和临时工程活动都应留在该项目文件夹边界内。需要长期保留的工程成果必须进入对应 Git repository 并 commit / push，GitHub remote 仍是 canonical truth。

单仓库项目可以让项目目录本身直接作为 repo working tree；多仓库项目则把各 GitHub repo 放在同一个项目目录下。详细规则见 `LOCAL-WORKSPACE-STANDARD.md`。

## 角色命名与 Project roster 原则

**全局只固定角色类型和职责，不固定角色名字。**

跨项目稳定的是：

- Project Manager Role
- Deep Engineering Role
- Primary Execution Role
- Secondary Execution Role
- Owner

每个项目建立自己的 project engineer roster。新项目在启动阶段一次性为实际参与的工程师建立项目专属名字；已有项目必须沿用既有 roster，不因为新 Task、新阶段、新 backend 或任务复杂度变化临时创造新的工程师名字。

项目工程师名字与身份默认固定。只有 Owner 明确新增、替换或调整工程师时才改变 roster；Project Manager Role 可以提出人员调整建议，但不能未经 Owner 确认自行扩充。

`0 / 1 / 2 名执行工程师` 指当前阶段从现有 roster 中实际启用的 Writer 数量，**不是 project roster 总人数，也不是创建新工程师身份的许可**。

具体 Agent/backend 与角色类型分开管理。WorkBuddy HY4 是 Owner 当前可用的高能力 execution / review backend，不是新的全局角色名或所有项目必须存在的 Role。详细规则见 `AGENT-OPERATING-MODEL.md`。

## Capability routing

Project Manager Role 不应只在普通 TeleAgent 与 Codex 之间二选一。

当前默认资源路由语义：

- **普通 TeleAgent execution resources**：明确、机械、步骤清楚、可验证的常规执行。
- **WorkBuddy HY4**：中高复杂度、较高语义理解与一致性要求、Release Truth、Project Memory、governance consistency、复杂 integration / recovery / Review 等任务。
- **Codex GPT-5.6 Sol / Deep Engineering resource**：最高风险架构、Contract、核心 runtime、重大跨 repo Core integration、极复杂 debugging 等真正深水任务。

这不是绝对能力排名或固定三级组织结构。Project Manager Role 应结合 complexity、risk、ambiguity、blast radius、architecture depth、verification difficulty 与当前 quota/availability 动态路由。

HY4 当前 quota 相对充裕，因此 HY4 能可靠完成的中高复杂度任务应优先考虑 HY4，以减少对更稀缺 Codex quota 的机械消耗；但质量与风险始终优先于 quota，真正 Deep Engineering 任务不能为了省 Codex 而降级。

Capability routing 优先在当前项目**已有 roster** 内进行。如果最适合的 backend 尚未加入该项目，Project Manager Role 应先向 Owner 提出新增/替换工程师建议，而不是临时取新名字直接派活。

**每次给工程 Agent 派正式任务时，Project Manager Role 必须先告诉 Owner：既有项目工程师名 + 实际 Agent/backend + Task ID。**

## Source of truth

工程事实默认按以下优先级判断：

1. GitHub remote 上可验证的 branch / commit / exact SHA。
2. 具体项目仓库中的代码、测试和项目级文档。
3. `/Users/hwang/Movies/Program/<project>/` 下的本地 checkout / worktree，仅视为 workspace。
4. ChatGPT、Codex、TeleAgent、WorkBuddy HY4 等 Agent 的口头说明或 handoff；remote 与 exact SHA 未核验前不视为最终事实。

因此：**GitHub 是长期 canonical memory，本地项目目录是统一 workspace。**

## 新会话必读顺序

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

涉及知识沉淀、工程流程演进或长期决策时，再读取 `KNOWLEDGE-ACCUMULATION.md`、相关 `decisions/` 和 `patterns/`。

## 核心文档

| 文件 | 用途 |
| --- | --- |
| `NEW-SESSION-BOOTSTRAP.md` | 新会话如何进入 Project Manager Role 并恢复项目规范 |
| `ENGINEERING-STANDARDS.md` | 跨项目总工程原则、执行节奏与 Definition of Done |
| `RESTRICTED-CONTENT-STANDARD.md` | Owner 指定的跨项目不可放宽内容禁令与验收 gate |
| `LOCAL-WORKSPACE-STANDARD.md` | `/Users/hwang/Movies/Program` 下的项目目录、repo 映射与 worktree 管理 |
| `AGENT-OPERATING-MODEL.md` | 角色类型、稳定 project roster、项目级角色命名、backend capability routing、工程师数量与并行规则 |
| `TASK-LIFECYCLE-STANDARD.md` | Task ID、状态机、幂等执行与重复发送恢复规则 |
| `PROMPT-HANDOFF-STANDARD.md` | Agent Prompt、dispatch 信息、GitHub-native handoff 与人工 handoff 兜底规范 |
| `GIT-GITHUB-STANDARD.md` | branch、worktree、commit、remote、exact-SHA、merge 规则 |
| `KNOWLEDGE-ACCUMULATION.md` | 知识沉淀、升级和淘汰规则 |
| `CODEX-RULES.md` | Deep Engineering Role 使用 Codex 时的专项规则 |
| `AGENTS.md` | Agent 使用本仓库作为规范来源时必须遵循的入口说明 |
| `JOURNAL.md` | 只记录工程规范体系的重大变化 |

## 核心原则

- 质量优先，不为了并行而并行，也不为了节省高能力模型额度而牺牲质量。
- 一个项目对应 `/Users/hwang/Movies/Program` 下一个项目目录；项目工程活动不散落到该边界之外。
- durable engineering files 必须进入对应 Git repo 并 push；本地 workspace 不替代 GitHub canonical truth。
- 项目角色名项目化；项目 engineer roster 建立后默认固定，不为每个任务临时新建工程师身份。
- 能机械结构化的任务优先普通 Execution resource；中高复杂度可优先考虑 HY4；真正 Deep Engineering 使用 Codex 或相应深水资源。
- Project Manager Role 负责 stage staffing、backend routing、技术判断、最终 Review 与 merge gate；但新增/替换项目工程师需要 Owner 明确确认。
- 每次正式派工都告诉 Owner实际是哪个既有项目工程师/backend。
- Owner 主要负责目标、优先级和必要任务/结果传递，不承担默认技术方案选择。
- restricted-content hard gate 不可被项目级规则放宽，任何命中都不能 `PASS`。
- 正式任务使用 Task ID 串联 Prompt、执行、Git 结果与 Review，并以 Task ID 做幂等键。
- Prompt 草稿、实际发送、执行、Review 和验收是不同状态。
- 每次只推进清晰的一步；上一阶段未验收前，不把下一阶段建立在未确认结果上。
- 多 Agent 只有真正独立时才并行；共享关键区域坚持 one Writer + read-only Reviewer。
- 能直接从 GitHub 恢复工程事实时，Owner 只需简短完成信号，Project Manager Role 自行核验 remote。
- 高风险工作以 remote exact SHA 为 Review 对象；Agent 自报完成不能替代核验。

## 本仓库记录什么

记录：长期协作规范、稳定工程习惯、可复用经验、质量门槛、重要架构/流程决策。

不记录：每月个人计划、单项目状态表、原始聊天、完整 Prompt 历史、普通 commit 日志、临时实验、客户/业务数据、账号、密钥或敏感日志。
