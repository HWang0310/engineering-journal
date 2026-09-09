# Git & GitHub Standard

本文件只定义 Git/GitHub mechanics。治理深度见 `ENGINEERING-STANDARDS.md`；construction mode 见 `LOCAL-WORKSPACE-STANDARD.md`。

## 1. Canonical truth

- GitHub remote 是 durable source of truth。
- 本地 checkout/worktree 是执行环境。
- 当前事实优先使用 remote branch / PR / commit。
- high-risk accepted baseline 使用 exact SHA。

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

## 5. Merge

- Agent 不拥有最终 merge acceptance。
- PM Review 通过后 merge。
- high-risk merge 前确认最终 head 与 reviewed exact SHA 一致。
- normal PR 至少确认当前 head 未在 Review 后发生未审变更。
- destructive history/tag/release 操作受 pre-authorization。

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
