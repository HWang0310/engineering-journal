# Agent Operating Model

本文件定义跨项目稳定的角色类型、engineer identity、backend routing 与 staffing。目标是防止身份混乱，而不是建立组织架构管理系统。

## 1. 全局角色类型

| Role | 默认职责 |
| --- | --- |
| **Project Manager Role** | 技术判断、拆解、routing、Review、merge gate、Owner-facing closure |
| **Deep Engineering Role** | 高风险架构、Contract、复杂调试、深 Review |
| **Execution Role** | 常规实现、测试、Git/docs/config |
| **Owner** | 产品目标、优先级、风险接受、不可逆业务决定 |

项目可以使用 Primary / Secondary 等更细 Execution Role，但不是所有项目必需。

## 2. Engineer identity != backend（canonical）

**工程师身份**与**执行 backend / model**是两个不同概念。

- 真正的 identity add / remove / rename 属于人员变化，需要 Owner 决定。
- 同一个既有 engineer 改用 TeleAgent、HY4、Codex 或其它 backend，原则上属于 PM capability routing，**不自动构成新增人员**，不需要 Owner 逐次批准。
- backend mapping 是 operational metadata，PM 可以更新；只有 identity 本身发生增删改名时才进入 Owner personnel decision。
- temporary Reviewer / specialist 不因为参与一次任务就自动成为长期 project engineer。
- backend 切换不得静默改变任务 ownership；谁是 Writer / Reviewer 仍需清楚。

## 3. Roster 只在有长期身份价值时使用

Roster 是解决跨会话身份漂移的工具，不是所有项目的默认组织图。

### Lean Project

单 Agent、PM + 1 Writer、短生命周期或低复杂度项目可以只使用：

```text
PM
Writer
```

也可以完全不使用拟人化角色名。无需为了模型名称创建长期 engineer identity。

### Durable named roster

只有项目确实长期使用多个命名工程师、且这些 identity 会跨会话/跨阶段复用时，才要求 durable named roster。

如果项目已有 durable named roster：

- 必须沿用现有名字；
- GitHub 中维护一个 canonical roster source；
- 记录至少：project role/name、active/replaced state；backend mapping 可记录但不是 identity；
- rename/replacement history 只在发生时记录；
- 其它 README/HANDOFF/Issue 不复制完整 roster。

新项目没有长期 named roster 需求时，不创建 roster bureaucracy。

### 3.1 Project Roster Memory（canonical）

当 durable named roster **确实存在**时，GitHub project memory 是 identity truth。新会话必须先恢复已有 named identities，再命名长期 engineer。

历史项目有稳定 named identities 但尚未落 GitHub 时，在下一次自然维护相关 project memory 时 backfill；不要求批量 migration，不阻塞无关 Fast Path。

### 3.2 Project-scoped Engineer Identity Boundary（canonical）

Engineer identity 是 **project-scoped durable identity**。一个 named engineer 只有属于**当前正在管理的 project scope**，并存在于该项目的 canonical roster，才可以作为当前项目 engineer 参与 named staffing / dispatch。

> **Engineer identity is project-scoped. A named engineer recorded in project A does not automatically become an engineer of project B, even when A is a dependency, plugin, subproject, parent project, or sibling project of B. Reading another project's repository or roster does not change the current project's staffing. A named engineer may only be treated as a current-project engineer when that identity exists in the current project's canonical roster, or when the Owner explicitly approves adding it.**

#### Project scope 先于 roster resolution

Repository boundary 与 project boundary 不是同一个概念。一个 project 可以显式包含多个 repositories，并共享一个 declared canonical roster；但**不能仅凭 repository dependency / plugin / parent-child / sibling / product-ecosystem 关系推断 roster 共享**。

因此在使用 named engineer 前必须先 resolve：

1. 当前正在管理的 `current_project`；
2. 当前项目 declared canonical roster source（如果存在 durable named roster）；
3. 该 roster 中的 engineer identities；
4. 最后才进行 named staffing / dispatch。

如果多个 repositories 确实属于同一个 project scope，必须由当前项目的 GitHub project-control facts 明确表达 shared project / shared canonical roster；没有这种明确声明时，默认按**独立 project scopes**处理。

#### 不自动继承 engineer identity 的来源

以下来源中的 named engineer **不得自动加入当前项目 roster**：

- dependency repository；
- plugin repository；
- 独立 subproject；
- sibling project；
- parent project；
- 当前 project-control scope 之外的其它独立 project/repository；
- 历史聊天、其它 ChatGPT session 或其它项目 handoff 中出现的 roster。

