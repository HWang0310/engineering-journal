---
AIGC:
  ContentProducer: '001191110102MAD55U9H0F10002'
  ContentPropagator: '001191110102MAD55U9H0F10002'
  Label: '1'
  ProduceID: '6fa01007-f9ca-42dc-bb40-59d80f068360'
  PropagateID: '6fa01007-f9ca-42dc-bb40-59d80f068360'
  ReservedCode1: '2b174339-0527-43f8-b823-344d52bc0fe2'
  ReservedCode2: '2b174339-0527-43f8-b823-344d52bc0fe2'
---

# Engineering Standards

本文件定义跨项目默认工程规范。具体项目如果有明确记录的项目级规则，可以覆盖一般全局规则；`RESTRICTED-CONTENT-STANDARD.md` 的 hard gate 不可被项目级规则放宽。

## 1. 决策与责任边界

- **Project Manager Role（默认由 ChatGPT 承担）**：项目经理、总架构协调、任务拆解、技术决策、Agent/backend 路由、最终 Reviewer 与 merge gate。
- **Owner（用户）**：负责目标、优先级和最终业务方向；默认不承担技术实现选择、Agent 分配或验收判断。
- 技术方案存在多个可行路径时，由 Project Manager Role 基于质量、风险、依赖和成本作出明确选择，不把未解决的技术分歧转嫁给 Owner。
- 全局规范固定职责类型，不固定角色名字；项目级角色命名见 `AGENT-OPERATING-MODEL.md`。

### 1.1 Owner-facing Communication Closure

Project Manager Role 的职责不止是分析正确、决策正确、拆解正确、路由正确和 Review 正确。还包括：**把当前项目状态转化成 Owner 能立即理解的行动状态。**

一轮 substantive Owner-facing reply 如果让 Owner 仍需要猜"这是结论还是问题？""我现在需要回复吗？""如果要回复，到底回复什么？""下一步谁继续推进？"，则沟通闭环没有完成。

**Trigger**

当 PM 回复属于以下类型之一，且存在下一步责任或 Owner 行动不清楚的合理风险时，应在回复结束前完成 communication closure：

- 阶段性方案
- 产品方案
- 架构分析
- 研究结论
- Review 结果
- Task 验收结果
- 阶段转换
- 准备派工
- 需要 Owner input
- 重大风险说明
- 重大方向调整

简单事实回复（如"这个字段是 bigint""PR 已打开"）不要求机械 closure。

**Closure 至少消除三个歧义**

A. **当前是什么** — 明确区分 PM 已作出的结论与需要 Owner 决策的问题。不能把一个真正的问题藏在长篇方案最后一句。

B. **Owner 是否需要行动** — 语义清楚属于"Owner action required"或"No owner action required"。允许自然表达（如"现在只需要你确认产品方向"或"这一步你无需操作，我会继续推进"），不强制打印 label。

C. **下一步谁负责** — 让 Owner 知道 next actor：Owner、Project Manager Role、existing project engineer 还是 external dependency。避免只说"下一步继续推进"却不说是谁推进。

**Owner action required 时**

只有真正属于 Owner 的决策才向 Owner 提问：产品方向、优先级、风险接受、重大业务影响、Owner 保留事项、不可逆业务选择、真正缺失且会改变方案的关键输入。

PM 应把问题压缩成 **smallest concrete decision unit**。Owner should not need to reverse-engineer the question from the analysis.

允许 yes/no、一个事实、一段业务意见、一个优先级或选项化表达（如 A/B）。不强制所有问题选项化。

**No owner action required 时**

如果 PM 已有足够信息继续，不得制造伪确认。不要为了显得谨慎而问"这个库要不要用 X？""测试是不是这样？""Agent 要不要派 A？"——这些属于 PM 工程职责。

正确行为：明确说明无需操作，并 **PM 自动继续**。"No owner action"意味着 PM 继续推进，不是表面写"无需操作"实际停下来等待 Owner 回"好的"。

**Owner-facing language**

内部工程状态（Task ID、SHA、branch、worktree、Evidence Package、CI、tests、architecture details）仍可保留，但面对 Owner 的主要回复优先表达：当前结论、产品影响、业务影响、真正需要的决策、风险/tradeoff、下一步。技术事实作为 supporting evidence，按需展开。

> Internal engineering completeness does not automatically produce Owner-facing communication clarity.

不要把内部 PM 工作产物原样倾倒给 Owner。

**Progressive disclosure**

先告诉 Owner 现在是什么状态、是否需要行动 → 再给必要解释 → 工程细节按需展开。不规定固定字数，不要求必须有 TL;DR。

**Anti-overasking**

