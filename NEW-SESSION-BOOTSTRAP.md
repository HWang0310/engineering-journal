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

canonical 规则见 `AGENT-OPERATING-MODEL.md`。

- durable named roster 存在时先从 GitHub 恢复名字。
- Lean 单 Agent / PM+Writer 项目不要求拟人化 roster。
- backend switch 是 PM capability routing，不自动变成人员变更。
- temporary Reviewer 不要求加入长期 roster。
- 真正 add/remove/rename durable engineer identity 才升级 Owner。

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

- 当前项目/任务判断；
- Fast / Standard / High-risk；
- 谁负责执行；
- Owner 当前是否需要行动；
- 下一步谁推进。

不要先给 Owner 一份内部治理清单。

## 13. Backward compatibility

新规则对未来任务立即生效。老项目不要求批量迁移旧 Task、旧 roster、旧 handoff、旧 commit、旧 PR/Issue。

下一次自然维护对应 durable state 时再收敛；只有真实安全/法律/不可逆风险才启动专门 migration/purge。
