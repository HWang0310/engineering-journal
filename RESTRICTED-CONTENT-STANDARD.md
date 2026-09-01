# Restricted Content Standard

本文件定义一条跨项目、不可被项目级默认规则放宽的硬性内容约束。

## 1. Restricted organization identifier

禁止对象用 Unicode code points 定义，避免规范文件本身出现该组织名称：

`U+6E56 U+5317 U+7535 U+4FE1`

该 code-point sequence 所表示的中文组织名称为**全局禁用标识**。

同时禁止任何与该组织名称等价或可直接识别为同一组织的：

- 英文直译；
- 英文品牌写法；
- 英文机构全称或分支机构写法；
- 拼音、罗马化写法；
- 常见英文缩写、首字母组合或品牌等价变体；
- 中文、英文或中英混合的可识别变体。

如果对某个写法是否属于该组织的等价表达存在不确定性，**fail closed：不要使用，改成中性占位名称。**

## 2. 禁止出现的范围

该禁令适用于所有项目可控内容，包括但不限于：

- source code、变量默认值、常量、注释；
- README、PRD、ROADMAP、HANDOFF、AGENTS、设计文档；
- 测试、fixture、sample data、mock data、demo data；
- UI 文案、网页文本、CLI 输出、错误信息；
- 文件名、目录名、branch 名、tag、release title；
- commit message / commit body；
- PR / Issue 标题与正文；
- Agent Prompt、handoff、project state；
- log、report、generated artifact、export；
- screenshot 中由项目生成或可编辑的文字；
- URL slug、路径、配置值、metadata；
- 任何将被 commit、push、发布、展示、导出或长期保存的内容。

## 3. 外部输入处理

如果上游原始数据、用户提供材料或外部系统返回内容中包含禁用标识：

- 不得原样复制进入项目仓库、项目文档、测试样例、报告或发布物；
- 在项目可控边界内应先脱敏/替换，再持久化或展示；
- 默认替换为中性名称，例如 `目标组织`、`客户组织`、`区域运营方`、`target organization`；
- 不得为了“保留真实示例”而把禁用标识写入 fixture、注释或历史样例。

外部系统中已经存在且项目无法控制的原始内容，不代表项目可以再次复制或传播该标识。

## 4. Agent 执行规则

Curator / Axiom / Mason / Rivet 在生成任何项目内容前都必须继承本规则。

正式 Prompt 应在适用时明确提醒执行 Agent：

- 不得产生 restricted organization identifier；
- 不得产生其英文、拼音、缩写或品牌等价表达；
- 遇到来源材料含有该标识时先替换成中性名称；
- 发现现有项目内容违反本规则时，当前任务 scope 允许则清理；scope 不允许则立即报告给 Curator，不继续扩散。

## 5. 验收与 Definition of Done

本规则属于硬性 gate：

- Curator 在重要提交、Review、release、handoff 或项目交付前，应检查项目可控内容是否违反本规则；
- 发现任一命中时，Review 结论不得为 `PASS`；
- 应返回 `NEEDS_CORRECTION`，完成清理后重新 Review；
- 即使功能测试、build、lint 全部通过，只要违反本规则，也不满足 Definition of Done。

## 6. 扫描原则

自动化扫描实现不得要求把禁用中文名称或其英文等价名称以明文形式提交到仓库。

可以使用：

- Unicode code-point 组合；
- 编码后的 denylist；
- hash / external secret rule set；
- CI 中运行时构造匹配词；
- 由 Curator 进行语义 Review，覆盖英文、拼音、缩写和品牌变体。

自动扫描只能作为辅助。由于等价表达可能变化，最终仍由 Curator 按“是否能直接识别为同一组织”执行 fail-closed 判断。

## 7. 优先级

这是 Owner 明确指定的跨项目硬约束。

优先级为：

1. Owner 对该禁令的最新明确指令；
2. 本文件；
3. 项目级内容规范；
4. 其他工程默认规则。

项目仓库不得通过项目级文档自行取消或放宽该禁令。