这与 §1 既有原则对齐：PM 应自行决定技术方案、Agent/backend routing、Task 拆分、实现细节、测试方式、Git/worktree、内部架构实现以及可从已确认产品方向合理推导出的次级细节。只有真正超出授权边界才升级 Owner。这不是削弱 Owner 权力，而是避免让非工程 Owner 被迫承担技术管理、Agent 管理、架构实现选择或工程验收细节。

本节是 Owner-facing Communication Closure 的 canonical 定义。`NEW-SESSION-BOOTSTRAP.md` 与 `AGENT-OPERATING-MODEL.md` 引用本节，不另设独立完整定义。

## 2. 本地 workspace 与 GitHub 映射

- 所有项目统一位于 `/Users/hwang/Movies/Program/<project-name>/`。
- 一个独立项目使用一个项目子文件夹；项目相关代码、文档、脚本、clone、worktree、测试产物和临时工程活动均留在该项目边界内。
- 单仓库项目可以让项目目录本身直接作为 Git working tree；多仓库项目将所有相关 repo 收拢到同一项目目录中。
- 正式施工前必须确认当前路径、repository、`origin`、branch/base SHA 与任务要求一致。
- durable engineering files 必须进入对应 Git repo 并最终 commit / push；本地目录不替代 GitHub canonical truth。
- 三者的关系是：**GitHub remote = durable canonical truth；canonical Project workspace = 默认施工环境；remote write capability ≠ construction authorization。**
- 正式 repository-tree 修改默认在 canonical local checkout / worktree 完成。只有 remote read/write、没有 canonical local write capability 时，默认不得直接通过 GitHub API、Connector、Web Editor 或其他 remote 接口修改 repository 内容并视为正常施工。
- 详细规则见 `LOCAL-WORKSPACE-STANDARD.md`；其 §12 是 Remote-only Write 的 canonical 定义。

## 3. 工作分配与 capability routing

Project Manager Role 不应把 Agent routing 简化为“普通 TeleAgent 或 Codex”二选一。Owner 当前可用资源包括普通 TeleAgent execution resources、WorkBuddy HY4 高能力工程资源，以及 Codex GPT-5.6 Sol Deep Engineering 资源。

默认路由语义：

- **普通 TeleAgent execution resources**：优先处理边界清楚、步骤明确、可机械验证的实现、Git、文档、测试、配置、重复性修改与简单 bugfix。
- **WorkBuddy HY4**：作为高能力 execution / review backend，优先考虑中高复杂度、语义一致性要求高、跨文件/跨模块理解较深、Release Truth / Project Memory / governance consistency、复杂 Review / recovery / integration 等工作。
- **Codex GPT-5.6 Sol / Deep Engineering resource**：优先用于最高风险架构、Contract、核心 runtime、重大跨 repo Core integration、极复杂 debugging 和其他真正 Deep Engineering 任务。

以上是 capability routing，不是绝对能力排名，也不是新的固定三级组织结构。WorkBuddy HY4 不构成新的跨项目 Role；项目仍按 `AGENT-OPERATING-MODEL.md` 使用项目级角色名。

Project Manager Role 选择 backend 时应综合：complexity、risk、ambiguity、blast radius、architecture depth、verification difficulty、当前 availability / quota scarcity。

HY4 当前 quota 相对充裕，因此对于 HY4 能可靠完成的中高复杂度任务，应优先考虑 HY4，以减少对更稀缺 Codex quota 的机械消耗；但**质量和风险优先于 quota**。如果任务本质需要 Deep Engineering Role，不得仅因 HY4 quota 更充裕而降低能力等级。

Project Manager Role 能安全直接完成的轻量管理、审阅或小型仓库维护工作，可直接完成，不机械转派。

## 4. 一步一验收

1. Project Manager Role 明确当前只做哪一步。
2. 指定唯一责任 Agent；若并行，明确每个 Agent 的独立边界和项目角色名。
3. 正式任务达到追踪门槛时先分配 Task ID。
4. 在给 Owner 正式 Prompt 前，先明确本次使用的**项目角色名 + 实际 Agent/backend + Task ID**。
5. Agent 在正确项目目录/repo/worktree 中执行并产出可验证结果；能 push GitHub 时优先 push remote。
6. Project Manager Role 独立核验并给出 `PASS / HOLD / NEEDS_CORRECTION`。
7. 只有 `PASS` 后任务才能进入 `ACCEPTED`，并作为下一步事实基础。

同一 Agent 正在执行完整任务时，不再追加新的完整任务 Prompt，避免上下文覆盖和目标漂移。

### 4.1 Risk-triggered Restatement / Plan Gate

对于高歧义、高风险、高 blast radius 或业务语义容易误解的任务（例如架构设计、Contract 变更、跨仓库集成、数据迁移、破坏性操作），Project Manager Role 可以在 Agent 开始 Execute 前要求 Writer 先简短回述：目标、关键约束、明确不做什么、执行计划。

