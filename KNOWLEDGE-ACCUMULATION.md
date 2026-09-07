---
AIGC:
  ContentProducer: '001191110102MAD55U9H0F10002'
  ContentPropagator: '001191110102MAD55U9H0F10002'
  Label: '1'
  ProduceID: 'eb162262-87bc-45c2-a207-4d0b7ac39706'
  PropagateID: 'eb162262-87bc-45c2-a207-4d0b7ac39706'
  ReservedCode1: 'cd362df1-1f91-4f09-bab6-2cee41b70140'
  ReservedCode2: 'cd362df1-1f91-4f09-bab6-2cee41b70140'
---

# Knowledge Accumulation

本仓库的目标不是“记得更多”，而是让未来项目少重复踩坑、少重复解释、少重复做同样的工程判断。

## 什么值得沉淀

优先记录已经体现跨项目价值的内容：

- 多次出现的工程失败模式及稳定解决办法。
- Agent 协作与任务拆解中被证明有效的工作方式。
- Git / worktree / branch / Review / merge 的稳定实践。
- 平台、框架、运行环境中长期存在的约束。
- 测试、验证、回归、发布中可复用的质量门槛。
- 架构、Contract、数据边界、安全边界等高影响决策。
- 能明显降低未来 Prompt 歧义、重复施工、状态丢失或工程返工率的规则。

## 什么不值得沉淀

不要因为“发生过”就记录：一次性小 bug、普通 commit/命令/测试、原始聊天全文、完整 Prompt 历史、暂时想法、单项目临时细节、账号凭据、客户数据、敏感业务数据或私有日志。

## 三类知识

### Standard

已经成为默认执行规则的内容，进入根目录标准文件。例如：角色类型与项目级命名、工程师数量决策、Task ID/幂等生命周期、exact-SHA Review、TeleAgent Prompt 与 GitHub-native handoff。

### Pattern

经过至少一次完整实践验证、未来类似项目可以复用，但还不需要成为全局强制规则的内容，进入 `patterns/`。状态可用 `Proposed / Proven / Deprecated`。

### Decision

存在多个合理方案，而且未来很可能再次问“为什么当时这样选”的高影响决定，进入 `decisions/`。

## 从项目经验升级为全局标准

一个经验可以升级为 Standard，当它满足多数条件：

- 在多个任务/项目中重复出现，或在复杂真实项目中充分验证且明显具有跨项目价值；
- 规则稳定，不依赖临时技术栈；
- 能降低质量风险、重复施工、状态丢失、返工或沟通成本；
- 可以写成清晰触发条件和行为规则；
- Project Manager Role 能给出可执行验收方式。

升级后更新对应根目录标准，不在多个 Pattern 中重复维护。

## 项目经验反向沉淀原则

项目级规范可以比全局规范更具体、更激进。Project Manager Role 应识别其中已经被真实工程证明有效的做法，并判断是否反向升级到全局。

反向沉淀时区分：

- **跨项目机制**：如 Task ID、幂等执行、状态恢复、GitHub-native handoff，可升级为全局 Standard。
- **项目结构优化**：如某个 multi-repo 项目更积极地多线并行，保留为项目级覆盖，不机械升级为全局默认。
- **项目角色命名**：具体名字只属于项目，不反向沉淀成全局固定名称；可沉淀的是“角色类型固定、项目名称项目化”的命名机制。
- **产品/架构专属规则**：特定 contract、runtime、业务对象模型只留在目标项目。

目标是让全局规范吸收成熟方法，而不是把一个项目的局部名字或全部习惯复制到其他项目。

## 维护原则

- 新规则尽量修改现有标准，不无限增加文件。
- 同一概念只有一个 canonical 定义，其他文档通过链接引用。
- 规则冲突时明确优先级，不保留两套模糊版本。
- 过期规则标记替代关系；历史价值低时可以删除。
- `JOURNAL.md` 只记录工程规范体系重大变更，不作为项目流水账。

## Automation-first maintenance

对于稳定、可机械判断且自动化成本合理的规则，应优先下沉为 test、lint、typecheck、schema validation、CI、script、preflight 或 automated gate，而不只留文字提醒。

- 能机器可靠检查的成熟规则优先下沉为自动化检查。
- 自动 gate 成熟后，减少或删除对应的人工重复 checklist 提醒。
- 语义判断类规则（架构正确性、业务意图、可维护性、是否真正满足 Owner 目标、可接受风险）通常仍需要 Project Manager Role / Reviewer 人工判断，不下沉为伪自动化。
- 规范维护时应清理过时、重复、已被新规则取代或已被自动 gate 替代的人工规则，避免规范体系只增不减。

## 当前已沉淀的基线（2026-09）

- 全局固定 Project Manager / Deep Engineering / Primary Execution / Secondary Execution 等职责类型，但**不固定项目角色名字**；每个项目维护自己的角色映射。
- Primary + Secondary Execution Role 为常规主力，Deep Engineering Role 用于深水升级。
- 每个新阶段由 Project Manager Role 明确 0 / 1 / 2 名执行工程师以及是否需要 Deep Engineering 专项介入。
- 不假并行；主动寻找安全并行机会，但无共享状态、无文件重叠、无分支依赖才并行。
- one Writer + read-only Reviewer 处理共享关键区域。
- GitHub remote 与 exact SHA 为工程事实基线。
- 一步一验收，Review 结论使用 `PASS / HOLD / NEEDS_CORRECTION`。
- 正式任务使用唯一 Task ID，并以 Task ID 贯穿 Prompt、执行、Git 结果与 Review。
- Prompt 状态与真实任务状态分离；发送状态不明时使用 `SEND_STATUS_UNKNOWN` + `STATUS_PROBE_ONLY`。
- 正式 Prompt 具有幂等预检；同 Task ID 已完成时返回 `ALREADY_COMPLETED`。
- TeleAgent Prompt 高结构化、低歧义、分步骤、带 scope、验收和 Git 证据。
- 能访问目标 GitHub 时优先 GitHub-native handoff；Owner 只需报告项目角色名 + Task ID 完成，Project Manager Role 自行核验 remote。
- 高风险工作使用 remote exact-SHA Review，Agent 自报 PASS 不能替代独立验收。