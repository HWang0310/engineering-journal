# Knowledge Accumulation

本仓库的目标不是“记录更多”，而是让未来工程减少重复判断和重复踩坑。

## 1. 什么值得沉淀

优先记录：

- 跨项目重复出现的失败模式与稳定解决方法；
- architecture / Contract / data / security boundary；
- 能显著降低返工、状态丢失、误施工或恢复成本的规则；
- 经过真实工程验证、未来仍会影响技术判断的模式。

不要因为“发生过”就沉淀：普通 bug、普通 commit、一次性命令、原始聊天、完整 Prompt、临时 reviewer/backend、单项目短期执行日志。

## 2. Standard / Pattern / Decision

- **Standard**：跨项目默认执行规则。
- **Pattern**：可复用但还不需要成为 hard default。
- **Decision**：未来会再次问“为什么这样选”的高影响选择。

同一概念只有一个 canonical definition；升级为 Standard 后删除/缩短重复定义。

## 3. Project Memory（项目侧）

Project Memory 只记录 durable truth。canonical 行为见 `ENGINEERING-STANDARDS.md` §13。

应该更新：

- architecture decision；
- milestone / stage；
- release；
- canonical Contract；
- durable roster identity change；
- major recovery fact。

默认不更新：

- ordinary bugfix；
- docs fix；
- correction round；
- temporary reviewer；
- temporary backend switch；
- ordinary refactor。

GitHub Issue / PR / commit history 已足以承载临时工程历史。

### 3.1 Current truth 与 historical provenance 分工

长期项目默认按职责分离：

- **Project Memory / current HANDOFF / control-plane**：现在是什么状态；
- **canonical Contract / ADR**：当前规则、边界与“为什么”；
- **OPEN Issue / OPEN PR**：当前 actionable execution / review state；
- **Issue / PR / commit history**：历史上发生了什么；
- **Archive / SUPERSEDED material**：保留历史材料和 provenance。

同一历史内容不要为了“方便恢复”重新复制到多个 current-truth surface。

Project Memory 不承担“给每个 closed Issue 做摘要”的职责；closed history 保留在 GitHub，需要时按 `NEW-SESSION-BOOTSTRAP.md` 的 `HISTORY_READ_TRIGGER` 定向读取。

旧 design/spec/plan 即使仍可搜索，只要已经 archived / superseded，就不能因为搜索命中而覆盖 current canonical Contract / ADR。

### 3.2 Owner-triggered data-analysis knowledge packs（canonical）

本节只约束**明确启用此工作流的数据分析 / 收入业务项目**，用于在日常对话中将经 Owner 指示的业务知识整理为可复核的 Markdown 知识库包；不适用于 DeepTalk 等视频/内容制作及其它无关项目。**知识库包是资料文件，不等于 Project Memory / 正式业务记忆。**