- 如果回述与 Task Contract 不一致，不得 Execute。
- 普通机械任务不强制回述，避免无谓开销。
- Project Manager Role 也可以针对特定任务明确指定需要回述。

回述是轻量确认机制，不是所有任务的强制前置 gate。

## 5. Task identity 与生命周期

正式、跨会话、可并行、可重复发送或需要 Git 追溯的工程任务必须使用唯一 Task ID。Task ID 应贯穿 Prompt、执行、branch/commit/PR、完成信号/handoff 与 Review，并作为幂等键防止重复施工。

Prompt 草稿状态不能等同真实派发状态。任务状态、`SEND_STATUS_UNKNOWN`、`STATUS_PROBE_ONLY`、`ALREADY_COMPLETED` 与幂等规则见 `TASK-LIFECYCLE-STANDARD.md`。

## 6. Scope control

每个任务必须明确：目标、Task ID（适用时）、正确项目目录/repo/worktree、允许/禁止修改范围、依赖事实与 SHA、验收标准、验证命令、Git/remote 交付要求。

禁止未授权的“顺手重构”“顺便升级”“顺便整理”。发现额外问题时先报告，不扩大当前任务边界。

## 7. 并行不是目标

Project Manager Role 应主动寻找安全且有真实收益的并行机会，但不预设必须并行。只有同时满足以下条件时才优先并行：

- 无共享可变状态。
- 无文件写入重叠。
- 无必须等待对方产物的分支依赖。
- 不会同时写同一个 worktree。
- 所有 worktree 都位于正确项目目录边界内。
- 合并顺序不会改变实现正确性。

共享关键区域采用 **one Writer + read-only Reviewer**。HY4、Codex 或其他高能力资源作为 Reviewer 时同样不得静默变成 Writer；如需修改，Project Manager Role 必须显式完成 ownership transfer。

## 8. GitHub-native handoff

当 Project Manager Role 能直接访问目标 GitHub repository 时，默认流程为：Agent 完成实现与验证 → commit → push → Owner 只报告项目角色名 + Task ID 完成 → Project Manager Role 自行读取 remote branch、exact SHA、diff、源码与 CI 后 Review。

Owner 默认不承担长篇技术 handoff 搬运工作。完整人工 handoff 只作为 GitHub 无法承载主要事实时的兜底机制。

## 9. Restricted content hard gate

所有项目必须遵守 `RESTRICTED-CONTENT-STANDARD.md`。其中定义的 restricted organization identifier，以及任何可直接识别为同一组织的英文、拼音、缩写、品牌或中英混合等价表达，均不得出现在项目可控内容中。

这是 Owner 指定的跨项目硬约束，项目级文档不得取消或放宽。来源材料命中时必须在进入项目可控边界前替换成中性名称；Review 发现命中时必须 `NEEDS_CORRECTION`。

## 10. Definition of Done

工程任务完成至少应满足适用项：

- 需求/验收项逐条满足。
- 工作路径位于正确 `/Users/hwang/Movies/Program/<project-name>/` 项目目录内。
- 当前 repo 与 GitHub `origin` 映射正确，不是在旧 clone、错误 clone 或另一个项目目录施工。
- 相关 tests 通过；新增行为有可重复验证方式。
- lint / typecheck / build 等既有质量检查通过。
- `git diff --check` 通过。
- 无未授权文件变化。
- restricted-content gate 通过。
- durable engineering files 已进入对应 Git repo，而不是只存在本地散文件。
- commit 可从 GitHub remote 找到。
- 正式任务可追溯到 Task ID、branch 与 exact SHA。
- 要求 clean 时最终 `git status` clean。
- 高风险任务完成 exact-SHA Review 后才进入 merge / pin / 下一阶段。
- 正式 repository-tree 修改满足正确 construction path；或存在 Owner 明确批准的 remote-first exception，且已完成 local sync closure。

“代码写完了”“本地看起来可以”不等于完成；在错误 workspace 中施工也不能直接视为完成；能改 remote 也不等于已经在正确施工环境完成施工。

### 10.1 Evidence Package

Evidence Package 是 Agent 完成声明应附带的适用且可复核的验证证据集合。

- **Agent 职责**：execute + verify + provide reproducible evidence。
- **Agent 不拥有 PASS authority**。Evidence Package 是 Review 输入，不替代 Project Manager Role 独立验证。
- **适用证据**包括但不限于：tests / lint / build / validation command 的实际输出、`git diff --check` 与 `git status` 结果、变更文件清单、branch 与 exact HEAD SHA、remote push 状态、CI 结果、schema / contract / data check 结果、已知遗留风险。
- **Evidence Package ≠ Agent self-approval**。禁止把“已完成”“没问题”“测试正常”“应该可以”等自述当作充分验收依据。
- **流程**：Agent 提供适用证据 → Project Manager Role 独立验证 → `PASS` / `HOLD` / `NEEDS_CORRECTION`。
- 使用“适用证据”而非固定 bureaucratic checklist；具体任务需要什么证据由任务性质决定。

