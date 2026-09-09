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
