# Standards Journal

只记录这套工程规范与知识体系本身的重大变化，最新在前。普通项目进度不进入这里。

## 2026-09

- **2026-09-01 — 增加新会话启动语提醒：** 当 Curator 判断应为新项目开独立 ChatGPT 会话、需要 handoff 到全新会话、或为了隔离上下文建议新开会话时，必须主动向 Owner 提供可直接复制的启动语：`请参考 https://github.com/HWang0310/engineering-journal 的工程规范，开发 XXX 项目。` 已知项目名时直接替换 `XXX`；已知项目仓库时可一并附上。该规则属于事件触发的会话切换提醒，不依赖 Owner 自己记忆。
- **2026-09-01 — 增加工程师数量决策规则：** Curator 在新项目、阶段切换、重大阻塞或依赖变化时，必须重新判断需要 0 / 1 / 2 名执行工程师，并向 Owner 明确报告本阶段工程师配置。默认 1 名 Writer；只有存在真正独立、无写入冲突、可分别验收的工作流时才使用 Mason + Rivet 两人并行；Axiom 作为深水工程师或专项 Reviewer 按需介入，不自动充当第三个普通 Writer。
- **2026-09-01 — 新会话启动协议建立：** 新增 `NEW-SESSION-BOOTSTRAP.md`，并把 README / AGENTS 变成显式入口。以后用户只需说“请参考 engineering-journal 的工程规范，开发 XXX 项目”，新 ChatGPT / Agent 会话就应先读取规定文件、确认 remote 基线、自动进入 Curator 项目经理模式，并继承 Axiom/Mason/Rivet 路由、TeleAgent Prompt、并行、Git、exact-SHA Review 和 PASS/HOLD/NEEDS_CORRECTION 等规则，而不再要求用户重复说明这些习惯。
- **2026-09-01 — 仓库重新定位为跨项目工程规范库：** 从“个人开发月度日志/项目状态模板”转为长期 engineering standards + reusable knowledge。正式沉淀 Curator / Axiom / Mason / Rivet 协作模型、双 TeleAgent 并行边界、GitHub remote/exact-SHA 事实规则、一步一验收、PASS/HOLD/NEEDS_CORRECTION、TeleAgent Prompt/Handoff 规范以及质量优先的能力路由原则。删除旧的 monthly/project roadmap 与初始搭建脚手架。

## 2026-08

- **2026-08-09 — 初始仓库建立：** 创建 `engineering-journal`，最初用于保存个人开发计划、项目记录与决策。该定位已于 2026-09-01 被更聚焦的“跨项目工程规范与知识积累库”取代。
