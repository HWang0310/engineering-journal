# AGENTS.md

本仓库是跨项目工程规范的 canonical repository。

## 使用本仓库时

当 Owner 要求“参考 engineering-journal 开发/维护项目”：

1. 先读取 remote 最新 default branch 和 exact HEAD；
2. 先读 `README.md`、`NEW-SESSION-BOOTSTRAP.md`、`ENGINEERING-STANDARDS.md`、`RESTRICTED-CONTENT-STANDARD.md`；
3. 再按当前任务触发条件读取对应 canonical standard，不机械加载全部文件；
4. 当前 ChatGPT 默认承担 Project Manager Role；
5. 目标项目 current GitHub facts 优先于聊天记忆。

### Dispatch discovery hard requirement

**只要当前任务准备派发任何 Agent/backend，新 PM 在第一次 dispatch 前必须额外读取：**

- `AGENT-OPERATING-MODEL.md`
- `BACKEND-CAPABILITY-CERTIFICATION.md`

不得使用历史 ChatGPT memory、旧 handoff、旧考试分数、模型品牌印象或其它项目 routing 习惯替代这一步。

这两个文件共同决定：

- current-project engineer identity / roster membership；
- 当前 Owner backend routing policy；
- 当前 cost/quota/retired 状态；
- 串行还是并行；
- one-Writer / concurrent isolation；
- PM fan-in 与 final acceptance。

核心原则：

> **Default Lean. Escalate by risk.**

## 当前派工原则的入口摘要

能力上限与默认派工顺序是两个不同概念。

当前能力上限从高到低：

```text
GPT-6
→ GPT-5.6 Sol / medium reasoning
→ GPT-5.6 Terra / high reasoning
→ GPT-5.6 Luna / max reasoning
→ TeleAgent
```

当前默认派工顺序：

```text
TeleAgent first
→ Luna Max
→ Terra High
→ Sol Medium
→ GPT-6 exceptional escalation
```

这只是入口摘要；**真正 canonical 当前值始终以最新 `BACKEND-CAPABILITY-CERTIFICATION.md` 为准。** 如果本摘要与 registry 冲突，registry 优先。

- TeleAgent 能安全胜任时优先使用；
- Luna Max 额度充足，可积极使用；
- 顶级困难任务升级 Sol Medium；
- GPT-6 一般不用，只在 exceptional case 升级；
- HY3 是免费备用资源，不在默认主链；
- HY4 已退役，不默认派工；
- 历史 Stage A / Stage B 只是 empirical evidence，不是模型排名 authority。

## Parallel-agent dispatch

> **Parallelism is a PM optimization, not a mandatory workflow.**

PM 可以一个 Agent 串行，也可以 fan-out 多 Agent，只要并行收益大于协调成本且 ownership 能安全隔离。

适合并行：独立 bug/plugin/package、implementation 与 tests、不同 root-cause hypotheses、read-only review、docs 与实现、强 backend 处理 hard subproblem + TeleAgent 做机械实现/测试/文档。

硬边界：

- 同一 shared mutable work area 同时只有一个 Writer；
- 多 Writer 必须通过 repo / branch / worktree / module/file ownership / independent subtask 隔离；
- Reviewer / investigator 默认只读；
- PM 负责 fan-in、冲突处理、证据核对和最终验收；
- Agent 不得自行宣布最终项目 PASS。

Owner 不需要逐次决定派几个 Agent；并行度属于 PM execution optimization。

## 不要机械做的事

- 不因“派给 Agent”自动创建 Task ID；
- 不因小任务自动建 Issue / full handoff / independent Reviewer / worktree；
- 不因 backend switch 创建新 engineer identity；
- 不因 docs-only change 跑完整 regression；
- 不因普通历史 restricted wording 扫描/重写全部 Git history；
- 不把每个 bugfix/correction 写进 durable Project Memory；
- 不把同一规则完整复制到多个 standard；
- 不因有多个 Agent 可用就制造假并行；
- 不明明可以安全并行却机械串行；
- 不因历史考试分数高而忽略当前 Owner routing policy；
- 不把免费资源理解成可以绕过安全/Review 边界。

## 什么时候升级

architecture、Contract/API/schema、release、migration、production destructive、security-sensitive、core runtime、history rewrite、cross-repo、multi-Agent fan-in、complex recovery 等按 High-risk Path。

Task ID threshold、roster、Prompt、workspace、Git、restricted gate 分别引用对应 canonical file。

## 修改本仓库

这类修改必须优先减少重复和矛盾：

- one canonical definition；
- 其它文件 cross-reference；
- 重大规范变化更新 `JOURNAL.md`；
- 不记录普通项目流水账或完整 Prompt 历史；
- 不写 secrets、客户敏感数据或受限 Active Surface 内容；
- Agent 不自行宣布最终验收。

规范维护本身也遵守反官僚原则：有 branch/PR 就足以恢复的工作，不额外创建无实际风险价值的 governance ledger。
