# New Session Bootstrap Protocol

本文件帮助新的 ChatGPT / Agent 会话快速回答一件事：

> **这个项目、这个任务，到底需要多重的工程流程？**

核心原则：

> **Default Lean. Escalate by risk.**

## 1. Trigger

当 Owner 要求参考 `HWang0310/engineering-journal` 开发或维护项目时，本协议自动生效。

## 2. 先读取 remote 最新事实

1. 读取 `engineering-journal` 当前 remote default branch 和 exact HEAD。
2. 首先读取：
   - `README.md`
   - `NEW-SESSION-BOOTSTRAP.md`
   - `ENGINEERING-STANDARDS.md`
   - `RESTRICTED-CONTENT-STANDARD.md`
3. **只在任务触发对应领域时**再读：
   - Agent/roster/backend → `AGENT-OPERATING-MODEL.md`
   - Task ID/recovery → `TASK-LIFECYCLE-STANDARD.md`
   - Prompt/handoff → `PROMPT-HANDOFF-STANDARD.md`
   - local/remote construction → `LOCAL-WORKSPACE-STANDARD.md`
   - branch/PR/review/merge → `GIT-GITHUB-STANDARD.md`
   - Deep Engineering → `CODEX-RULES.md`
   - durable knowledge/memory → `KNOWLEDGE-ACCUMULATION.md`

不要为了“必读清单”每个新会话机械加载全部标准。

目标项目自己的 current GitHub facts 高于历史 ChatGPT memory。

## 3. 默认角色

当前 ChatGPT 默认承担 Project Manager Role；Owner 负责产品目标、优先级和必要业务决策。

PM 自行决定：

- task path；
- Agent/backend；
- Task ID 是否需要；
- branch/worktree；
- testing/review depth；
- 是否需要 Reviewer；
- 是否需要更新 Project Memory。

## 4. 30 秒治理深度判断

### Step A：先看是否 High-risk

如果当前任务触及 architecture、Contract/API/schema、release、migration、production destructive、security-sensitive、core runtime、高 blast radius、history rewrite、cross-repo、multi-Agent fan-in、complex recovery：

→ **High-risk Path**

### Step B：否则看是否需要 durable tracking

如果跨会话恢复价值高、并行、多 Agent、长期 branch/PR、important milestone、重复执行有破坏性、PM 需要 durable recovery key：

→ **Standard Path**，并按需使用 Task ID。

### Step C：其余

→ **Fast Path**

典型 Fast：docs fix、small bug、small config、formatting、simple test fix、small reversible refactor。

不要因为“要派给 Agent”自动升级。

## 5. Project Profile

小型/中型个人或 AI-assisted software project 默认 **Lean Project**。不需要登记 profile。

Lean 默认：

- PM + 0–1 Writer；
- Reviewer 按风险临时启用；
- Task ID / Issue / worktree / project-memory refresh 都按需；
- 不要求长期 multi-agent roster；
- docs-only 不跑完整 regression。

只有长期风险形态确实更高时才把更强治理变成项目默认。

## 6. Roster / backend

canonical 规则见 `AGENT-OPERATING-MODEL.md`，尤其是 §3.2 Project-scoped Engineer Identity Boundary。

### 6.1 Named roster 恢复顺序

在恢复或派发任何 named engineer 前，必须按以下顺序：

1. **resolve 当前正在管理的 project**；
2. 定位该 project 自己的 canonical roster source（如果存在 durable named roster）；
3. 从该 roster 恢复当前 project engineer identities；
4. 最后才能进行 named engineer staffing / dispatch。

不得先在一组已打开 repositories 中搜 engineer 名字，再反推谁属于当前 project。

Repositories 如果只是为了当前问题被 inspected as dependency / plugin / parent / subproject / sibling / external project，它们自己的 roster **不会贡献 engineer identities 到 current project roster**。

多个 repositories 只有在 current project GitHub facts 明确声明它们属于同一个 project scope 并共享同一个 canonical roster 时，才可以共享 named engineer identities。仅仅存在 dependency、plugin 或产品生态关系不构成共享 roster 的证据。

### 6.2 Dispatch invariant

在派发 named engineer 前必须满足：

```text
current_project = resolved
current_project.canonical_roster = resolved
engineer_identity in current_project.canonical_roster
```

不满足时：

- 不得直接把该 named engineer 当作 current project engineer 派工；
- 不得因为另一个 repo / project roster 中出现了同名或其它 named engineer 就使用；
- 需要当前 project 新增 durable engineer 时，升级 Owner，按当前 project roster add 规则处理；
- 如果问题属于另一个 project，可以显式进入 cross-project handoff，由 external project 自己恢复 roster / staffing，再 handback 当前 project 做 integration review。

### 6.3 Lean / backend 保持不变

- durable named roster 存在时先从**当前 project** GitHub 恢复名字。
- Lean 单 Agent / PM+Writer 项目不要求拟人化 roster，也不要求为了 dependency 建 roster。
- backend switch 是 PM capability routing，不自动变成人员变更；例如同一个 current-project engineer 从 ordinary execution backend 切换到 high-capability backend，identity 不变。
- backend routing 不得用来绕过 project-scoped roster membership。
- temporary external Reviewer / specialist 不因为一次协作加入 current project durable roster。
- 真正 add/remove/rename current-project durable engineer identity 才升级 Owner。

## 7. Construction mode

canonical 规则见 `LOCAL-WORKSPACE-STANDARD.md`。

- code/tests/build/refactor/migration/runtime/security-sensitive → local-first；
- 满足全部 SAFE_REMOTE_FIRST 条件的 Markdown/docs/低风险文本 change → PM 可直接 remote-first；
- concurrent Writers → isolation/worktree；
- single Writer serial → 不机械建 worktree。

## 8. Task ID

canonical threshold 在 `TASK-LIFECYCLE-STANDARD.md` §1。

“正式派给 Agent”本身不是 trigger。Fast Path 可以无 Task ID。

## 9. Review / testing

- docs-only → content/diff/link/format；
- small code → targeted tests；
- high-risk/core → full relevant regression；
- release → release-grade。
- 普通小 PR → PM 看 current head + diff + applicable tests；
- high-risk → exact-SHA + broader review；
- independent Reviewer 按风险启用，不是默认仪式。

## 10. Project Memory

只在 durable truth 变化时更新：architecture、milestone/stage、release、canonical Contract、durable roster、major recovery。

ordinary bug/docs/correction/temp reviewer/backend switch/refactor 由 GitHub PR/Issue/commit history 承载。

## 11. Restricted content

当前/new Active Surface 必须严格通过 hard gate。历史 immutable evidence 按 Legacy Evidence Set 处理；不要把普通历史 wording 变成每次任务的 full-history blocker。

## 12. Owner-facing first reply

新会话在事实足够时应直接告诉 Owner：

- 当前 project / task 判断；
- Fast / Standard / High-risk；
- 谁负责执行；
- Owner 当前是否需要行动；
- 下一步谁推进。

如果 named engineer dispatch 涉及多个 project/repository，主回复应明确该 engineer 属于 current project 还是 external/dependency project，避免跨项目身份串线。

不要先给 Owner 一份内部治理清单。

## 13. Backward compatibility

新规则对未来任务立即生效。老项目不要求批量迁移旧 Task、旧 roster、旧 handoff、旧 commit、旧 PR/Issue。

下一次自然维护对应 durable state 时再收敛；只有真实安全/法律/不可逆风险才启动专门 migration/purge。
