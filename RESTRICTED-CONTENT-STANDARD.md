# Restricted Content Standard

本文件定义一条跨项目、不可被项目级默认规则放宽的硬性内容约束。

## 1. Restricted organization identifier

禁止对象用 Unicode code points 定义，避免规范文件本身出现该组织名称：

`U+6E56 U+5317 U+7535 U+4FE1`

该 code-point sequence 所表示的中文组织名称为**全局禁用标识**。

同时禁止任何与该组织名称等价或可直接识别为同一组织的英文直译、英文品牌写法、英文机构全称/分支机构写法、拼音/罗马化、常见英文缩写/首字母组合、品牌等价变体以及中英混合可识别变体。

如果对某个写法是否属于等价表达存在不确定性，**fail closed：不要使用，改成中性占位名称。**

## 2. 禁止出现的范围

该禁令适用于所有项目可控内容，包括 source code、注释、文档、测试、fixture/sample/mock/demo data、UI/CLI 文案、文件名/目录名、branch/tag/release、commit message/body、PR/Issue、Agent Prompt/handoff/project state、log/report/artifact/export、可编辑截图文字、URL/path/config/metadata，以及任何将被 commit、push、发布、展示、导出或长期保存的内容。

## 3. 外部输入处理

上游原始数据、用户材料或外部系统结果包含禁用标识时：

- 不得原样复制进入项目仓库、文档、测试样例、报告或发布物；
- 在项目可控边界内先脱敏/替换，再持久化或展示；
- 默认替换为中性名称，例如 `目标组织`、`客户组织`、`区域运营方`、`target organization`；
- 不得为了保留真实示例把禁用标识写入 fixture、注释或历史样例。

## 4. Agent 执行规则

Project Manager Role、Deep Engineering Role、Primary Execution Role、Secondary Execution Role 在生成任何项目内容前都必须继承本规则；具体项目使用自己的角色名。

正式 Prompt 在适用时应明确提醒：不得产生 restricted organization identifier 及其英文、拼音、缩写或品牌等价表达；来源材料命中时先替换中性名称；发现现有内容违反本规则时，scope 允许则清理，scope 不允许则立即报告给 Project Manager Role，不继续扩散。

## 5. 验收与 Definition of Done

本规则属于硬性 gate：

- Project Manager Role 在重要提交、Review、release、handoff 或交付前检查项目可控内容；
- 发现任一命中时 Review 不得为 `PASS`；
- 必须 `NEEDS_CORRECTION`，清理后重新 Review；
- 即使 tests / build / lint 全部通过，只要违反本规则，也不满足 Definition of Done。

## 6. 扫描原则

自动化扫描实现不得要求把禁用中文名称或其英文等价名称以明文形式提交到仓库。可以使用 Unicode code-point 组合、编码 denylist、hash/external secret rule set、CI 运行时构造匹配词，并由 Project Manager Role 做语义 Review，覆盖英文、拼音、缩写和品牌变体。

自动扫描只是辅助；最终按“是否能直接识别为同一组织”执行 fail-closed 判断。

## 7. 优先级

1. Owner 对该禁令的最新明确指令；
2. 本文件；
3. 项目级内容规范；
4. 其他工程默认规则。

项目仓库不得通过项目级文档自行取消或放宽该禁令。