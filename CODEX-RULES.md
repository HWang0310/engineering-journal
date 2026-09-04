# Codex Deep-Engineering Rules

Codex GPT-5.6 Sol 默认承担跨项目的 **Deep Engineering Role**。Deep Engineering Role 是职责类型，不是固定角色名；每个项目应使用自己的项目级角色名，具体映射见 `AGENT-OPERATING-MODEL.md`。

## 1. Codex 是 Deep Engineering resource，不是所有复杂任务的默认答案

Owner 当前还拥有普通 TeleAgent execution resources 与 WorkBuddy HY4 高能力工程资源。因此 Project Manager Role 不应把 routing 简化成：

`普通 TeleAgent 不够 -> Codex`

对于中高复杂度、需要较强语义理解、Release Truth / Project Memory / governance consistency、复杂 integration / recovery / Review 等任务，如果 WorkBuddy HY4 能可靠完成，应优先考虑 HY4，以减少对更稀缺 Codex quota 的机械消耗。

但这不是固定逐级升级流程，也不是绝对能力排名。任务从一开始就属于 Deep Engineering 时应直接 Codex，不要求先由 TeleAgent 或 HY4 失败一次。

## 2. 什么时候使用 Deep Engineering Role

优先用于：

- 最高风险架构与 Contract 设计；
- 核心 runtime；
- 重大跨 repository / Core integration；
- 高 blast-radius 系统边界修改；
- 长时间无法稳定定位的极复杂 bug；
- exact-SHA 高风险审查、pin 前审查；
- 需要同时深入理解架构、实现、测试和边界条件的深度 Review；
- Project Manager Role 判断普通 Execution resource 或 WorkBuddy HY4 已不足以可靠承担的深水任务。

如果任务本质需要 Deep Engineering Role，不得仅因为 HY4 quota 相对充裕而降低能力等级。

## 3. 什么时候不要优先使用 Codex

不优先用于：

- 机械 Git 操作；
- 常规文档修改；
- 已有明确方案的重复性实现；
- 普通配置调整；
- 可以通过高质量 Prompt + 明确验收可靠交给普通 TeleAgent 的工作；
- WorkBuddy HY4 能可靠完成、且风险尚未达到 Deep Engineering 门槛的中高复杂度执行 / Review 工作。

原则：**Codex 能歇则歇，但质量和风险需要时必须用。** quota 是 routing 因素，不是能力降级理由。

## 4. Project Manager Role 的 routing 判断

决定是否使用 Codex 时至少考虑：

- complexity；
- risk；
- ambiguity；
- blast radius；
- architecture depth；
- verification difficulty；
- 当前可用高能力资源；
- 当前 quota / availability。

如果任务只是“普通执行能力不足”，先重新判断是否适合 WorkBuddy HY4 等高能力 engineering resource；如果任务已经明确触及 Deep Engineering 边界，可以直接使用 Codex。

## 5. 接任务前

Project Manager Role 应尽量给出：

- 当前架构背景与明确问题；
- 已验证的 remote branch / exact SHA；
- 已尝试方案与失败证据（如有）；
- 允许/禁止修改范围；
- 本次需要做设计、实现、调试还是审查；
- 验收标准与输出格式；
- 当前项目赋予 Deep Engineering Role 的项目角色名；
- 本次实际 backend 为 Codex GPT-5.6 Sol。

在给 Owner 正式 Prompt 前，应明确告诉 Owner 本任务实际交给 Codex，而不是只给出抽象项目角色名。

## 6. 做 Review 时

- Review 对象尽可能绑定 exact SHA。
- 先验证事实，再接受 Writer handoff 自述。
- 重点寻找系统性风险，不只看表面代码风格。
- 对高风险改动给出可执行 PASS 条件。
- `NEEDS_CORRECTION` 时指出最小必要修正范围，不无故扩展重构。
- 继续遵守 **one Writer + read-only Reviewer**；Codex 作为 Reviewer 时不得静默切换为 Writer，需要修改时由 Project Manager Role 显式完成 ownership transfer。

## 7. 输出

回传至少包含：

- 项目角色名与 Task ID；
- 结论与状态建议；
- 关键风险/根因；
- 修改或建议修改的文件/边界；
- 验证证据；
- branch / exact SHA（如有代码变更）；
- 是否可以进入 merge / pin / 下一阶段。

Deep Engineering Role 的价值在于解决真正深水问题和提高最高风险环节确定性，而不是承担所有“稍微复杂”的任务。