# Standards Journal

只记录工程规范体系的重大演进。最新在前；普通项目进度、Task 日志和完整 Prompt 不进入这里。

## 2026-09

- **2026-09-11 — Backend Capability Certification / HY4 retirement：** 将跨项目 backend/model routing 从“模型品牌印象”升级为可验证 capability certification。新增 `BACKEND-CAPABILITY-CERTIFICATION.md` 作为当前 backend availability、Owner resource policy、C1–C4 能力认证和 routing evidence 的 durable registry；新增统一 `BACKEND-CAPABILITY-EXAM-V1.md`，用同题评估 scope judgment、debugging、implementation、verification、Git/recovery、cross-project identity 与 Contract migration。Engineer identity 继续保持 project-scoped，backend certification 不创建跨项目 named engineers。Owner 明确将 WorkBuddy HY4 设为 `RETIRED_DO_NOT_ROUTE`；GPT-6 Owner 预认证为顶级资源并免除本轮考核；GPT-5.6 Sol、Terra、Luna Max、Luna normal 与 TeleAgent 进入统一考核。PM 后续优先选择达到任务最低认证等级且额度/成本更优的 backend，能力证据不足时再升级。

- **2026-09-09 — Project-scoped Engineer Identity Boundary：** 修正 multi-repo / dependency / plugin / parent-subproject 场景中的 named engineer 跨项目串线风险。`AGENT-OPERATING-MODEL.md` §3.2 成为 canonical 定义：engineer identity 是 project-scoped durable identity；读取另一个 project/repository 的 roster 不改变 current project staffing；dependency/plugin/subproject/sibling/parent relationship 不自动共享 engineer identities；只有 current project canonical roster 中存在的 named identity 才能作为 current-project engineer dispatch，或由 Owner 明确批准 roster add。多个 repositories 只有在 project-control facts 明确声明共享 project scope / canonical roster 时才共享 identities。Cross-project bug 默认通过 project-to-project handoff，由 external project 自己 staffing，再 handback current project integration review。`NEW-SESSION-BOOTSTRAP.md` 增加 resolve current project → locate current roster → restore identities → named dispatch 顺序和 membership invariant。backend switch 仍属于 PM capability routing，Lean 项目仍可只用 generic PM/Writer，不新增 dependency roster bureaucracy。

- **2026-09-09 — Governance Simplification / Lean Default Review：** 基于中小型个人 / AI-assisted 项目治理成本过高，以及 restricted-content timeless hard gate 与 immutable Git/GitHub audit evidence 的真实冲突，将全局治理模型重构为 **Default Lean. Escalate by risk.**。`ENGINEERING-STANDARDS.md` 成为 Lean Project、Fast/Standard/High-risk、scope-appropriate testing、risk-based Review 和 durable Project Memory 的 canonical owner；Task ID 不再因“派给 Agent”自动触发；长期 named roster 降为有真实跨会话 identity 价值时才使用，backend switch 改为 PM capability routing；引入 `SAFE_REMOTE_FIRST` 允许可逆、无 runtime/build/security/migration 依赖的 docs/低风险文本直接 remote-first；normal small PR 只需 current head + diff + applicable validation，高风险边界才强化 exact-SHA / independent Review；Project Memory 只记录 architecture、milestone/stage、release、canonical Contract、durable roster change、major recovery。Restricted Content 改为 `ACTIVE SURFACE` 与封闭 `LEGACY_EVIDENCE_SET` 双轨：新/current content 继续 hard gate，普通 historical wording 不再要求 history rewrite/force push/tag/release recreation，也不永久阻塞当前 PASS；secret/legal/security/highly-sensitive/Owner-requested physical removal 仍升级 destructive purge。新规则向前立即生效，老项目自然维护时逐步收敛，不做批量治理迁移。

- **2026-09-07 — Project Roster Memory：** 为长期使用 named engineers 的项目建立 GitHub durable identity memory，防止跨会话重复命名。2026-09-09 Lean refactor 后，该机制只在 durable named roster 确实有长期身份价值时要求；Lean 单 Agent / PM+Writer 项目可不拟人化。

- **2026-09-07 — Risk-proportional execution paths：** 建立 Fast / Standard / High-risk 执行深度，确认 governance depth 应与 task risk 匹配。2026-09-09 进一步把 Lean 设为项目和任务默认，并清理 Task ID、worktree、Reviewer、testing 等残余重流程触发点。

- **2026-09-07 — Owner-facing Communication Closure：** PM 的 substantive reply 必须让 Owner 清楚当前结论、是否需要行动和下一责任人；无需 Owner 决策时 PM 自动继续，不制造伪确认。

- **2026-09-07 — Remote-only Write boundary：** 最初建立 local construction / remote governance 能力边界。2026-09-09 在保留 code/runtime/migration local-first 的同时增加 `SAFE_REMOTE_FIRST`，低风险 docs/text 不再需要逐次 remote-first 授权和立即 local-sync cleanup task。

- **2026-09-07 — PM-supervised workflow hardening：** 引入 evidence、risk-triggered restatement、risk-based Review、debugging circuit breaker、irreversible-action pre-authorization 与 automation-first；这些机制在 2026-09-09 后明确按风险启用，不作为所有普通任务默认仪式。

- **2026-09-04 — Stable project identity / capability routing：** 区分 project identity、stage staffing 与 backend capability；引入 high-capability execution/review resource 与 Deep Engineering routing。2026-09-09 明确 backend/model switch 不等于人员变化，临时 Reviewer 不要求扩充长期 roster。

- **2026-09-04 — Local Project workspace：** 统一 `/Users/hwang/Movies/Program/<project-name>/` 作为本地工程根目录，防止重复 clone 和事实分叉。2026-09-09 明确单 Writer 不机械要求额外 worktree，SAFE_REMOTE_FIRST 文本改动可直接 remote construction。

- **2026-09-01 — Restricted-content hard gate：** 建立 restricted identifier 及其可识别等价表达的跨项目内容限制。2026-09-09 增加 Active Surface / Legacy Evidence 时间与可变性边界，解决普通历史 immutable evidence 与当前 PASS 的冲突。

- **2026-09-01 — Task identity / GitHub-native handoff / bootstrap：** 建立 Task ID 幂等恢复、GitHub-native handoff、新会话 bootstrap、PM Review 与工程协作基础。后续版本逐步将这些机制从“正式任务默认”收敛为风险/恢复价值触发。

- **2026-09-01 — Repository repositioning：** 仓库从个人开发日志转为跨项目 engineering standards + reusable knowledge canonical repository。

## 2026-08

- **2026-08-09 — Initial repository：** 最初用于个人开发计划、项目记录与决策；该定位已被后续跨项目工程规范定位取代。