- **唯一授权触发（含固定一句话指令）**：Owner 在数据分析/收入业务会话中，且该会话或其**已授权的归档目的项目启用本工作流**时，明确说“保存为知识库包”“把刚刚的业务知识归档到知识库包”，或使用固定句式 **“参考项目规范 https://github.com/HWang0310/engineering-journal，把本知识整理成知识包”**，均视为**本次当前业务知识库包整理 + 该项目已配置归档目标执行**的明确授权，无须 Owner 再写详细 Prompt、重复提供已确认内容或另行授权普通文件保存。该短句**不授权**越过敏感信息审批、GitHub PR/merge gate、产品知识导入或 Business Memory 变更。普通 SQL 排查、业务问答、总结、仅提及知识库、单独说“整理一下”、提供上传文件或聊天纠正，均**不构成自动保存授权**；不得主动回扫无关历史会话、批量归档或定时同步。
- **内容边界**：只整理本项目适用的数据分析知识（如收入/计费/合作分成、指标口径、库表字段、SQL/程序血缘、核对及工作流）。以实际可核实来源为依据，包含主题、适用范围、源文件/SQL 版本或路径、已证实规则、必要示例、边界、冲突与 UNKNOWN/TODO；区分 SOURCE_FACT、DOCUMENT_FACT、CANDIDATE 与人工确认事实，绝不将推测伪装为已核实口径。
- **归档目标由项目配置**：项目可选择 Owner 授权的**仅本机归档**，或 Owner 明确批准的**本机 + GitHub 私有仓库双端归档**；路径和仓库由项目级 canonical 控制面指定。全局规范不默认授权所有项目上传 GitHub，也不创建跨项目共享私有资料库。同名不同内容文件不得静默覆盖。
- **本地落盘必须由本机 Agent 执行（PM dispatch gate）**：在 Owner 已明确触发归档、且项目配置要求本地保存时，负责该数据分析会话的 PM 必须主动把**实际 Mac 文件写入与本地校验**派给具有 Owner 本机目录实际访问权限的**当前项目 canonical roster 中具名执行工程师**，并在派工前遵守 `AGENT-OPERATING-MODEL.md` 的 named dispatch 与 `BACKEND-CAPABILITY-CERTIFICATION.md` 的最新 routing 规则；若来源数据分析项目没有本地可执行工程师，PM 应优先将归档执行作为**明确的跨项目 handoff**交给归档目的项目 PM，由目的项目按其自己的 canonical roster 选派有本机访问能力的具名工程师并独立审核本项目文件；来源 PM **不得直接冒用目的项目工程师身份或擅自向其它项目工程师派工**。若无法完成合法 handoff 或目的项目也没有 local-capable executor，则报告本机端 `PARTIAL / HOLD`，不让 Owner 无谓地手动中继工程交接。PM 可以整理知识包、协调 GitHub 归档与做最终 Review，但**仅完成 GitHub 文件/PR 绝不视为已本地保存**。工程师须交付实际存在的本地文件路径、写入结果和与 GitHub 审核版本的 SHA-256 比对证据；PM 独立验收两端状态后才能宣布 `DUAL_ARCHIVE_COMPLETE`。如果无法派发/无法本地写入，报告 `PARTIAL / HOLD` 并指明缺少本机端，不虚构本地完成。此为显式保存任务的执行路由要求，**不是**普通对话或 SQL 分析的自动派工/归档触发。
- **双端归档一致性**：启用双端模式后，明确“保存为知识库包”的一次指令才允许本次归档同步。先审核拟归档内容是否允许进入指定私有仓库，核实 repo 可见性与当前授权；只把**同一份通过审核的 Markdown 内容**写入本机指定目录与 GitHub 指定受控目录。两端文件内容用 SHA-256 或等效字节校验核对；记录文件路径、GitHub commit/PR 和校验结果。分支/PR 中的内容未 merge 到指定默认分支前，不算 GitHub 正式归档完成。
- **失败与环境限制**：若本机目录不可写、GitHub 归档未 merge、两端字节不一致或内容不符合仓库数据安全约束，报告 `PARTIAL / HOLD` 和缺失的具体端，不得声称“双端归档成功”。无本机写入能力时，可提供可下载 Markdown 和准确的 GitHub 状态，但不能虚报已保存到 Owner Mac；不允许未经批准将敏感内容脱敏成不同版本却宣称“两端相同”。
- **独立授权边界**：获准的**GitHub 文件备份**仅是归档目标，并**不授权**自动导入产品知识中心、外发给远程模型、启动数据库查询/变更，或写入正式 Business Memory。这些动作分别遵守项目自己的导入、数据安全、业务变更与人工确认规则；文件归档不代表业务口径已 HUMAN_CONFIRMED。
- **隐私与安全**：私有仓库也不是生产/客户数据仓。不得上传凭据、客户/生产明细、内部敏感材料或项目禁止入库的标识到 GitHub/PR/logs/未经授权服务。业务定义仅在项目 Owner 已明确批准且内容审核通过时允许入库；任何不适宜入库的片段先 `HOLD`，处理后再进行双端同内容保存。依各项目 Restricted Active Surface、secret 与 data governance 边界处理。

#### 3.2.1 一句话指令后的标准执行流程（PM-owned）

收到上述固定短句后，**当前业务会话的 PM 直接接管完整归档闭环**；不要再向 Owner 索取长 Prompt、让 Owner 决定文件名/Agent/任务分拆或手工传递工程交接。根据实际证据，依次执行：

