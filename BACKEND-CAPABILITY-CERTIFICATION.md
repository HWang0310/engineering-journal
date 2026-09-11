# Backend Capability & Routing Registry

本文件是跨项目 **backend / model capability routing** 的 durable registry。它记录 Owner 当前资源策略、PM 默认派工顺序、成本/额度约束和经验能力边界；它**不是 project engineer roster**。

核心原则：

> **Engineer identity is project-scoped. Backend capability and routing policy are cross-project reusable operational evidence.**

项目中的 Forge / Atlas / Writer 等 identity 仍由目标项目 canonical roster 决定；PM 可以把一个既有 engineer 路由到本文件中的可用 backend profile，但不能因为出现一个新模型就自动向所有项目增加 named engineer。

## 1. Owner routing policy（canonical，2026-09-11）

当前排序由 **Owner resource policy** 决定，不由考试分数机械产生。

### 1.1 能力上限顺序

从高到低：

```text
GPT-6
→ GPT-5.6 Sol / medium reasoning
→ GPT-5.6 Terra / high reasoning
→ GPT-5.6 Luna / max reasoning
→ TeleAgent
```

说明：

- GPT-6 属于极少使用的最高能力资源；正常项目一般不需要直接升级到 GPT-6。
- 顶级困难任务默认交给 **GPT-5.6 Sol / medium reasoning**。
- 再往下依次使用 **GPT-5.6 Terra / high reasoning**、**GPT-5.6 Luna / max reasoning**。
- **TeleAgent 是日常执行主力**，不是因为能力排名最高，而是因为当前免费、token 额度宽松，且已证明能完成大量真实工程任务。

### 1.2 默认派工顺序

PM 不应从最强模型开始烧额度，而应先使用**足以安全胜任的最便宜/最充足资源**：

```text
TeleAgent first
→ Luna Max
→ Terra High
→ Sol Medium
→ GPT-6 exceptional escalation
```

也就是说：

1. TeleAgent 能胜任时，默认优先 TeleAgent；
2. TeleAgent 证据不足、任务复杂度更高或需要更强判断时，升级 Luna Max；
3. Luna Max 不够时升级 Terra High；
4. 真正顶级困难任务使用 Sol Medium；
5. GPT-6 只用于极少数 Sol 仍不足、风险/复杂度极高或 PM 明确判断值得升级的情况。

### 1.3 当前资源表

| Backend profile | 当前定位 | Cost / quota policy | 默认 routing |
| --- | --- | --- | --- |
| GPT-6 | exceptional top capability | 稀缺/通常不用 | 仅在 Sol 不足或极端高难度时升级 |
| GPT-5.6 Sol / medium reasoning | 顶级困难任务主力 | 有限资源 | architecture、Contract、deep debugging、顶级复杂度、关键 Review |
| GPT-5.6 Terra / high reasoning | 次顶级 | 有限资源 | 中高复杂度实现、复杂 debugging、重要 Review；Luna Max 不足时升级 |
| GPT-5.6 Luna / max reasoning | 高额度高级执行资源 | 额度充足，可积极使用 | Advanced / multi-file / difficult implementation；可大量承担 Terra 以下工作 |
| TeleAgent | 默认执行主力 | **免费 / unlimited-token 优先资源** | Fast / Standard / 大量 implementation、tests、docs、机械执行；除非不能胜任，否则优先 |
| WorkBuddy HY3 | 可用免费备用资源 | 当前免费；未来若收费可直接退役 | 不在默认主链；PM 可在适配任务或资源需要时使用 |
| GPT-5.6 Luna normal | Owner 当前不计划使用 | 不纳入默认 routing | 默认不选 |
| WorkBuddy HY4 | `RETIRED_DO_NOT_ROUTE` | 已因成本策略退役 | 不派工，除非 Owner 日后明确重新启用 |

资源价格、额度、平台可用性变化属于 operational policy，可以直接更新本表，不构成 engineer identity 变化。某 backend 从 available 变为 retired 时，不需要修改任何 project roster。

## 2. PM routing decision（canonical）

PM 每次派工按以下顺序判断：

1. 先判断任务 complexity / ambiguity / blast radius / recovery need；
2. 判断任务能否安全拆成独立并行工作流；
3. 先尝试 TeleAgent 等免费/高额度资源承担其能力范围内的工作；
4. 证据表明当前资源不足时，沿 `TeleAgent → Luna Max → Terra High → Sol Medium → GPT-6` 升级；
5. 强模型可以只处理最难的判断/架构/root cause，机械实现、测试、文档等可继续交给 TeleAgent；
6. 所有重要结果最终由项目 PM 做独立 Review / integration / acceptance。

