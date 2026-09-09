# Local Workspace Standard

本文件定义 local workspace、remote construction 和并行隔离。核心目标是避免错误 clone / dirty state / 并行覆盖，同时不让低风险文本工作承担不必要的本地流程。

## 1. Canonical local project root

Owner 本地项目默认位于：

`/Users/hwang/Movies/Program/<project-name>/`

代码、构建、测试、migration、运行时调试等需要本地环境的工程活动优先在该 Project workspace 完成。

GitHub remote 是 durable truth；本地 workspace 是执行环境。

## 2. 避免重复 clone

- 已有 canonical checkout 时，不在 Agent 默认 workspace 再维护第二个活跃 clone。
- 发现旧 clone / dirty / unpushed work 时，先确认价值再处理。
- 不因整理目录覆盖未提交或未 push 的有效成果。

## 3. Single repo / multi repo

- single repo 可以让 project root 直接作为 working tree；
- multi repo 放在同一 project root 下；
- repo ↔ origin 映射必须能明确恢复。

## 4. Worktree

**worktree 不是单 Writer 小任务的默认要求。**

需要独立 worktree 的典型情况：

- 同 repo concurrent Writers；
- 当前 checkout 有不能混入的 active work；
- high-risk change 需要强隔离；
- PM 有具体 recovery/isolation 理由。

单 Writer 串行、clean checkout 的普通任务可以直接使用现有 working tree + branch。

## 5. Capability vocabulary

| Capability | Meaning |
| --- | --- |
| `READ_LOCAL` | 读取 canonical Project workspace |
| `WRITE_LOCAL` | 在 canonical Project workspace 写文件/执行 Git |
| `READ_REMOTE` | 读取 GitHub remote |
| `WRITE_REMOTE` | 通过 GitHub API/Connector 等修改 remote |

能力描述当前执行主体，不代表某产品永远具备该能力。

## 6. Construction modes（canonical）

### 6.1 LOCAL_DEFAULT

以下默认 local-first：

- source code；
- tests；
- build/toolchain changes；
- runtime config；
- refactor；
- migration / SQL execution；
- binary / generated artifact；
- security-sensitive change；
- 依赖本机环境验证的工作；
- multi-writer / complex merge；
- high-risk change。

local-first 时在开始修改前确认适用的 repo / origin / branch/base / dirty state。

### 6.2 SAFE_REMOTE_FIRST

PM / Agent 可以**无需每次单独向 Owner申请 remote-first 许可**，直接通过 GitHub API / Connector 处理低风险 repository-tree text change，但必须同时满足：

- Markdown/docs，或小型纯文本 metadata/config；
- 可逆；
- 无 build/runtime/production/security/migration 依赖；
- 无 binary；
- 无 concurrent Writer conflict；
- remote base/head 明确；
- diff 可直接从 GitHub 验证；
- 不依赖某个本地 dirty/unpushed state 才能判断正确性；
- restricted-content Active Surface gate 可执行。

典型：README/Markdown 修正、docs cross-reference、非运行时文本 metadata。

“文件很小”不自动等于 safe。会影响 runtime、deployment、security、release、migration 的 config 仍走 local-first / higher-risk path。

SAFE_REMOTE_FIRST 推荐使用 branch + commit + PR；极小且项目允许直接提交时可更轻，但 PM 必须保留可恢复 remote fact。

### 6.3 GitHub control-plane

读取 branch/SHA/diff、Issue/PR、Review comment、labels/state、review request、merge 等 governance action 始终可以 remote 执行，不因没有 `WRITE_LOCAL` 而禁止。

## 7. Remote-first 与 local convergence

SAFE_REMOTE_FIRST 完成后**不要求为了流程立即创建单独 local-sync task**。

如果后续需要在 canonical local checkout 继续施工：

1. 先 fetch；
2. 检查 local dirty/unpushed state；
3. 安全 reconcile remote；
4. 不覆盖有效本地工作。

也就是说，local convergence 在“下一次真正需要 local construction 时”自然完成，而不是每个 remote docs fix 的关闭 gate。

如果 remote change 与现有本地有效工作发生冲突，进入 recovery flow，不粗暴 reset。

## 8. Remote-first exception outside safe boundary

不满足 SAFE_REMOTE_FIRST 的 repository-tree write，默认回到 local-capable Writer。

只有 Owner 明确要求 remote-only 处理且 PM 判断风险可接受时，才允许特殊 remote-first exception；高风险/不可逆操作仍受 `ENGINEERING-STANDARDS.md` §12.2。

## 9. Validation

- local code work：检查适用的 path/repo/origin/branch/status + tests。
- SAFE_REMOTE_FIRST：确认 base/head、changed files、diff、content validation、restricted Active Surface；无本地 runtime 依赖时不补跑本地回归。
- parallel Writers：验证隔离和 merge order。

## 10. Backward compatibility

老项目不要求立即整理全部历史 clone/worktree。下一次自然进入相关本地工程活动时再收敛。

不要为了符合目录标准对仍有 dirty/unpushed work 的旧目录做 destructive cleanup。