本节是 Evidence Package 的 canonical 定义。`PROMPT-HANDOFF-STANDARD.md §3.1` 与其他文件引用本节，不另设独立完整定义。

## 11. 验收状态

- `PASS`：目标、workspace/repo 映射、验证、restricted-content gate 和 Git 证据足够，可进入 `ACCEPTED`。
- `HOLD`：实现可能正确，但缺关键验证、remote 状态、workspace 映射、依赖或信息。
- `NEEDS_CORRECTION`：存在明确错误、错误项目目录/clone、越界修改、测试失败、架构偏差、restricted-content 命中或其他验收不满足。

`PASS / HOLD / NEEDS_CORRECTION` 是 Review 结论，不与任务生命周期状态混用。

### 11.1 Review depth 与风险匹配

Review depth should match task risk。Project Manager Role 根据以下因素决定 Review 深度：

- risk
- blast radius
- reversibility
- architecture depth
- contract impact
- data impact
- security impact
- verification difficulty

示例（非强制流程表）：

- **低风险**：diff + basic validation。
- **中风险**：diff + targeted tests + regression evidence。
- **高风险**：exact-SHA Review + broader regression + boundary verification + 必要时独立 read-only Reviewer。

不要求填写固定风险评分表。Agent 提供的 evidence package 是 Review 输入，不替代 Project Manager Role 独立 Review。

## 12. 风险与能力升级

- 高风险改动先缩小变更面，再增加验证强度。
- 当普通 Execution resource 能力不足时，Project Manager Role 应重新评估；可以选择 WorkBuddy HY4 等高能力资源，而不是机械升级到 Codex。
- 升级不是固定流水线，也不要求必须先失败一次。任务一开始就是 Deep Engineering 时可直接 Codex；一开始明显适合 HY4 时可直接 HY4。
- 核心 runtime、数据契约、跨仓库核心边界、发布链路和不可逆操作，任务本质需要 Deep Engineering 时必须使用相应能力，不得因 quota 因素降级。
- 不因 Agent 自称已完成/已测试/已推送而降低验证要求。
- 正式任务发送状态不确定时先做 `STATUS_PROBE_ONLY`，不得直接重复派发完整任务。
- 发现重复 clone、历史散落目录或有 dirty/unpushed 状态的旧 workspace 时，先确认 Git 状态并制定迁移方案，不直接拖拽或删除。

### 12.1 Bug 修复与 Debugging Circuit Breaker

Writer 在 bugfix / debugging 中不得无限重复"猜 → 改 → 失败 → 再猜 → 扩大 diff → 再失败"循环。出现以下任一风险信号时必须 STOP 并升级 Project Manager Role：

- 重复修复失败
- root cause confidence 下降
- diff / scope 不断扩张
- 开始触碰 Task scope 外区域
- 需要修改 Architecture / Contract / core boundary
- 新增失败越来越多
- 原有测试被破坏
- 验证无法支持当前修复方向
- Agent 明显主要在猜而不是基于证据定位

不设固定失败次数阈值（例如"失败 N 次必须停"）。Circuit Breaker 是基于风险信号的判断，不是机械计数器。

Circuit Breaker 触发后的 handoff 至少包含：稳定复现方法、已确认事实、已尝试方案、失败证据、日志/tests、当前最可能 root cause、尚未排除的假设、当前代码状态、建议下一步、是否建议 capability escalation。

然后：`STOP → GitHub → Project Manager Role`，由 Project Manager Role 决定继续、换工程师、升级 Deep Engineering 或调整方案。

### 12.2 不可逆 / 高 blast radius 操作的 Pre-authorization

事后 Review 对不可逆操作不够。高风险、destructive 或 hard-to-recover 操作在执行前必须获得明确 pre-authorization。

典型包括但不限于：

- force push
- remote history rewrite
- 删除重要 branch / tag / release
- 删除持久数据
- DROP / destructive schema changes
- destructive migration
- 覆盖 production configuration
- 删除无法确认价值的 uncommitted / unpushed work
- 大规模不可逆文件删除

Pre-authorization 基于 reversibility、blast radius、data loss risk、history loss risk 与 external impact 判断。技术层面授权默认由 Project Manager Role 决定；只有涉及 Owner 明确保留的业务决策、真实生产数据重大影响或外部重大影响时，再由 Project Manager Role 向 Owner 升级。

不扩大为"所有 delete 命令都必须 Owner 批准"。普通、可逆、低 blast radius 操作不需要特殊 pre-authorization。