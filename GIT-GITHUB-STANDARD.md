# Git & GitHub Standard

本文件只定义 Git/GitHub mechanics。治理深度见 `ENGINEERING-STANDARDS.md`；construction mode 见 `LOCAL-WORKSPACE-STANDARD.md`。

## 1. Canonical truth

- GitHub remote 是 durable source of truth。
- 本地 checkout/worktree 是执行环境。
- 当前事实优先使用 remote branch / PR / commit。
- high-risk accepted baseline 使用 exact SHA。

### 1.1 Issue / PR sequence 与 Active Queue Hygiene

GitHub Issue / PR number 只表示 repository-local durable sequence，不表示 active task 数量，也不构成 historical traversal 指令。

- `Issue #500` 不得被解释成“需要恢复 #1–#499”；
- fresh recovery 默认查询 OPEN Issue / OPEN PR；closed / merged history 的读取触发条件由 `NEW-SESSION-BOOTSTRAP.md` 的 History Traversal / Recovery Budget 定义；
- Issue 完成后及时 close；
- PR merge 后，对应 Issue 若没有独立未完成 acceptance，应 close completed；
- 被新 Contract / architecture 明确 supersede 的 Issue 应 close as `not_planned` / superseded，而不是长期留在 active queue；
- registration-only Issue 完成注册后 close completed，未来作为 provenance reference；
- 长期 HOLD Issue 只有确实仍可能恢复执行、并且保留 open 能表达 actionable state 时才继续 open。

目标：**OPEN queue 表达当前 actionable state；closed/merged objects 保留 durable provenance。**

不要求为了新规则批量整理全部历史；自然维护或 current-state ambiguity 真正影响 recovery 时再轻量 cleanup。

## 2. Branch / worktree

- 是否需要 branch 由变更追踪和项目策略决定；PR-based 项目通常使用 branch。
- concurrent Writers 必须隔离。
- 单 Writer 串行任务不要求额外 worktree。
- worktree 仅在并行、dirty-state isolation、high-risk isolation 等有实际价值时使用。
- 删除 branch/worktree 前确认没有未提交/未 push 的有效成果。

## 3. Commit

- 一个 commit 尽量表达一个可审查目的。
- 不混入无关 refactor / formatting / dependency upgrade。
- commit 前检查 diff。
- 新 commit message/body 属于 restricted-content Active Surface。
- secrets / credentials / sensitive data 不进入 Git。

Task ID 仅在达到 `TASK-LIFECYCLE-STANDARD.md` §1 门槛时需要出现在工程追踪中。

## 4. Review modes

### Normal small PR

PM 确认：

- current PR head；
- changed files / diff；
- scope；
- applicable validation；
- merge target。

不要求单独 pin-style exact-SHA Evidence Package。

### High-risk exact-SHA Review

主要用于：

- Contract / API / schema；
- release / tag / pin；
- core runtime；
- high-risk refactor；
- cross-repo integration；
- migration；
- multi-Agent fan-in；
- security-sensitive change；
- history rewrite。

Writer push → PM/Reviewer 验证 exact SHA → diff/tests/boundaries → `PASS/HOLD/NEEDS_CORRECTION`。

### 4.1 Correction review: delta-first

同一 PR 已完成一轮 Review 并进入 `NEEDS_CORRECTION` 后，后续 correction round 默认采用 **delta-first review**，避免机械重读已经审过且未变化的历史内容。

如果上一轮已审 head 为 `A`，当前 head 为 `B`，PM 默认先验证并审查：

- `A...B` 的 correction delta；
- 本轮新增或修改的文件 / patch；
- 上一轮 blocking findings 是否逐项解决；
- 本轮 validation evidence；
- 是否出现 scope drift、boundary change 或新的风险。

已经在 `A` 上审过、且 `A...B` 没有触及的 unchanged patch / historical correction transcript 默认不重新读取。

以下情况应扩大到更广的 Review，而不是只看 delta：

- correction 改变了上一轮成立的核心假设；
- 修改触及 shared contract / cross-cutting behavior；
- scope 明显扩大；
- base / ancestry 变化使上一轮 diff 语义失效；
- PM 无法确定上一轮 reviewed head；
- 实际风险要求重新检查完整 surface。

**Delta-first 是 correction round 的读取优化，不替代 final merge gate。** merge 前仍按当前任务风险确认 current PR head、current full changed-file surface、applicable validation、merge target；high-risk 仍遵守 exact-SHA Review。

