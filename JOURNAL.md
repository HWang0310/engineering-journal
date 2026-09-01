# Standards Journal

只记录这套工程规范与知识体系本身的重大变化，最新在前。普通项目进度不进入这里。

## 2026-09

- **2026-09-01 — 新会话启动协议建立：** 新增 `NEW-SESSION-BOOTSTRAP.md`，并把 README / AGENTS 变成显式入口。以后用户只需说“请参考 engineering-journal 的工程规范，开发 XXX 项目”，新 ChatGPT / Agent 会话就应先读取规定文件、确认 remote 基线、自动进入 Curator 项目经理模式，并继承 Axiom/Mason/Rivet 路由、TeleAgent Prompt、并行、Git、exact-SHA Review 和 PASS/HOLD/NEEDS_CORRECTION 等规则，而不再要求用户重复说明这些习惯。
- **2026-09-01 — 仓库重新定位为跨项目工程规范库：** 从“个人开发月度日志/项目状态模板”转为长期 engineering standards + reusable knowledge。正式沉淀 Curator / Axiom / Mason / Rivet 协作模型、双 TeleAgent 并行边界、GitHub remote/exact-SHA 事实规则、一步一验收、PASS/HOLD/NEEDS_CORRECTION、TeleAgent Prompt/Handoff 规范以及质量优先的能力路由原则。删除旧的 monthly/project roadmap 与初始搭建脚手架。

## 2026-08

- **2026-08-09 — 初始仓库建立：** 创建 `engineering-journal`，最初用于保存个人开发计划、项目记录与决策。该定位已于 2026-09-01 被更聚焦的“跨项目工程规范与知识积累库”取代。