目标：

> **Use the cheapest / most available resource that can safely do the work. Escalate capability only when the task actually needs it.**

不要因为某 backend 在历史考试中得分高，就机械把所有任务派给它；也不要因为资源免费，就把超出其可靠边界的任务硬塞给它。

## 3. Parallel-agent routing

并行是 PM 的效率工具，不是固定仪式。

> **Parallelism is a PM optimization, not a mandatory workflow.**

允许 PM 在同一项目中同时派发多个 Agent / engineer 工作，只要工作面可以安全隔离并存在真实并行收益。例如：

```text
PM
├─ TeleAgent A：primary implementation
├─ TeleAgent B：targeted tests / edge cases
├─ TeleAgent C：independent investigation
└─ Luna Max / Terra / Sol：hard subproblem or independent review
```

也可以只有一个 Agent 串行完成。PM 根据依赖关系决定，不需要 Owner 每次批准“分配几个人”。

并行必须满足 `AGENT-OPERATING-MODEL.md` 的 ownership / isolation 规则：

- 同一 shared mutable work area 同时只有一个 Writer；
- 多 Writer 必须按 repo / branch / worktree / file ownership / independent subtask 做隔离；
- investigation、tests、read-only review、不同 root-cause hypotheses 可天然并行；
- 强依赖前一步结果、同一 Contract 核心改动、同一文件高冲突区域通常应串行；
- PM 负责 fan-in、冲突处理、证据核对和最终验收。

不要为了“看起来并行”制造协调成本；也不要在本来可以独立并发的任务上机械串行。

## 4. Empirical capability evidence（reference, not ranking authority）

2026-09-11 已完成 Stage A written exam 与 Stage B local Git live practical。它们证明多个 backend 具备基础/高级工程能力，也暴露过真实行为差异，但测试题没有足够能力精确拉开顶级模型的上限，因此**考试成绩不再作为 canonical 模型排名来源**。

现有结果保留为 reference evidence：

| Backend profile | Stage A | Stage B | Evidence note |
| --- | ---: | ---: | --- |
| GPT-5.6 Sol（当时 Owner-labeled high profile） | 100 | 99 | strong scope/debug/Git/Contract evidence |
| GPT-5.6 Terra high | 99 | 99 | strong stable implementation/review evidence |
| GPT-5.6 Luna Max | 100 | 100 | strong live Git preservation/debug/test evidence |
| TeleAgent | 94 | 98 | strong implementation evidence；Stage A 曾出现一次 exact instruction direction reversal，PM 验收仍重要 |
| WorkBuddy HY3 | 94 | 99 | strong live execution evidence；当前作为可用免费备用资源 |

考试文件：

- `BACKEND-CAPABILITY-EXAM-V1.md`
- `BACKEND-CAPABILITY-LIVE-PRACTICAL-V1.md`

默认**不再继续追加比较测试**。只有以下情况才值得重新评估：

- backend/model 发生明显版本或能力变化；
- 平台 execution surface 明显变化；
- 连续真实项目表现明显偏离当前 routing 预期；
- Owner / PM 有具体未解决的能力边界问题。

## 5. Hard boundaries unaffected

无论 backend 多强，都不能绕过：

- destructive / irreversible action pre-authorization；
- secrets / sensitive production/customer data protection；
- project-scoped engineer identity；
- one Writer per shared mutable area；
- concurrent Writer isolation；
- architecture / Contract / migration 等重大风险的相应 Review；
- tests/evidence 不得伪造；
- PM final acceptance；
- Owner 对产品方向和不可逆业务决策的最终权力。

GPT-6 / Sol 不是“免 Review 模型”；TeleAgent 免费也不是“无限权限 Agent”。

## 6. Relationship to project roster

本文件不创建跨项目 named engineer。

如果某个具体项目需要新增一个长期 named engineer：

1. Owner 批准该项目 roster add；
2. 在该项目 canonical roster 中创建 identity；
3. PM 再从本 registry 选择适合的 backend profile；
4. 后续 backend 切换不改变该 identity。

因此 backend routing、并行 fan-out、模型升级/降级都属于 **execution strategy**，不会自动改变项目 roster。