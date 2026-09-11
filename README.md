# Engineering Journal

`engineering-journal` 是跨项目工程规范与可复用工程知识的长期仓库。

它不是项目看板、Task 日志或聊天归档。核心目标：

> **Default Lean. Escalate by risk.**

工程治理必须降低真实风险，而不是为了流程完整而增加流程。

## 新会话最短启动方式

Owner 可以说：

> 请参考 https://github.com/HWang0310/engineering-journal 的工程规范，开发 XXX 项目。

新会话先读取 remote 最新 default branch，再按 `NEW-SESSION-BOOTSTRAP.md` 判断当前任务走 Fast / Standard / High-risk。

## 默认治理模型

### Lean Project（默认）

一般中小型个人 / AI-assisted software project 默认 Lean：

- PM + 0–1 Writer；
- Reviewer 按风险临时启用；
- 不要求每个任务 Issue / Task ID；
- 不要求每个 PR 独立 Reviewer；
- 单 Writer 不要求额外 worktree；
- docs-only 不跑完整 regression；
- Project Memory 只记录 durable truth；
- 不为 backend 切换创建新人员。

### Fast Path

普通 docs fix、small bug、small config、formatting、simple test fix、small reversible refactor：

`Prompt → Execute → Verify → Commit/PR（需要时）→ PM Review`

默认不需要完整 Task/Issue/Handoff。

### Standard Path

当任务有一定恢复、追踪或工程复杂度时升级；Task ID 只有达到 threshold 才使用。

### High-risk Path

architecture、Contract/API/schema、release、migration、production destructive、security-sensitive、core runtime、cross-repo、multi-Agent fan-in、history rewrite 等启用强治理。

完整 canonical 定义见 `ENGINEERING-STANDARDS.md`。

## Owner / PM

- Owner：产品目标、优先级、风险接受、不可逆业务选择。
- PM：技术方案、Agent/backend、Task ID、Git/worktree、测试、Reviewer、Review、merge。

Owner 不承担工程流程管理。

## Engineer identity 与 backend

Engineer identity != backend。

同一 Writer 可以因任务需要切换 backend；这属于 PM capability routing，不自动变成人员变更。

只有真正新增/删除/改名长期 engineer identity 才属于 Owner personnel decision。

Lean 单 Agent / PM+Writer 项目可以不用拟人化 roster；长期 multi-Agent named project 才维护 durable roster。

身份/roster 规则详见 `AGENT-OPERATING-MODEL.md`；跨项目可复用的模型/backend 能力证据、当前可用/退役资源和认证等级见 `BACKEND-CAPABILITY-CERTIFICATION.md`。

## Backend capability routing

PM 不凭模型品牌印象派工，也不把 backend profile 当成 engineer identity。

- 任务先确定所需最低 capability level；
- 再从已认证 backend 中选择满足要求、额度/成本更合适的资源；
- backend/profile 明显变化时可重新认证；
- 退役资源不得继续默认 routing；
- 统一入职考核见 `BACKEND-CAPABILITY-EXAM-V1.md`。

目标：**用最低但足够安全可靠的已认证能力完成任务，证据不足时再升级。**

## Local / Remote

- code/tests/build/refactor/migration/runtime/security-sensitive：默认 local-first；
- 满足 `SAFE_REMOTE_FIRST` 条件的 Markdown/docs/低风险文本改动：可以直接 GitHub remote-first；
- single Writer serial 不要求额外 worktree；
- concurrent Writers 必须隔离。

详见 `LOCAL-WORKSPACE-STANDARD.md`。

## Restricted content

当前/new Active Surface 继续严格 hard gate。

历史 immutable Git/GitHub/release/backup 对象可以在 Owner-approved cutoff 下登记为封闭 `LEGACY_EVIDENCE_SET`，用于 audit/recovery/provenance，不因普通历史 wording 永久阻塞当前 PASS。

真实 secrets、法律删除、安全事故、高敏数据或 Owner 明确要求物理历史删除时，才升级 destructive purge。

详见 `RESTRICTED-CONTENT-STANDARD.md`。

## Testing / Review

- docs-only：diff / content / link / format；
- small code：targeted tests；
- high-risk/core：full relevant regression；
- release：release-grade。

普通小 PR 由 PM 确认 current head + diff + applicable tests；exact-SHA 深 Review 和独立 Reviewer 主要用于高风险边界。

## Project Memory

只记录 architecture、milestone/stage、release、canonical Contract、durable roster change、major recovery 等长期事实。

普通 bug/docs/correction/temp reviewer/backend switch/refactor 由 PR/Issue/commit history 承载。

## Canonical standards

| File | Canonical responsibility |
| --- | --- |
| `ENGINEERING-STANDARDS.md` | Lean Project、Fast/Standard/High-risk、testing、Review、Project Memory、hard boundaries |
| `NEW-SESSION-BOOTSTRAP.md` | 新会话快速选择治理深度 |
| `TASK-LIFECYCLE-STANDARD.md` | Task ID threshold / idempotency / recovery |
| `AGENT-OPERATING-MODEL.md` | engineer identity / roster / backend / staffing |
| `BACKEND-CAPABILITY-CERTIFICATION.md` | backend/model capability certification、resource policy、routing evidence |
| `BACKEND-CAPABILITY-EXAM-V1.md` | 统一 backend 入职考核题面与评分 rubric |
| `PROMPT-HANDOFF-STANDARD.md` | Prompt 与 GitHub-native handoff |
| `LOCAL-WORKSPACE-STANDARD.md` | local-first / SAFE_REMOTE_FIRST / worktree |
| `GIT-GITHUB-STANDARD.md` | Git mechanics / review refs / merge |
| `RESTRICTED-CONTENT-STANDARD.md` | Active Surface / Legacy Evidence gate |
| `KNOWLEDGE-ACCUMULATION.md` | 可复用知识沉淀 |
| `CODEX-RULES.md` | Deep Engineering 专项使用原则 |
| `AGENTS.md` | Agent 入口和 cross-reference |
| `JOURNAL.md` | 重大标准演进历史 |

同一核心规则只在一个文件完整定义，其它文件只引用。

## Hard boundaries that remain

简化不意味着放弃：

- secrets / credentials 不入库；
- customer / production sensitive data protection；
- destructive / irreversible actions pre-authorization；
- production DB destructive operations 高风险；
- one Writer per shared area；
- concurrent Writers isolation；
- architecture / Contract 深 Review；
- GitHub durable truth；
- meaningful recovery；
- Agent 无最终验收权；
- Owner 保留产品方向和不可逆业务决策。