项目 A 依赖项目 B，不意味着 B 的 engineer 成为 A 的 engineer。PM / Agent 为了解决 A 的问题打开 B 的 repository、读取 B 的 `AGENTS.md` / project memory / roster，也不会改变 A 的 staffing。

#### Dispatch invariant

在派发 named engineer 前必须满足：

```text
current_project = resolved
current_project.canonical_roster = resolved
engineer_identity in current_project.canonical_roster
```

如果不满足：

- 不得把该 named engineer 当作当前项目 engineer 直接派工；
- 不得因为在另一个 repo / project 中看到了该 identity 就使用；
- 不得通过 backend routing、模型切换或 temporary staffing 绕过 roster membership；
- 如果希望把该 identity 变成当前项目的 durable engineer，必须按当前项目 roster add 规则由 Owner 明确批准，并写入当前项目 canonical roster。

Lean Project 没有 durable named roster 时仍可使用 generic `PM` / `Writer`，本 invariant 不要求为 dependency 或临时协作额外创建 named roster。

#### Cross-project work

Cross-project work 必须区分：

- **current project engineer**：属于 `current_project.canonical_roster`；
- **external / dependency project engineer**：属于另一个 project scope，不因此成为 current project engineer。

当当前项目发现 dependency/plugin project 的问题时，推荐链路：

```text
Current Project PM
→ cross-project handoff / issue to External Project
→ External Project PM resolves its own roster and staffing
→ External Project implements / verifies
→ handback result / PR / release fact
→ Current Project PM performs integration review
```

不得静默简化为：

```text
Current Project → External Project Named Engineer
```

除非当前上下文已经**明确切换到 external project scope**，并按该项目自己的 roster 派工；或者 Owner 已把该 engineer 正式加入 current project roster。

Temporary external Reviewer / specialist 可以作为 external collaborator 参与一次性 review/advice，但不会因此成为 current project durable engineer，也不改变 canonical roster。

## 4. Capability routing

PM 根据任务需要选择 backend，不使用固定模型排名。考虑：

- complexity；
- risk；
- ambiguity；
- blast radius；
- architecture depth；
- verification difficulty；
- availability / quota。

一般：

- 明确、机械、可验证 → ordinary execution resource；
- 中高复杂度、跨文件语义一致性、复杂 recovery/review → high-capability execution/review resource；
- highest-risk architecture / Contract / core runtime / extreme debugging → Deep Engineering resource。

backend 选择可以在同一 engineer identity 下变化。不要因为“这个任务想用更强模型”就新增 project engineer；backend routing 同样不得把另一个 project scope 的 named engineer 变成当前项目 engineer。

## 5. Staffing

PM 只启用当前阶段真正需要的人：

- **0 Writer**：PM 直接分析、Review或做安全的小型管理/remote docs 操作；
- **1 Writer**：默认工程实现配置；
- **2+ Writers**：只有独立工作流且有真实并行收益时；
- **Reviewer / specialist**：按风险临时启用，不要求写入长期 roster。

不要把“有更多 backend 可用”或“另一个依赖项目有更多 named engineers”理解为当前项目自动增加人员。

## 6. Writer / Reviewer ownership

- 同一 shared mutable area 只有一个 Writer。
- Reviewer 默认只读；发现问题退回 Writer，或由 PM 明确 transfer ownership。
- temporary Reviewer 不需要新增 durable identity。
- ownership transfer 是 task-level 状态，不等于 roster change。
- cross-project ownership transfer 必须同时明确 project scope；不能只转移人名而不说明当前管理的是哪个 project。

## 7. Dispatch transparency

如果 Owner 需要手动把 Prompt 发给某个 Agent，PM 应简短告诉 Owner“当前 project / 发给谁 / 使用哪个 backend / 当前目标”。Task ID 仅在达到门槛时说明。

如果 PM 能直接执行或直接调用工具，不为透明度机械增加一轮 Owner relay。

## 8. Anti-patterns

- backend switch = personnel change；
- 临时 Reviewer = durable roster expansion；
- 每个 Task 创建新名字；
- 小项目为了形式维护四人以上角色表；
- 因 quota 机械降级真正 Deep Engineering；
- 因可用 Agent 多就制造假并行；
- Reviewer 静默变 Writer；
- 为一次小任务要求 Owner 批准模型切换；
- 因读取 dependency/plugin repo 的 roster，把 external named engineer 当成 current project engineer；
- 用 backend routing 绕过 project-scoped roster membership。

最终目标：稳定 identity、清晰 project scope、合适 capability、低沟通成本和风险相称的工程吞吐。
