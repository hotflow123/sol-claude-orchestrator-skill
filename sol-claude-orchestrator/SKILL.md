---
name: sol-claude-orchestrator
description: 启用 Claude-first 路由：始终由 5.6 Sol 主控，仅在用户对当前任务明确批准 subagent 后，优先把适合派发的实现、验收或高风险复核交给 Claude。用于用户明确调用本 Skill、说“Claude-first”或要求在 Claude 额度充足时优先使用 Claude；调用本 Skill 本身不构成 subagent 批准。
---

# Claude-first 路由

## 模式开关

- `Claude-first`：Sol 保持主控；需要派发且已获批准时，优先选择 Claude。
- `取消 Claude-first` 或 `GPT-only`：立即停止 Claude 优先，后续执行 `$sol-gpt-orchestrator` 规则。
- 路由偏好可在当前会话保持，但 subagent 授权只对用户明确批准的当前任务、角色和范围有效。

## 硬规则

- 默认使用 Sol Medium 单人完成日常开发、普通修改、Bug、UI 和常规 Agent 任务。
- 默认 subagent 数量为 0。未经当前任务明确批准，不启动任何 subagent；最多批准 2 个，默认顺序执行。
- Claude-first 只改变派发优先级，不要求必须派发，也不等于批准派发。
- 同一时间只允许一个模型写代码。两个 subagent 必须承担不同职责，不得重复实现或重复审查。
- Sol 负责需求、范围、架构、验收契约、最终 diff、关键证据和最终结论。
- Ultra 仅在用户对当前任务明确指定时使用，不自动升级。

## Sol 档位

- Sol Medium：日常主控和默认实现。
- Sol Extra High：仅用于架构设计、复杂根因、跨模块状态、长链路任务或已获批准的多 Agent 编排。
- 跑一个已知测试命令或短浏览器流程时由 Sol 直接执行；不要为简单工具操作启动 subagent。

## Claude 角色

- Sonnet 5：明确的常规实现、测试执行、自适应浏览器操作和 UI 证据整理。
- Opus 4.8 High：架构已冻结但代码语义复杂的精密实现；不用于机械修改或普通验收。
- Fable 5 xHigh：安全、许可、迁移、状态协议和复杂耦合的单点复核；只在编码前审架构或编码后审 diff，二选一。

Sonnet 与 Opus 不同时写代码。Fable 不跑测试、不操作浏览器、不重做整个任务。

## 最多两个 subagent

按任务只选择必要组合：

1. 普通批量开发：Sonnet 实现；通常不启用第二个。
2. 复杂实现：Opus 实现；通常不启用第二个。
3. 大型 UI：Sonnet 或 Opus 实现，加 Sonnet 只读浏览器验收。
4. 高风险开发：Sonnet 或 Opus 实现，加 Fable 单点复核。
5. Sol 已实现：按需要只选 Sonnet 验收或 Fable 复核。

如果 Fable 已占第二个名额，测试和浏览器验收由 Sol 执行。

## 执行流程

1. Sol 读取代码，确认工作区、允许路径、禁止路径、接口和验收标准。
2. Sol 先判断单人是否更便宜；足够时直接完成。
3. 需要派发时，说明模型、唯一职责、范围和收益，等待当前任务明确批准。
4. 只向获批模型传递最小任务包，不传完整对话或无关仓库内容。
5. 执行模型返回文件清单、diff 摘要和证据；验收模型只返回 `PASS`、`FAIL` 或 `BLOCKED` 及原始证据。
6. Sol 读取最终 diff，处理发现，完成最小关键路径复验并给出结论。

## 验收边界

- 单命令、单页面、短流程由 Sol 直接执行。
- 多套件、多视口、多角色或长流程才考虑 Sonnet 验收。
- 验收代理不得修改代码、隐藏失败或自行改变验收标准。
- 模型意见冲突时先补测试、日志和规范证据；证据仍不足时请求用户决策，不追加第三模型。
