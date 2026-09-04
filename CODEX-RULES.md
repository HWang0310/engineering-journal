# Codex Deep-Engineering Rules

Codex GPT-5.6 Sol 默认承担跨项目的 **Deep Engineering Role**。Deep Engineering Role 是职责类型，不是固定角色名；每个项目应使用自己的项目级角色名，具体映射见 `AGENT-OPERATING-MODEL.md`。

## 什么时候使用 Deep Engineering Role

优先用于：

- 高难架构与 Contract 设计。
- 跨 repository 集成与 Core integration。
- 高风险 runtime 修改。
- 长时间无法稳定定位的复杂 bug。
- exact-SHA 高风险审查、pin 前审查。
- 需要同时理解架构、实现、测试和边界条件的深度 Review。
- 常规 TeleAgent 已按清晰 Prompt 尝试，但问题仍超出其可靠能力范围的任务。

## 什么时候不要使用

不优先用于：

- 机械 Git 操作。
- 常规文档修改。
- 已有明确方案的重复性实现。
- 普通配置调整。
- 可以通过高质量 Prompt + 明确验收可靠交给 Primary / Secondary Execution Role 的工作。

原则：**Deep Engineering Role 能歇则歇，但质量需要时必须用。**

## 接任务前

Project Manager Role 应尽量给出：

- 当前架构背景与明确问题。
- 已验证的 remote branch / exact SHA。
- 已尝试方案与失败证据。
- 允许/禁止修改范围。
- 本次需要做设计、实现、调试还是审查。
- 验收标准与输出格式。
- 当前项目赋予 Deep Engineering Role 的项目角色名。

不要把大量未筛选上下文直接丢给 Codex 让其猜任务。

## 做 Review 时

- Review 对象尽可能绑定 exact SHA。
- 先验证事实，再接受 Writer handoff 自述。
- 重点寻找系统性风险，不只看表面代码风格。
- 对高风险改动给出可执行 PASS 条件。
- `NEEDS_CORRECTION` 时指出最小必要修正范围，不无故扩展重构。

## 输出

回传至少包含：

- 项目角色名与 Task ID。
- 结论与状态建议。
- 关键风险/根因。
- 修改或建议修改的文件/边界。
- 验证证据。
- branch / exact SHA（如有代码变更）。
- 是否可以进入 merge / pin / 下一阶段。

Deep Engineering Role 的价值在于解决高难问题和提高高风险环节确定性，而不是增加普通任务的 token 消耗。