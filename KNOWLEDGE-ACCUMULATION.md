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

- **唯一授权触发**：Owner 明确说“保存为知识库包”“把刚刚的业务知识归档到知识库包”或等效的明确持久化指令。SQL 排查、业务问答、普通总结、提及知识库、要求“整理一下”、提供上传文件或聊天纠正都**不构成自动保存授权**。不主动回扫历史会话、批量归档或定时同步。
- **内容边界**：只整理本项目适用的数据分析知识（如收入/计费/合作分成、指标口径、库表字段、SQL/程序血缘、核对及工作流）。以实际可核实来源为依据，包含主题、适用范围、源文件/SQL 版本或路径、已证实规则、必要示例、边界、冲突与 UNKNOWN/TODO；区分 SOURCE_FACT、DOCUMENT_FACT、CANDIDATE 与人工确认事实，绝不将推测伪装为已核实口径。
- **归档目标由项目配置**：项目可选择 Owner 授权的**仅本机归档**，或 Owner 明确批准的**本机 + GitHub 私有仓库双端归档**；路径和仓库由项目级 canonical 控制面指定。全局规范不默认授权所有项目上传 GitHub，也不创建跨项目共享私有资料库。同名不同内容文件不得静默覆盖。
- **双端归档一致性**：启用双端模式后，明确“保存为知识库包”的一次指令才允许本次归档同步。先审核拟归档内容是否允许进入指定私有仓库，核实 repo 可见性与当前授权；只把**同一份通过审核的 Markdown 内容**写入本机指定目录与 GitHub 指定受控目录。两端文件内容用 SHA-256 或等效字节校验核对；记录文件路径、GitHub commit/PR 和校验结果。分支/PR 中的内容未 merge 到指定默认分支前，不算 GitHub 正式归档完成。
- **失败与环境限制**：若本机目录不可写、GitHub 归档未 merge、两端字节不一致或内容不符合仓库数据安全约束，报告 `PARTIAL / HOLD` 和缺失的具体端，不得声称“双端归档成功”。无本机写入能力时，可提供可下载 Markdown 和准确的 GitHub 状态，但不能虚报已保存到 Owner Mac；不允许未经批准将敏感内容脱敏成不同版本却宣称“两端相同”。
- **独立授权边界**：获准的**GitHub 文件备份**仅是归档目标，并**不授权**自动导入产品知识中心、外发给远程模型、启动数据库查询/变更，或写入正式 Business Memory。这些动作分别遵守项目自己的导入、数据安全、业务变更与人工确认规则；文件归档不代表业务口径已 HUMAN_CONFIRMED。
- **隐私与安全**：私有仓库也不是生产/客户数据仓。不得上传凭据、客户/生产明细、内部敏感材料或项目禁止入库的标识到 GitHub/PR/logs/未经授权服务。业务定义仅在项目 Owner 已明确批准且内容审核通过时允许入库；任何不适宜入库的片段先 `HOLD`，处理后再进行双端同内容保存。依各项目 Restricted Active Surface、secret 与 data governance 边界处理。

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
