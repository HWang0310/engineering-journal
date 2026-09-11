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

这两个 canonical sources 决定 current-project roster membership、当前 Owner backend routing policy、resource/cost/quota/retired 状态、串行/并行边界、Writer isolation 与 PM final acceptance。

**不要在入口文件复制易变的 backend 排名或额度表；当前具体 routing 顺序始终以最新 `BACKEND-CAPABILITY-CERTIFICATION.md` 为准。**

核心原则：

> **Default Lean. Escalate by risk.**

## Parallel-agent dispatch

> **Parallelism is a PM optimization, not a mandatory workflow.**

PM 可以一个 Agent 串行，也可以 fan-out 多 Agent；是否并行由真实并行收益、任务依赖和 ownership isolation 决定，Owner 不需要逐次决定 Agent 数量。

硬边界：同一 shared mutable work area 同时只有一个 Writer；并行 Writer 必须隔离；Reviewer/investigator 默认只读；PM 负责 fan-in、冲突处理、证据核对和最终验收；Agent 不得自行宣布项目最终 PASS。完整规则见 `AGENT-OPERATING-MODEL.md`。

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
