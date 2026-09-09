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

backend 选择可以在同一 engineer identity 下变化。不要因为“这个任务想用更强模型”就新增 project engineer。

## 5. Staffing

PM 只启用当前阶段真正需要的人：

- **0 Writer**：PM 直接分析、Review或做安全的小型管理/remote docs 操作；
- **1 Writer**：默认工程实现配置；
- **2+ Writers**：只有独立工作流且有真实并行收益时；
- **Reviewer / specialist**：按风险临时启用，不要求写入长期 roster。

不要把“有更多 backend 可用”理解为“项目必须增加人员”。

## 6. Writer / Reviewer ownership

- 同一 shared mutable area 只有一个 Writer。
- Reviewer 默认只读；发现问题退回 Writer，或由 PM 明确 transfer ownership。
- temporary Reviewer 不需要新增 durable identity。
- ownership transfer 是 task-level 状态，不等于 roster change。

## 7. Dispatch transparency

如果 Owner 需要手动把 Prompt 发给某个 Agent，PM 应简短告诉 Owner“发给谁 / 使用哪个 backend / 当前目标”。Task ID 仅在达到门槛时说明。

如果 PM 能直接执行或直接调用工具，不为透明度机械增加一轮 Owner relay。

## 8. Anti-patterns

- backend switch = personnel change；
- 临时 Reviewer = durable roster expansion；
- 每个 Task 创建新名字；
- 小项目为了形式维护四人以上角色表；
- 因 quota 机械降级真正 Deep Engineering；
- 因可用 Agent 多就制造假并行；
- Reviewer 静默变 Writer；
- 为一次小任务要求 Owner 批准模型切换。

最终目标：稳定 identity、合适 capability、低沟通成本和风险相称的工程吞吐。
