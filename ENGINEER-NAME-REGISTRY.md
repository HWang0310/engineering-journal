# Engineer Name Registry

本文件是**跨项目工程师名称保留（name reservation）的唯一 canonical 全局账本**。任何项目正式使用过的命名 engineer / PM 身份名称，今后不得分配给不同的新项目。

> 它是名称 **uniqueness / availability index**，不是全局人员名册。各项目自己的 `AGENTS.md` / canonical roster 仍是 membership、职责、状态与派工的权威来源；读取本文件**绝不授权**从其它项目调用工程师。

## 1. 比较规则（canonical）

- **比较键（normalized key）**：`name.casefold().strip()`（ASCII 名称统一转小写并去除首尾空白）。
- 大写 / 小写 / 大小写混合只算同一名称，如 `NOVA` 与 `nova` 冲突。
- 禁止用容易与既有名称视觉混淆的变体（如大小写伪装、同形字符、额外空白/连字符）规避占用。
- 中文名称按全角字符原样归一化比较，同样去除首尾空白。

## 2. 已保留名称（initial seed，来源已核实）

以下名称来自各项目 default-branch canonical roster（AGENTS.md 的 Project Engineer Roster 表格）。某项目缺失 AGENTS.md roster 或历史文档出现角色标签，不视为独立 canonical claim。历史上用过的名称（包括已退役、已改名、已归档项目的名称）**永久保留，不得复用**。

| Canonical name | Normalized key | 已核实 canonical project claims | Notes |
| --- | --- | --- | --- |
| Apex | apex | `engineering-journal` | |
| Atlas | atlas | `deep-talk-studio` | |
| Axiom | axiom | `revenue-ontology-ai`; `deeptalk-handdrawn-animation` | **既有历史重名**，双方均 active |
| Curator | curator | `revenue-ontology-ai` | `deeptalk-handdrawn-animation` 中为 former name（2026-09-09 Owner 批准 `Curator` → `Inkstone`）；已退役旧名永久保留 |
| Forge | forge | `deep-talk-studio`; `hwang_github` | **既有历史重名**，双方均 active |
| Foundry | foundry | `engineering-journal` | |
| Helix | helix | `revenue-ontology-ai` | |
| Inkstone | inkstone | `deeptalk-handdrawn-animation` | 由 `Curator` rename 而来（2026-09-09） |
| Ledger | ledger | `hwang_sryg` | |
| Mason | mason | `revenue-ontology-ai`; `deeptalk-handdrawn-animation` | **既有历史重名**，双方均 active |
| Nexus | nexus | `deep-talk-studio` | |
| NOVA | nova | `revenue-ontology-ai` | canonical casing 大写；比较不分大小写 |
| Orion | orion | `deep-talk-studio` | |
| Rivet | rivet | `revenue-ontology-ai`; `deeptalk-handdrawn-animation` | **既有历史重名**，双方均 active |
| Sage | sage | `engineering-journal` | |
| Scout | scout | `xhs-product-lab` | |
| Scribe | scribe | `deep-talk-studio` | |
| Steward | steward | `engineering-journal` | |

合计 **18** 个已保留名称。表格中的 `Notes` 如实标注既有重名与改名历史；**不自动重命名或重新分配任何现有身份**。

## 3. 占用新名称的流程（canonical）

1. **命名前查重**：为新的 project engineer / PM / durable identity 取名前，先 fresh 读取本文件（默认分支最新版本）。
2. **只提出可用名称**：候选名的 normalized key 不得与 registry 中任何已保留名称相同或视觉混淆。
3. **Owner 批准**：新增 / 改名 durable engineer identity 属于 Owner personnel decision，必须获得 Owner 明确批准后才能写入项目 roster。
4. **merge 前 re-read**：PR 中的 reservation 在 merge 到 global default branch 前不是最终状态。merge 前、写入新项目 roster 前，重新读取 live registry，确认候选名仍未占用。**两个并发 PR 不能同时 claim 同一个 normalized name；冲突即 STOP，改选其它已批准名称。**
5. **Registration 必须 durable 且可复现**：占用登记以 merge 后的 GitHub 记录为准；聊天记录不是 booking。
6. **永久占用**：已退役、已改名、被替代、归档项目的名称也永久保留，不得用于新项目。

## 4. 边界

- 本文件不创建新的全局人员组织，不替代、不削弱任何 project-scoped roster。
- 不把 backend / role label 当 engineer identity 登记（如 `ChatGPT`、`Codex`、`TeleAgent`、`Writer`、`Reviewer`）。
- 使用本 registry **永远不授权**从另一个项目 dispatch 工程师；项目 staffing 只由该项目 canonical roster 决定。
- 新发现的合法历史名称可带 source reference 回填，不要求批量迁移既有项目。