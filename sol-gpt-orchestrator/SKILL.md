---
name: sol-gpt-orchestrator
description: 启用 GPT-only 路由：禁止 Claude，由 5.6 Sol Medium 默认单人主控，仅在用户对当前任务明确批准后使用最多两个 GPT 家族 subagent。用于用户明确调用本 Skill、说“GPT-only”“取消 Claude-first”或明确禁止 Claude 的任务；调用本 Skill 本身不构成 subagent 批准。
---

# GPT-only 路由

## 模式开关

- `GPT-only` 或 `取消 Claude-first`：禁止调用 Claude 模型、Claude CLI、Claude API 和 Claude 子任务。
- `Claude-first`：仅在用户明确切换时停止 GPT-only，并采用 `$sol-claude-orchestrator`。
- 路由偏好可在当前会话保持，但 subagent 授权只对用户明确批准的当前任务、角色和范围有效。

## 硬规则

- 默认使用 Sol Medium 单人完成日常开发、普通修改、Bug、UI 和常规 Agent 任务。
- 默认 subagent 数量为 0。未经当前任务明确批准，不启动任何 subagent；最多批准 2 个，默认顺序执行。
- 同一时间只允许一个模型写代码。两个 subagent 必须职责不同，不得重复实现或重复审查。
- Sol 负责需求、范围、架构、验收契约、最终 diff、关键证据和最终结论。
- Ultra 仅在用户对当前任务明确指定时使用，不自动升级。

## Sol 档位

- Sol Medium：日常主控和默认实现。
- Sol Extra High：仅用于架构设计、复杂根因、跨模块状态、长链路任务或已获批准的多 Agent 编排。
- 单个测试命令或短浏览器流程由 Sol 直接执行，不为简单工具操作启动 subagent。

## GPT 角色

- Luna Medium/High：边界冻结后的批量编码、补测试、机械迁移和明确构建修复。
- Luna Max：目标和验收完全明确、要求高质量一次性交付的代码审查、重要文档、方案优化或限定实现。
- Terra 或 GPT-5.5：多测试套件、构建、扫描和结构化日志证据；只读验收，不修改代码。

Luna 执行与 Luna Max 审查不默认叠加。机械工作不使用 Luna Max，简单测试不启动 Terra 或 GPT-5.5。

## 最多两个 subagent

按任务只选择必要组合：

1. 普通批量开发：Luna 实现；通常不启用第二个。
2. 一次性交付：Luna Max 完成一个明确产物；Sol 验收。
3. 大型开发：Luna 实现，加 Terra 或 GPT-5.5 只读验收。
4. Sol 已实现：按需要只选 Luna Max 审查或一个验收代理。

## 执行流程

1. Sol 读取代码，确认工作区、允许路径、禁止路径、接口和验收标准。
2. Sol 先判断单人是否更便宜；足够时直接完成。
3. 需要派发时，说明模型、唯一职责、范围和收益，等待当前任务明确批准。
4. 只向获批模型传递最小任务包，不传完整对话或无关仓库内容。
5. 执行模型返回文件清单、diff 摘要和证据；验收模型只返回 `PASS`、`FAIL` 或 `BLOCKED` 及原始证据。
6. Sol 读取最终 diff，处理发现，完成最小关键路径复验并给出结论。

## 验收边界

- 单命令、单页面、短流程由 Sol 直接执行。
- 多套件、多视口、多角色或长流程才考虑验收代理。
- 验收代理不得修改代码、隐藏失败或自行改变验收标准。
- 模型意见冲突时先补测试、日志和规范证据；证据仍不足时请求用户决策，不追加第三模型。
