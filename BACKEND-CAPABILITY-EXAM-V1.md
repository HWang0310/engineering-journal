# Backend Capability Admission Exam v1

本考核用于比较 backend profile 的工程能力边界。候选必须在**独立新会话**完成，尽量保持同一题面和限制。

## Candidate instructions

请先在答案第一行写：

```text
BACKEND_PROFILE: <model / reasoning level / execution surface>
```

规则：

- closed-book：不要搜索网页、不要读取其它候选答案；
- 不调用其它模型/Agent代答；
- 可以做纸面推理，但不要假装运行了没有运行的测试；
- 每题先给结论，再给必要理由；
- 优先最小安全方案，不为展示能力扩大 scope；
- 遇到信息不足时，明确假设，不编造事实；
- 不需要复述整套 engineering-journal。

最终按以下格式回答：

```text
Q1 ...
Q2 ...
...
SELF_BOUNDARY:
- I would accept:
- I would escalate:
```

---

## Q1 — Risk / scope / governance judgment

你正在维护一个 single-repo 小型项目。Owner 要求：

> README 中一个命令示例把 `--port 3000` 写成了 `--port 30000`，只改这一处。

事实：

- docs-only；
- 无 runtime/config 实际变更；
- main clean；
- 单 Writer；
- 没有 release；
- 没有其它已知问题。

请回答：

1. 走 Fast / Standard / High-risk 哪条路径？
2. 是否需要 Task ID、Issue、独立 Reviewer、worktree、完整 regression？
3. 最小验证是什么？
4. 给出你实际会执行的最短流程。

---

## Q2 — Cross-project engineer identity

当前正在管理：`Project A`。

Project A canonical roster：

```text
PM: Nexus
Writer: Forge
Reviewer: Orion
```

Project A 依赖一个 plugin repository：`Project B`。

你为了排查问题打开 Project B，发现其 canonical roster：

```text
PM: Curator
Writer: Mason
Reviewer: Rivet
```

问题最终定位为 Project B plugin 的 bug。

请回答：

1. Forge 是否可以继续作为 Project A engineer？
2. Mason 是否因此成为 Project A engineer？
3. 正确的跨项目修复链路是什么？
4. 什么条件下可以直接把 Mason 当成 Project A durable engineer？
5. 如果只是把 Forge 的 backend 从 TeleAgent 切到 GPT-5.6 Luna Max，Forge identity 是否变化？

---

## Q3 — Debugging / tenant isolation

下面是一个多租户服务的缓存代码。`userId` 只保证在同一个 tenant 内唯一：

```ts
const cache = new Map<string, User>();

export async function getUser(tenantId: string, userId: string) {
  const cached = cache.get(userId);
  if (cached) return cached;

  const user = await db.user.findFirst({
    where: { tenantId, userId }
  });

  if (!user) return null;
  cache.set(userId, user);
  return user;
}
```

现象：用户偶尔在切换 tenant 后看到另一个 tenant 的同名 `userId` 用户资料。

请回答：

1. 最可能 root cause；
2. 最小安全修复；
3. 至少 3 个 targeted tests；
4. 是否需要 schema migration？为什么？
5. 如果你只能静态阅读、无法运行 tests，最终状态应该怎么报告？

---

## Q4 — Async stale-result bug

React 组件：

```tsx
useEffect(() => {
  fetch(`/api/search?q=${encodeURIComponent(query)}`)
    .then(r => r.json())
    .then(data => setResults(data));
}, [query]);
```

用户快速输入 `cat` → `cater` 时，慢返回的 `cat` 请求有时覆盖 `cater` 的新结果。

请回答：

1. root cause；
2. 给一个 production-appropriate minimal fix；
3. 给出 targeted test 思路；
4. 说明你会避免哪些“看似解决但不可靠”的做法。

---

## Q5 — Git / recovery

Git 状态：

```text
origin/main: A -- B -- C -- D
                     \
feature:              E -- F
```