1. **识别本次业务主题与范围**：`本知识` 指当前业务会话/明确引用的当前成果中**已分析、已验证、可复用**的相关知识；从当前对话及可访问的权威文件/SQL/已确认结果恢复，不擅自引用无关项目、推测跨会话未见事实。自然推导主题文件名 `<业务主题>知识库包.md`，同名版本冲突 STOP 或按明确版本命名。
2. **整理成可复用 Markdown**：以真实材料为准，包含业务背景及范围、已确认口径、计算和账期/单位规则、源表字段与关系/血缘、可复用 SQL/操作步骤、代表性已核验案例（脱敏）、验证证据及来源、适用限制、UNKNOWN/待确认/冲突。只写确有依据的部分；本业务不涉及的栏目可省略，不生成空模板、猜测或整段聊天流水账。
3. **来源与归档目的地严格分离（必检）**：先识别 `SOURCE_PROJECT`（SQL、程序、口径的证据仓库）和 `ARCHIVE_PROJECT`（Owner 已批准的知识包持久化仓库），**不得推定两者相同**。归档路径以**已获 Owner 授权的归档目的项目**的最新 `AGENTS.md` 为优先权威，而不是要求来源仓库先配置归档目录；来源会话无需独立工程仓库或名册。若此类收入/数据分析知识的批准目的地为 `HWang0310/revenue-ontology-ai`，即使用其本地 `完整知识库包/` 和 GitHub `docs/knowledge-packs/` 的当前配置，数生、合作分成等源码实际来自 `hwang_github` 也不影响。**已存在有效归档目标时，来源仓库缺少配置不是 `HOLD` 理由，不得据此向 Owner 重复索要启用授权或改仓库决定。** 检查本地/GitHub 是否有对应知识包并核对版本；按**目的仓库** Private Repository Security 筛查内容。若内部资料不能安全进入 GitHub，先 `HOLD` 并给出最小安全处理选项，不能上传原文或把两份不同文件称为一致。
4. **PM 必须实际派工/交接，不得停在建议**：需要 Mac 本机落盘时，按本节 PM dispatch gate 主动选派**有本机实际访问能力的当前项目具名 Agent**。来源会话无法合法派工时，且已识别归档目的项目，须通过可用的项目交接渠道**实际创建或提交可追踪的 GitHub-native handoff（Issue / PR 交接说明）**给目的项目 PM，由目的项目按自己的 roster 决定具名 Agent 和执行；不得跨项目直接指派。**“建议 Owner 为来源仓库新启用归档”或只交付可下载 Markdown 不能代替既有归档目的地的实际 handoff**。如果当前环境确实缺少发起交接/启动本机 Agent 的能力，要说明缺少的具体能力、已完成的工作与受阻操作，报 `PARTIAL / BLOCKED`；不能虚称已经派工，更不能为了弥补工具限制要求 Owner 重发长 Prompt。
5. **归档、Review 和双端核验**：让执行 Agent 保存本机实际文件；同一份审核通过的 Markdown 经目的 GitHub 项目 branch/PR 与 PM Review 归档。**PR merge 仍须遵守原有 Owner 授权 gate，不得把此短句解释为自动批准 merge**。完成 merge 后核实本机路径真实存在、目标 `main` 文件存在、两个 SHA-256 一致。
6. **简短结果回报**：只报告 `KNOWLEDGE_PACK`、`ARCHIVE_PROJECT`、`LOCAL_STATUS/PATH`、`GITHUB_STATUS/URL`、`HANDOFF/AGENT_STATUS`、`SHA256_MATCH`、`BLOCKERS`、`STATUS`。只有两端真实保存且校验一致才写 `DUAL_ARCHIVE_COMPLETE`；等待合并/不可达/冲突时写 `PARTIAL / HOLD`。**生成下载文件仅为 `DRAFT_READY`，不能把知识整理完成伪称为归档完成**；仅在出现真实需要 Owner 行使的审批权（如敏感内容入库或 PR merge）时才请求 Owner 最小决策。

上述步骤是**一次短指令的默认执行语义**，不改变本节前述项目启用范围、资料安全、产品导入和业务记忆人工确认边界。

启用项目只需在其项目级 `AGENTS.md` 或等价 canonical 控制面提供作用域、归档模式、具体本地目录，以及双端模式下的私有 GitHub 仓库/归档路径，并**引用本节**；不要重复整套通用触发/授权规则。非启用项目无需加载本节，也不得因此自动开启知识采集。

## 4. Project roster knowledge

具体 engineer 名字只属于目标项目，不反向沉淀为全局固定名字。

只有长期 durable named roster 才需要 GitHub Project Roster Memory；Lean 单 Agent / PM+Writer 项目可以不拟人化。canonical 规则见 `AGENT-OPERATING-MODEL.md`。

## 5. Automation-first maintenance

能机器可靠检查的成熟规则优先下沉为 test / lint / CI / script / preflight。自动 gate 稳定后删除重复人工 checklist。

语义判断（architecture、Owner intent、risk acceptance、maintainability）仍由 PM/Reviewer 判断，不伪自动化。

## 6. Governance simplification

规范本身也必须接受成本审查：

- 同一规则多处完整复制 → 合并为 one canonical definition；
- 已被更高层原则覆盖的旧 checklist → 删除或改 cross-reference；
- 低风险任务被高风险流程拖累 → 调整默认值而不是继续加例外；
- 不为规范维护本身制造无限 governance tasks。

## 7. Backward compatibility

新标准默认向前生效。旧项目、旧 Task、旧 handoff、旧历史对象不因规范升级自动要求 migration。

只有下一次自然维护需要相关 durable truth，或存在真实安全/法律风险时才专门处理历史。
