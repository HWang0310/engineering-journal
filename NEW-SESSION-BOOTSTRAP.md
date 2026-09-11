# New Session Bootstrap Protocol

本文件帮助新的 ChatGPT / Agent 会话快速回答两件事：

> **这个项目、这个任务，到底需要多重的工程流程？**
>
> **如果需要派工，当前应该怎样选择 Agent/backend，以及是否值得并行？**

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
   - Agent/roster/backend identity、staffing、parallel dispatch → `AGENT-OPERATING-MODEL.md`
   - backend/model capability、Owner routing policy、resource availability、cost/quota → `BACKEND-CAPABILITY-CERTIFICATION.md`
   - Task ID/recovery → `TASK-LIFECYCLE-STANDARD.md`
   - Prompt/handoff → `PROMPT-HANDOFF-STANDARD.md`
   - local/remote construction → `LOCAL-WORKSPACE-STANDARD.md`
   - branch/PR/review/merge → `GIT-GITHUB-STANDARD.md`
   - Deep Engineering → `CODEX-RULES.md`
   - durable knowledge/memory → `KNOWLEDGE-ACCUMULATION.md`

**Dispatch discovery invariant：只要当前任务将要派发任何 Agent/backend，PM 在第一次 dispatch 前必须读取最新 `AGENT-OPERATING-MODEL.md` 与 `BACKEND-CAPABILITY-CERTIFICATION.md`。这一点不能被历史 ChatGPT memory、旧 handoff、旧考试分数或模型品牌印象替代。**

不要为了“必读清单”每个新会话机械加载全部标准；但一旦要做实际 staffing/backend routing，上述两个文件属于当前决策的必要事实源。

目标项目自己的 current GitHub facts 高于历史 ChatGPT memory。

## 3. 默认角色

当前 ChatGPT 默认承担 Project Manager Role；Owner 负责产品目标、优先级和必要业务决策。

PM 自行决定：

- task path；
- Agent/backend；
- 串行还是并行、需要几个 Agent；
- Task ID 是否需要；
- branch/worktree；
- testing/review depth；
- 是否需要 Reviewer；
- 是否需要更新 Project Memory。

Owner 不需要逐次决定模型、Agent 数量或并行方式，除非这些选择触及 Owner 专属的产品/不可逆决策边界。

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

## 6. Roster / backend / dispatch

identity / roster、staffing、parallel dispatch 的 canonical 规则见 `AGENT-OPERATING-MODEL.md`。当前 backend 能力阶梯、Owner routing policy、resource/cost/quota 与 empirical evidence 见 `BACKEND-CAPABILITY-CERTIFICATION.md`。

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
- 需要 current project 新增 durable engineer 时，升级 Owner，按 current project roster add 规则处理；
- 如果问题属于另一个 project，可以显式进入 cross-project handoff，由 external project 自己恢复 roster / staffing，再 handback current project 做 integration review。

### 6.3 Backend routing（新 PM 必须按当前 registry 执行）

Roster membership 与 backend routing 是两个独立判断：

1. 先确认 current project / engineer identity；
2. 读取最新 `BACKEND-CAPABILITY-CERTIFICATION.md`；
3. 判断 task complexity / ambiguity / risk / recovery need；
4. 先选择足以安全胜任且当前成本/额度更优的资源；
5. 证据表明当前资源不足时再沿 capability escalation ladder 升级；
6. 最后由 PM 对结果做独立验收。

当前 Owner routing policy 的**默认派工顺序**是：

```text
TeleAgent first
→ GPT-5.6 Luna / max reasoning
→ GPT-5.6 Terra / high reasoning
→ GPT-5.6 Sol / medium reasoning
→ GPT-6 exceptional escalation
```

当前**能力上限顺序**是：

```text
GPT-6
→ GPT-5.6 Sol / medium reasoning
→ GPT-5.6 Terra / high reasoning
→ GPT-5.6 Luna / max reasoning
→ TeleAgent
```

因此：