### 4.2 Review fact freshness / mutation-triggered refresh

同一个连续 Review cycle 内，已经从 remote 验证且没有理由认为失效的 GitHub facts 可以复用，不要求为了仪式重复请求。

典型可复用事实包括：

- live target branch SHA；
- current PR head SHA；
- PR base / merge target；
- changed-file list / diff surface；
- 已读取的 current review state。

当发生或合理怀疑发生会使事实失效的 mutation 时，才刷新对应事实。典型 trigger：

- PR head 有新 push / rebase / force update；
- target branch 因其它 merge / push 前进；
- PR base 被 retarget；
- 新 review / correction / handoff 改变 current review state；
- merge 执行完成；
- 外部动作可能改变当前判断所依赖的 remote state。

不要因为一个 GitHub 请求完成后“保险起见”立即重复读取同一未变化事实。

final merge 前仍必须满足本文件 §5 的 current-head要求；merge 后只重新验证当前任务 DoD 真正需要的事实。例如 main merge SHA 属于 release/recovery 证据时应确认；linked Issue 是否自动关闭，只有在 issue closure 本身属于任务 acceptance / lifecycle requirement 时才需要单独再读取。

## 5. Merge

- Agent 不拥有最终 merge acceptance。
- PM Review 通过后 merge。
- high-risk merge 前确认最终 head 与 reviewed exact SHA 一致。
- normal PR 至少确认当前 head 未在 Review 后发生未审变更。
- destructive history/tag/release 操作受 pre-authorization。

### 5.1 Parallel fan-in / base migration remote ancestry invariant

当 parallel lane / PR 声称已经 merge/rebase 到当前 target branch，或 sibling work 已先 merge、当前 PR 需要重新基于最新 target 做 fan-in 时，PM 在 merge acceptance 前必须以 **remote Git facts** 验证 base migration。

至少确认：

- live target branch SHA；
- live PR head SHA；
- `merge-base(target, head)`；
- live target 是否为 PR head ancestor；如果不是，必须明确为什么该 ancestry 仍符合当前 merge strategy；
- PR 相对 live target 的 current changed-file / diff surface；
- changed-file / diff surface 是否重新包含已经 merged 的 sibling work；
- package / handoff / base metadata 是否与 remote Git ancestry 一致。

以下都**不能单独证明** base migration 已完成：

- Agent self-report，例如“已 rebase 到最新 main”；
- package / manifest / handoff 中记录的新 base SHA；
- GitHub `mergeable=true`。

如果 Agent 声称已更新到最新 target，但 remote 显示：

- merge-base 仍对应旧 target；
- current target 不是 head ancestor 且没有符合当前 merge strategy 的明确原因；
- 已 merged sibling work 重新进入 PR surface；
- metadata 与 remote ancestry 不一致；

则 PM 应 `HOLD`，先修复 branch/base，再继续 fan-in。

能通过普通 merge / rebase 修复时优先普通方式。任何 force push / history rewrite 仍遵守现有 pre-authorization 规则。

**本 invariant 是 parallel fan-in / claimed base migration 的轻量 remote mechanics verification；它本身不会把普通低风险 PR 自动升级为 exact-SHA deep review。** exact-SHA 仍只按任务真实风险和本文件 §4 的 Review mode 使用。

## 6. SAFE_REMOTE_FIRST

符合 `LOCAL-WORKSPACE-STANDARD.md` §6.2 的 docs/small text change 可以 remote-first；不要求先 local checkout。

Code/tests/build/refactor/migration/runtime/security-sensitive work 默认 local-first。

## 7. Restricted content

只对**新/当前 Active Surface**强制 gate，包括新 branch/tag/release 名称、新 commit message、current PR/Issue/comment、current tree/config。

历史 immutable Git/GitHub evidence 按 `RESTRICTED-CONTENT-STANDARD.md` 的 Legacy Evidence Set 处理，不因普通历史 wording 自动要求 rewrite/force push/tag recreation。

## 8. Minimal validation

按变更类型执行适用验证，不维护固定“所有任务必跑”的 Git 命令清单。

- remote docs：base/head + diff + content checks；
- local code：repo/origin/status + targeted tests；
- high-risk：增加 exact SHA、broader regression、boundary/recovery checks。
