# Standards Journal

只记录这套工程规范与知识体系本身的重大变化，最新在前。普通项目进度不进入这里。

## 2026-09

- **2026-09-01 — 从 DeepTalk 反向沉淀正式任务机制：** 将已经在复杂 multi-repo 项目中验证成熟的三类做法升级为跨项目 Standard：①正式/跨会话/可重复派发任务使用唯一 Task ID，并区分 Prompt 草稿、发送、运行、push、Review、ACCEPTED 等真实状态；发送状态不明时使用 `SEND_STATUS_UNKNOWN` + `STATUS_PROBE_ONLY`，不得直接重发；②Task ID 同时作为幂等键，Agent 执行前必须检查已有结果，同一任务已完成时返回 `ALREADY_COMPLETED`；③当 Curator 能访问目标 GitHub 时默认使用 GitHub-native handoff，Owner 只需报告 Agent + Task ID 完成，Curator 自行核验 branch、exact SHA、diff、源码与 CI，完整人工 handoff 降级为兜底机制。DeepTalk 的“三条工程线积极并行”等项目结构优化没有机械升级为全局默认，全局仍坚持只在真正独立时并行。
- **2026-09-01 — 增加新会话启动语提醒：** 当 Curator 判断应为新项目开独立 ChatGPT 会话、需要 handoff 到全新会话、或为了隔离上下文建议新开会话时，必须主动向 Owner 提供可直接复制的启动语：`请参考 https://github.com/HWang0310/engineering-journal 的工程规范，开发 XXX 项目。` 已知项目名时直接替换 `XXX`；已知项目仓库时可一并附上。该规则属于事件触发的会话切换提醒，不依赖 Owner 自己记忆。
- **2026-09-01 — 增加工程师数量决策规则：** Curator 在新项目、阶段切换、重大阻塞或依赖变化时，必须重新判断需要 0 / 1 / 2 名执行工程师，并向 Owner 明确报告本阶段工程师配置。默认 1 名 Writer；只有存在真正独立、无写入冲突、可分别验收的工作流时才使用 Mason + Rivet 两人并行；Axiom 作为深水工程师或专项 Reviewer 按需介入，不自动充当第三个普通 Writer。
- **2026-09-01 — 新会话启动协议建立：** 新增 `NEW-SESSION-BOOTSTRAP.md`，并把 README / AGENTS 变成显式入口。以后用户只需说“请参考 engineering-journal 的工程规范，开发 XXX 项目”，新 ChatGPT / Agent 会话就应先读取规定文件、确认 remote 基线、自动进入 Curator 项目经理模式，并继承 Axiom/Mason/Rivet 路由、TeleAgent Prompt、并行、Git、exact-SHA Review 和 PASS/HOLD/NEEDS_CORRECTION 等规则，而不再要求用户重复说明这些习惯。
- **2026-09-01 — 仓库重新定位为跨项目工程规范库：** 从“个人开发月度日志/项目状态模板”转为长期 engineering standards + reusable knowledge。正式沉淀 Curator / Axiom / Mason / Rivet 协作模型、双 TeleAgent 并行边界、GitHub remote/exact-SHA 事实规则、一步一验收、PASS/HOLD/NEEDS_CORRECTION、TeleAgent Prompt/Handoff 规范以及质量优先的能力路由原则。删除旧的 monthly/project roadmap 与初始搭建脚手架。

## 2026-08

- **2026-08-09 — 初始仓库建立：** 创建 `engineering-journal`，最初用于保存个人开发计划、项目记录与决策。该定位已于 2026-09-01 被更聚焦的“跨项目工程规范与知识积累库”取代。
