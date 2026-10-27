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
- **写入目标**：由已启用项目自己的 canonical 配置指定其**Owner 授权的私有本地归档目录**；不因本规范创建一个跨项目共享私有资料库，也不默认写入 GitHub。已存在的同名文件若内容不同，不能静默覆盖。
- **实际执行**：仅收到本次明确保存指令、且执行环境有权限写指定目录时才真正落盘，并如实回报文件名/位置/结果。无法访问 Owner 本机时可交付 Markdown 文件供 Owner 保存，必须注明“未写入 Owner 本机”，不能把生成文件或回答文本声称为完成了本机保存。
- **独立授权边界**：归档 Markdown **不授权**自动导入产品知识中心、外发给远程模型、提交 GitHub、启动数据库查询/变更，或写入正式 Business Memory。以上动作分别遵守项目自己的导入、数据安全、业务变更与人工确认规则；仅文件归档不代表业务口径已 HUMAN_CONFIRMED。
- **隐私与安全**：不得因 Owner 要求归档，就将凭据、客户/生产明细、内部敏感材料写入 GitHub/PR/公开工件/未经授权远程服务。依各项目 Restricted Active Surface、secret 与 data governance 边界处理。

启用项目只需在其项目级 `AGENTS.md` 或等价 canonical 控制面提供作用域与具体本地目录，并**引用本节**；不要重复整套通用触发/授权规则。非启用项目无需加载本节，也不得因此自动开启知识采集。

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