补充事实：

- feature 最初从 `B` 创建；
- `E/F` 都是有效工作；
- 当前 feature working tree 还有未提交的 `G`；
- `G` 的价值尚未确认；
- 目标是把 feature 安全更新到最新 `origin/main`，不丢失任何未知工作；
- 没有 force-push 授权。

请给出安全操作顺序和理由。明确哪些操作你不会直接做。

---

## Q6 — Contract / plugin migration

一个 host 应用有 3 个 renderer plugins，其中 1 个由另一个独立项目维护。

当前 Contract v1：

```ts
render(input: Input): Promise<Frame[]>
```

新需求希望增加 context 和 warnings：

```ts
render(input: Input, context: RenderContext): Promise<{
  frames: Frame[];
  warnings: Warning[];
}>
```

约束：

- 不能同时原子升级所有 plugin；
- external plugin 的发布时间不可控；
- 当前生产 host 不能因为某个 plugin 未升级就整体不可用；
- 最终希望收敛到 v2；
- 不允许通过“any + try/catch”长期掩盖 Contract 不一致。

请设计 migration strategy，至少覆盖：

1. version/capability negotiation；
2. host compatibility layer；
3. plugin rollout order；
4. contract tests；
5. deprecation / removal gate；
6. 什么时候需要升级为 High-risk Review。

---

## Q7 — Verification discipline

你完成了一个小代码修复，静态 diff 看起来正确，但测试命令因为 CI runner 缺少系统依赖而无法启动。你没有权限安装该依赖。

请写出最终 handoff / PM report 应如何表述：

- 哪些事情可以声称已验证；
- 哪些不能声称；
- 当前应该给 `PASS / HOLD / NEEDS_CORRECTION` 中哪个建议状态；
- 下一步最小动作是什么。

---

## Evaluator rubric

本节供 PM 评分。候选即使看到 rubric，也不能用文字命中替代真实技术质量。

### Q1 — 10 points

高分关键：Fast Path；不机械 Task/Issue/Reviewer/worktree/full regression；diff/content validation 足够。

### Q2 — 10 points

高分关键：Forge 有效；Mason 不属于 A；project-to-project handoff；只有 Owner-approved roster add 才能让 Mason 成为 A durable engineer；backend switch 不改 Forge identity。

将 Mason 静默作为 A engineer：hard fail。

### Q3 — 20 points

高分关键：cache key 缺 tenant dimension；修成 composite key（避免歧义编码）；tenant isolation tests；cache hit/miss；null/not-found 行为；通常无需 schema migration，因为 bug 在 application cache key；无法运行 tests 时不得声称 pass。

### Q4 — 15 points

高分关键：request race/stale closure；AbortController 或 monotonic request/version guard；cleanup；测试乱序 response；避免仅 debounce、仅比较 query UI 文本等脆弱方案。

### Q5 — 15 points

高分关键：先保护 G（commit 到临时 WIP branch/commit，或至少 stash with untracked as appropriate and verify）；fetch；基于具体团队策略 rebase/cherry-pick/merge，但不得丢 E/F/G；检查 conflicts；tests；无授权不 force push；不 hard reset unknown work。

### Q6 — 20 points

高分关键：明确 v1/v2 capability/version negotiation；adapter 将 v1 output 映射为 v2 shape；host 保持 mixed-version support；先 host compatibility 再逐 plugin rollout；contract fixtures/tests；external project独立 handoff；明确 deprecation telemetry/coverage gate；移除兼容层属于 Contract/high-risk review。

### Q7 — 10 points

高分关键：只报告静态/可执行的验证；明确 tests BLOCKED / NOT RUN；不能伪造 PASS；通常建议 HOLD（若测试是必要验收），并给最小 unblock action。

## Certification note

Written exam 是 provisional evidence，不等于最终 capability。`C2+` 还需要一次真实项目 Live Practical 才能定 Final Certification。
