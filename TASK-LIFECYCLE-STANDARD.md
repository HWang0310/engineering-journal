# Task Lifecycle Standard

本文件定义跨项目的正式工程任务标识、派发状态、幂等执行与重复发送恢复规则。

## 1. 哪些任务必须有 Task ID

以下任一条件成立时，任务必须分配唯一且不可变的 Task ID：

- 正式派发给 Axiom / Mason / Rivet 的工程任务；
- 可能跨会话继续、暂停后恢复或由不同 Agent 接手；
- 可能与其他任务并行；
- 重复发送会造成二次开发、重复 commit 或破坏共享状态；
- 需要通过 branch / commit / PR / handoff / project state 追溯；
- 属于重要里程碑、核心实现、高风险修复或跨仓库集成。

极小、一次性、不可重复且无需跨会话追踪的轻量任务可以不分配 Task ID。不要为了形式给每个微小操作编号。

## 2. Task ID 的作用

Task ID 同时承担：

- 工程任务的唯一身份；
- Prompt、Agent 执行、branch/commit/PR、handoff 与 Review 的关联键；
- 跨会话恢复任务状态的索引；
- 防止重复执行的 idempotency key。

推荐使用稳定、简短、可读格式，例如：

`<PROJECT>-<AREA>-<SEQUENCE>`

示例：`APP-AUTH-001`、`CORE-RUNTIME-003`。

具体项目可以定义更细格式，但同一 Task ID 一旦派发后不得复用给另一个目标。

## 3. Prompt 与任务状态必须分开

**写过 Prompt 不等于已经发送；发送不等于已经执行；Agent 自报完成不等于已验收。**

正式任务推荐使用以下生命周期：

```text
DRAFT
→ READY_TO_SEND
→ SENT
→ RUNNING
→ PUSHED / HANDOFF_RECEIVED
→ CURATOR_REVIEW
→ ACCEPTED / NEEDS_CORRECTION / HOLD
→ CLOSED
```

状态含义：

- `DRAFT`：任务仍在设计，不可假定 Agent 已看到。
- `READY_TO_SEND`：Prompt 已完成，可以交给 Owner 转发。
- `SENT`：Owner 已确认发送给目标 Agent。
- `RUNNING`：Agent 已开始实际执行。
- `PUSHED`：工程结果已经 push 到可访问 remote；优先用于 GitHub-native handoff。
- `HANDOFF_RECEIVED`：无法依靠 remote 完成全部验证时，已收到必要 handoff 证据。
- `CURATOR_REVIEW`：Curator 正在独立核验工程事实。
- `ACCEPTED`：Curator Review 结果为 `PASS`，该任务成果可以成为后续阶段事实基础。
- `NEEDS_CORRECTION`：Curator Review 发现明确问题，需要修正。
- `HOLD`：实现可能正确，但缺少证据、依赖或外部条件，暂不可继续。
- `CLOSED`：任务已完成所需集成/收尾，不再需要继续跟踪。

`PASS / HOLD / NEEDS_CORRECTION` 是 **Review 结论**；`ACCEPTED / CLOSED` 是 **任务生命周期状态**。不要混用。

## 4. 特殊恢复状态

### SEND_STATUS_UNKNOWN

当 Curator 或 Owner 无法确认一个正式 Prompt 是否已经发送/执行时，不得直接重发完整任务。

先把任务视为 `SEND_STATUS_UNKNOWN`，执行一次只读状态探测。

### STATUS_PROBE_ONLY

状态探测只能收集事实，不执行原任务、不修改源码、不创建重复实现。

至少检查适用项：

- 目标 branch 是否存在；
- local HEAD / remote HEAD；
- 是否存在未 push commit；
- `git status` / worktree 状态；
- `git log`、commit message、PR、项目状态文件或 handoff 中是否已经出现该 Task ID；
- 是否已经存在可验证的 exact SHA。

探测完成后，Curator 再决定：恢复执行、补 push、进入 Review，或重新派发。

### ALREADY_COMPLETED

如果 Agent 在执行前发现同一 Task ID 已经存在完成且可验证的结果，不得重新执行。

应返回 `ALREADY_COMPLETED`，并提供已有 branch / exact SHA / 验证证据，交给 Curator 判断是否直接进入 Review。

## 5. 正式 Prompt 必须幂等

带 Task ID 的正式 Prompt 应包含幂等预检：

1. 先确认当前 repository / branch / worktree。
2. 检查同一 Task ID 是否已经留下 commit、PR、项目状态或可验证 handoff。
3. 如果已经完成，不再次修改，返回 `ALREADY_COMPLETED`。
4. 如果存在部分实现，先报告现状；除非 Prompt 明确允许恢复，否则不要盲目覆盖或从头重做。
5. 只有确认没有已完成实例后，才执行本轮工程修改。

幂等规则的目标是防止 Owner 误重复发送 Prompt、跨会话状态丢失或 Agent 重启导致二次施工。

## 6. Task ID 应贯穿哪些工程事实

在适用时，Task ID 应尽量出现在：

- 正式 Prompt；
- branch / PR 描述或项目状态记录；
- commit message 或 commit body；
- Agent 完成信号 / handoff；
- Curator Review 记录；
- 跨会话 handoff / project state。

不要求所有项目机械地把 Task ID 塞进每个文件名，但必须保证未来能够从 Task ID 追溯到 remote exact SHA 与 Review 结论。

## 7. Curator 的责任

Curator 负责：

- 判断任务是否达到“必须分配 Task ID”的门槛；
- 生成 Task ID，不把编号工作转嫁给 Owner；
- 区分 Prompt 草稿状态与真实派发状态；
- 在发送状态不明时先做 `STATUS_PROBE_ONLY`；
- 防止同一 Task ID 被两个 Writer 无边界重复施工；
- 根据 remote 事实决定 `PASS / HOLD / NEEDS_CORRECTION`；
- 只有 Review `PASS` 后才把任务标为 `ACCEPTED`。

Owner 不需要维护复杂任务状态表；Curator 应在需要时明确告诉 Owner 当前 Task ID、当前状态以及下一步动作。