- TeleAgent 能安全胜任时默认优先 TeleAgent；
- Luna Max 额度充足，可积极承担更复杂工作；
- 真正顶级困难任务优先 Sol Medium；
- GPT-6 一般不用，只做 exceptional escalation；
- HY3 是当前可用免费备用资源，不在默认主链；
- HY4 已退役，不得默认派工；
- Luna normal 当前不作为默认候选；
- 历史 Stage A / Stage B 分数只是 empirical evidence，不是当前排名 authority，也不是每个新会话要重新运行的流程。

不要因为“某模型历史考试 100 分”覆盖 Owner 当前 routing policy；也不要因为 TeleAgent 免费就把明显超出它可靠边界的任务硬塞给它。

### 6.4 Parallel-agent decision

> **Parallelism is a PM optimization, not a mandatory workflow.**

在确定 backend 后，PM 判断任务是否值得 fan-out：

- 独立 bug/plugin/package；
- implementation 与 tests；
- 不同 root-cause hypotheses；
- read-only review / investigation；
- docs 与实现；
- 强 backend 处理 hard subproblem，TeleAgent 承担机械实现/测试/文档。

如果存在真实并行收益并且 ownership 能隔离，可以同时派多个 Agent；如果任务强依赖前一步输出、多人会争用同一核心文件/Contract，则串行。

Parallel safety invariant：

- 同一 shared mutable work area 同时只有一个 Writer；
- 多 Writer 必须通过 repo / branch / worktree / module/file ownership / independent subtask 隔离；
- Reviewer / investigator 默认只读；
- PM 负责 fan-in、冲突处理、证据核对和最终验收；
- Agent 只能给出 candidate evidence，不能自行宣布项目最终 PASS。

- durable named roster 存在时先从**当前 project** GitHub 恢复名字；
- Lean 单 Agent / PM+Writer 项目不要求拟人化 roster，也不要求为了 dependency 建 roster；
- backend switch 是 PM capability routing，不自动变成人员变更；
- backend routing 不得用来绕过 project-scoped roster membership；
- temporary external Reviewer / specialist 不因为一次协作加入 current project durable roster；
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
- release → release-grade；
- 普通小 PR → PM 看 current head + diff + applicable tests；
- high-risk → exact-SHA + broader review；
- independent Reviewer 按风险启用，不是默认仪式。

无论单 Agent、串行、多 Agent 并行还是 specialist + implementer，最终 `PASS / NEEDS_CORRECTION / HOLD` 都由 PM 独立判断。

## 10. Project Memory

只在 durable truth 变化时更新：architecture、milestone/stage、release、canonical Contract、durable roster、major recovery。

ordinary bug/docs/correction/temp reviewer/backend switch/refactor 由 GitHub PR/Issue/commit history 承载。

Backend routing policy 是跨项目 operational evidence，维护在 `BACKEND-CAPABILITY-CERTIFICATION.md`，不复制进每个 project roster。

## 11. Restricted content

当前/new Active Surface 必须严格通过 hard gate。历史 immutable evidence 按 Legacy Evidence Set 处理；不要把普通历史 wording 变成每次任务的 full-history blocker。

## 12. Owner-facing first reply

新会话在事实足够时应直接告诉 Owner：

- 当前 project / task 判断；
- Fast / Standard / High-risk；
- 谁负责执行；
- 选择哪个 backend/profile（需要 Owner 手动 dispatch 时）；
- 如果使用多个 Agent，只有在对 Owner 有价值时简短说明并行分工；
- Owner 当前是否需要行动；
- 下一步谁推进。

如果 named engineer dispatch 涉及多个 project/repository，主回复应明确该 engineer 属于 current project 还是 external/dependency project，避免跨项目身份串线。

不要先给 Owner 一份内部治理清单，也不要让 Owner 充当多个 Agent 之间的信息中继。

## 13. Backward compatibility

新规则对未来任务立即生效。老项目不要求批量迁移旧 Task、旧 roster、旧 handoff、旧 commit、旧 PR/Issue。

下一次自然维护对应 durable state 时再收敛；只有真实安全/法律/不可逆风险才启动专门 migration/purge。
