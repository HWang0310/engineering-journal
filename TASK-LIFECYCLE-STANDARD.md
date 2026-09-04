# Task Lifecycle Standard

本文件定义跨项目的正式工程任务标识、派发状态、幂等执行与重复发送恢复规则。

## 1. 哪些任务必须有 Task ID

以下任一条件成立时，任务必须分配唯一且不可变的 Task ID：

- 正式派发给任一执行 Agent 或 Deep Engineering Role 的工程任务；
- 可能跨会话继续、暂停后恢复或由不同 Agent 接手；
- 可能与其他任务并行；
- 重复发送会造成二次开发、重复 commit 或破坏共享状态；
- 需要通过 branch / commit / PR / handoff / project state 追溯；
- 属于重要里程碑、核心实现、高风险修复或跨仓库集成。

极小、一次性、不可重复且无需跨会话追踪的轻量任务可以不分配 Task ID。不要为了形式给每个微小操作编号。

## 2. Task ID 的作用

Task ID 同时承担：工程任务唯一身份、Prompt/执行/Git/Review 关联键、跨会话恢复索引和防止重复执行的 idempotency key。

推荐格式：`<PROJECT>-<AREA>-<SEQUENCE>`。具体项目可以定义更细格式，但同一 Task ID 一旦派发后不得复用给另一个目标。

## 3. Prompt 与任务状态必须分开

**写过 Prompt 不等于已经发送；发送不等于已经执行；Agent 自报完成不等于已验收。**

跨项目推荐使用以下语义状态：

```text
DRAFT
→ READY_TO_SEND
→ SENT
→ RUNNING
→ PUSHED / HANDOFF_RECEIVED
→ PM_REVIEW
→ ACCEPTED / NEEDS_CORRECTION / HOLD
→ CLOSED
```

- `DRAFT`：任务仍在设计。
- `READY_TO_SEND`：Prompt 已完成，可以转发。
- `SENT`：Owner 已确认发送。
- `RUNNING`：Agent 已开始执行。
- `PUSHED`：结果已 push 到可访问 remote。
- `HANDOFF_RECEIVED`：已收到 GitHub 之外必要证据。
- `PM_REVIEW`：Project Manager Role 正在独立核验工程事实。
- `ACCEPTED`：Review 结果为 `PASS`，成果可成为后续事实基础。
- `NEEDS_CORRECTION`：发现明确问题，需要修正。
- `HOLD`：缺证据、依赖或外部条件，暂不可继续。
- `CLOSED`：任务完成所需集成/收尾，不再跟踪。

项目可以把 `PM_REVIEW` 显示成 `<Project PM Name>_REVIEW`，例如项目为了辨识度使用自己的 PM 角色名；但必须明确与跨项目语义 `PM_REVIEW` 等价。

`PASS / HOLD / NEEDS_CORRECTION` 是 Review 结论；`ACCEPTED / CLOSED` 是生命周期状态，不要混用。

## 4. 特殊恢复状态

### SEND_STATUS_UNKNOWN

当 Project Manager Role 或 Owner 无法确认正式 Prompt 是否已发送/执行时，不得直接重发完整任务。先视为 `SEND_STATUS_UNKNOWN`，执行只读状态探测。

### STATUS_PROBE_ONLY

状态探测只能收集事实，不执行原任务、不修改源码、不创建重复实现。至少检查适用项：目标 branch、local/remote HEAD、未 push commit、`git status`、worktree、Task ID、PR/project state/handoff 和已有 exact SHA。

探测完成后，由 Project Manager Role 决定恢复执行、补 push、进入 Review 或重新派发。

### ALREADY_COMPLETED

Agent 执行前发现同一 Task ID 已存在完成且可验证结果时，不得重新执行。应返回 `ALREADY_COMPLETED`，并提供已有 branch / exact SHA / 验证证据。

## 5. 正式 Prompt 必须幂等

带 Task ID 的正式 Prompt 应包含：

1. 确认 repository / branch / worktree。
2. 检查同一 Task ID 是否已有 commit、PR、项目状态或可验证 handoff。
3. 已完成则返回 `ALREADY_COMPLETED`，不再次修改。
4. 存在部分实现时先报告现状；除非 Prompt 明确允许恢复，否则不要盲目覆盖或从头重做。
5. 只有确认没有已完成实例后才执行本轮修改。

## 6. Task ID 应贯穿哪些工程事实

在适用时，Task ID 应尽量出现在正式 Prompt、branch/PR 描述或项目状态、commit message/body、Agent 完成信号/handoff、Project Manager Review 记录和跨会话 handoff/project state 中。

不要求机械地把 Task ID 塞进每个文件名，但必须保证未来能从 Task ID 追溯到 remote exact SHA 与 Review 结论。

## 7. Project Manager Role 的责任

Project Manager Role 负责：

- 判断任务是否达到必须分配 Task ID 的门槛；
- 生成 Task ID，不把编号工作转嫁给 Owner；
- 区分 Prompt 草稿状态与真实派发状态；
- 发送状态不明时先做 `STATUS_PROBE_ONLY`；
- 防止同一 Task ID 被两个 Writer 无边界重复施工；
- 根据 remote 事实决定 `PASS / HOLD / NEEDS_CORRECTION`；
- 只有 Review `PASS` 后才把任务标为 `ACCEPTED`。

Owner 不需要维护复杂任务状态表；Project Manager Role 应在需要时明确告诉 Owner 当前 Task ID、当前状态以及下一步动作，并使用该项目自己的角色名进行沟通。