# Restricted Content Standard

本文件定义 restricted organization wording 的跨项目 hard gate，并区分**当前可控内容**与**历史不可变证据**，避免普通历史对象永久阻塞当前工程。

## 1. Restricted identifier

禁止对象仍使用 Unicode code points 定义：

`U+6E56 U+5317 U+7535 U+4FE1`

同时禁止任何可直接识别为同一组织的英文、罗马化、缩写、品牌或中英混合等价表达。

对**新产生内容**存在语义不确定时，默认使用中性名称，不扩散可识别表达。

## 2. Two-surface model（canonical）

Review 必须区分：

- **ACTIVE SURFACE**：当前或未来可由项目控制、修改、发布或继续传播的内容。
- **LEGACY EVIDENCE**：已经形成、主要承担 audit / recovery / provenance 作用、且不应为普通 wording 问题破坏其 identity 的历史对象。

历史存在不等于当前许可；当前 hard gate 也不等于必须重写全部历史。

## 3. ACTIVE SURFACE

以下当前/新内容继续严格禁止 restricted content：

- current repository tree；
- current filenames / directory names；
- current repo slug / description；
- current config / metadata；
- new branch / tag / release names与说明；
- new commit message / body；
- new PR / Issue / comment / review content；
- new docs / code / tests / fixtures / samples / artifacts / exports；
- new Prompt / handoff / project-state 内容；
- current UI/CLI/log/report；
- 其它新创建、重新发布或重新复制的 project-controlled content。

外部输入命中时，在进入 Active Surface 前先替换为中性名称。

Active Surface 任一命中：

`ACTIVE_SURFACES: FAIL`

必须修正后才能对当前交付给 `PASS`。

## 4. LEGACY_EVIDENCE_SET

Owner 可以为已有项目批准一个明确 cutoff（例如 exact commit、日期或已存在对象清单）。cutoff 之前符合条件的 immutable/history-bearing 对象可以登记为：

`LEGACY_EVIDENCE_SET`

可包含：

- 已存在的 historical commits / commit messages；
- published tags / release snapshots；
- 已关闭的 historical PR / Issue；
- historical backup / audit snapshot；
- 其它为 recovery / provenance 保留 identity 的历史对象。

Legacy Evidence Set 必须满足：

1. **封闭集合**：cutoff 后不能继续增长；
2. **只用于 audit / recovery / provenance**；
3. **不是当前命名规范或新内容模板**；
4. 新文档引用历史对象时用 SHA / object ID / 中性描述，不重新复制 restricted wording；
5. 不能以“historical”为理由新建包含 restricted content 的 commit、tag、PR、Issue、backup 或 artifact；
6. 当前 tree/config/active metadata 不因“历史形成”自动变成 Legacy Evidence。

普通历史 wording 命中本身不再永久阻塞当前项目 PASS。

## 5. Gate output

Review 输出拆为两个维度：

```text
ACTIVE_SURFACES: PASS | FAIL
LEGACY_EVIDENCE_DISPOSITION: COMPLETE | INCOMPLETE
```

语义：

- 当前工程 `PASS` 的必要条件是 `ACTIVE_SURFACES: PASS`。
- 如果当前任务本身负责建立/审计 Legacy Evidence Set，则还需要完成该任务范围内的 disposition。
- 对普通无关任务，历史 inventory 尚未完全整理可以标为 `INCOMPLETE`，但不能因此把 Active Surface 已通过的普通改动永久判为失败。
- `INCOMPLETE` 不是继续传播 restricted content 的许可。

## 6. 什么时候需要 destructive historical purge

对于普通 organization-name / wording restriction，默认**不要求**：

- Git history rewrite；
- force push；
- commit SHA replacement；
- tag recreation；
- release snapshot recreation；
- historical PR / Issue destruction；
- historical backup rewrite。

只有存在真正需要物理删除的风险时升级 destructive purge，例如：

- credential / secret leakage；
- legally required deletion；
- security incident；
- highly sensitive personal/customer data；
- Owner 明确要求 physical historical removal。

destructive purge 属于 High-risk / irreversible operation，必须有独立 plan、blast-radius analysis、backup/recovery 方案和 Owner approval。相关 Git/history 操作还遵守 `ENGINEERING-STANDARDS.md` §12.2。

## 7. Scan scope

默认扫描与当前 change 相关的 Active Surface，不要求每个普通任务重新扫描全部 Git history。

Legacy history scan 只在：

- 首次建立 Legacy Evidence Set；
- audit / recovery 任务；
- 新发现历史风险；
- Owner 或 PM 有具体理由要求

时执行。

自动扫描不得把 restricted wording 明文写入仓库；可使用 code points、编码规则或外部运行时规则。自动扫描是辅助，语义 Review 仍需判断等价表达。

## 8. Backward compatibility

本标准对未来和当前 Active Surface 立即生效。旧项目不要求因为过去符合旧规则就批量重写 history。

老项目可以在下一次自然维护或首次遇到历史 gate 冲突时建立 Owner-approved cutoff 与 Legacy Evidence Set。除真实安全/法律风险外，不为了“清洁历史”制造 destructive migration。

## 9. Priority

1. Owner 当前明确指令；
2. 本文件；
3. 项目级内容规则；
4. 其它工程默认规则。

项目级规则不得放宽 Active Surface hard gate，但可以在本标准边界内定义自己的中性替代词和 Legacy Evidence cutoff。
