# Codex Deep-Engineering Rules

Codex / equivalent high-capability resource 主要承担 **Deep Engineering Role**。它不是所有“稍复杂”任务的默认答案。

## 1. 适用边界

优先用于：

- highest-risk architecture / Contract / API/schema；
- core runtime；
- high blast-radius refactor；
- major cross-repo integration；
- security-sensitive change；
- extreme debugging；
- migration / release critical review；
- PM 判断普通 execution resource 不足以可靠完成的深水任务。

普通 docs、机械 Git、明确 small bug、routine config、已有方案的重复实现不优先使用 Deep Engineering。

## 2. Backend 不是人员身份

使用 Codex 只是 capability routing。一个已有 engineer 临时或长期使用 Codex backend，不自动新增 project engineer，也不需要 Owner 为“模型切换”批准人员变化。

真正 add/remove/rename durable engineer identity 才遵守 `AGENT-OPERATING-MODEL.md` 的 Owner personnel boundary。

## 3. Task / Prompt

Task ID 仅在达到 `TASK-LIFECYCLE-STANDARD.md` §1 门槛时使用。

Deep Engineering Prompt 应提供与风险相称的 current facts/base、scope、failure evidence（如有）、acceptance boundary、forbidden changes 与 verification。

不要因为使用 Codex 就自动升级成完整企业式流程；如果任务本身低风险，仍可轻量。

## 4. Review

Deep Engineering Review 重点用于系统性风险和高风险边界。

- high-risk 对象尽量绑定 exact SHA；
- 验证事实，不接受 Writer 自述代替 evidence；
- `NEEDS_CORRECTION` 给最小必要修正；
- independent Reviewer 是风险工具，不是普通 PR 默认角色；
- Reviewer 默认只读，修改需显式 transfer Writer ownership。

## 5. Output

只回传 PM 做决定所需内容：

- conclusion；
- key risk/root cause；
- changed/reviewed boundary；
- applicable evidence；
- branch/exact SHA（高风险/需要时）；
- merge/next-stage recommendation。

Deep Engineering 的价值是提高最高风险环节确定性，不是增加流程层级。
