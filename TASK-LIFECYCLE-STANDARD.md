# Task Lifecycle Standard

本文件只定义 **Task ID threshold、幂等恢复与需要 durable task identity 时的生命周期语义**。执行深度由 `ENGINEERING-STANDARDS.md` §4 决定。

## 1. Task ID threshold（canonical）

Task ID 只在它提供真实恢复/追踪价值时要求。以下任一条件成立时应分配唯一 Task ID：

- 跨会话继续、暂停后恢复或可能换 Agent 接手；
- multi-Agent / 并行执行；
- branch / PR 需要长期追踪或未来需要明确 recovery key；
- high-risk / Deep Path；
- 重要 milestone；
- release / Contract / API/schema / migration；
- 重复执行会造成明显破坏、重复发布、重复数据变更或共享状态损坏；
- PM 判断未来恢复、审计或依赖链需要 durable task identity。

**以下本身不是 Task ID trigger：**

- 仅仅“派给了 Agent”；
- 普通一次性 docs fix；
- small bug / small config / small refactor / formatting / simple test fix；
- 创建了一个短生命周期 commit 或 PR；
- PM 能在当前会话和当前 PR 中完整恢复上下文。

没有 Task ID 的 Fast Path 可以直接：

`Prompt → Execute → Verify → Commit/PR（需要时）→ PM Review`

不要为了形式给每个 Agent 动作编号。

## 2. Task ID 的作用

Task ID 是 durable recovery / idempotency key，不是流程徽章。使用时应能从 Task ID 追溯到适用的 branch / PR / exact SHA / Review 结论。

推荐格式：`<PROJECT>-<AREA>-<SEQUENCE>`。同一 ID 不复用给不同目标。

## 3. Lifecycle 不是每个任务的必填状态机

只有需要 durable lifecycle tracking 的任务才使用状态语义。推荐状态：

```text
DRAFT → SENT/RUNNING → PUSHED → PM_REVIEW → ACCEPTED | NEEDS_CORRECTION | HOLD → CLOSED
```

项目不需要为了小任务逐项登记所有中间状态。GitHub PR/Issue 已能表达状态时，不重复维护第二套状态表。

`PASS / HOLD / NEEDS_CORRECTION` 是 Review 结论，不等同生命周期状态。

## 4. 幂等与恢复

### SEND_STATUS_UNKNOWN

只有在同一 durable task 可能被重复派发、且发送状态确实不明时使用。先做只读状态探测，不直接重发高成本/高风险任务。

### STATUS_PROBE_ONLY

检查适用事实：branch、remote HEAD、PR、Task ID、已有 commit、未 push work、必要 project state。只读，不重新施工。

### ALREADY_COMPLETED

带 Task ID 的任务若已存在可验证等价成果，应返回已有 branch / SHA / PR / evidence，而不是重复施工。

Fast Path 无 Task ID 时，不要求人为模拟这些状态；直接以当前 Git/PR facts 判断是否已经做过。

## 5. Prompt 与 Task ID

带 Task ID 的 Standard / High-risk Prompt 应做与风险相称的幂等预检。Fast Prompt 无 Task ID 时不需要完整幂等模板。

Task ID 可出现在 branch、PR、commit、handoff、project state 中的**必要位置**，但不要求机械复制到所有对象。

## 6. Owner 与 PM

- Task ID 由 PM 决定和维护。
- Owner 不维护编号表或生命周期状态机。
- 只有 Task ID 对 Owner 当前决策有帮助时才在主回复中强调；否则保留为工程 supporting evidence。
