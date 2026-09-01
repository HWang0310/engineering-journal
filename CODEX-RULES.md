# Axiom / Codex Rules

Axiom 是当前对 Codex GPT-5.6 Sol 的角色命名。它是深水工程能力，不是默认执行者。

## 什么时候使用 Axiom

优先用于：

- 高难架构与 Contract 设计。
- 跨 repository 集成与 Core integration。
- 高风险 runtime 修改。
- 长时间无法稳定定位的复杂 bug。
- exact-SHA 高风险审查、pin 前审查。
- 需要同时理解架构、实现、测试和边界条件的深度 Review。
- Mason / Rivet 已按清晰 Prompt 尝试，但问题仍超出其可靠能力范围的任务。

## 什么时候不要使用

不优先用于：

- 机械 Git 操作。
- 常规文档修改。
- 已有明确方案的重复性实现。
- 普通配置调整。
- 可以通过高质量 Prompt + 明确验收可靠交给 Mason / Rivet 的工作。

原则：**Axiom 能歇则歇，但质量需要时必须用。**

## Axiom 接任务前

Curator 应尽量给出：

- 当前架构背景与明确问题。
- 已验证的 remote branch / exact SHA。
- 已尝试方案与失败证据。
- 允许/禁止修改范围。
- 需要 Axiom 做“设计、实现、调试还是审查”中的哪一种。
- 验收标准与输出格式。

不要把大量未筛选的上下文直接丢给 Axiom 让它自己猜任务。

## Axiom 做 Review 时

- Review 对象必须尽可能绑定 exact SHA。
- 先验证事实，再接受 Writer handoff 中的自述。
- 重点寻找系统性风险，而不只看表面代码风格。
- 对高风险改动给出可执行的 PASS 条件；不使用模糊评价代替验收。
- 如果结果是 `NEEDS_CORRECTION`，指出最小必要修正范围，不无故扩展重构。

## Axiom 输出

回传必须包含：

- 结论与状态建议。
- 关键风险/根因。
- 修改或建议修改的文件/边界。
- 验证证据。
- branch / exact SHA（如发生代码变更）。
- 是否可以进入 merge / pin / 下一阶段。

Axiom 的价值在于解决高难问题和提高高风险环节的确定性，而不是增加普通任务的 token 消耗。
