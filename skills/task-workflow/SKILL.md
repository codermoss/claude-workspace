---
name: task-workflow
description: 任务工作流调度系统 - 根据任务类型路由调度三大技能库，提供 3 个标准工作流组合
version: 3.0.0
---

# Task-Workflow - 任务工作流调度系统

> "政策和策略是党的生命，各级领导同志务必充分注意，万万不可粗心大意。"

## 设计思想

Task-Workflow 是**跨技能编排器**，调度三大技能库协同工作：

| 技能库 | 调度方式 | 体现位置 |
|--------|---------|---------|
| Qiushi（思想方法论） | 显式 Step 调用 | investigation / contradiction / mass-line / concentrate-forces / spark-prairie-fire / protracted-strategy / criticism |
| Superpower（工程纪律） | 显式 Step 调用 | brainstorming / writing-plans / executing-plans / TDD / systematic-debugging / karpathy / verification |
| Pensieve（项目知识库） | Pipeline 入口白盒调用 + 内部黑盒自治 | run-when-reviewing-code / run-when-committing / pensieve-wand agent |

**核心职责**：
- **编排层**：定义 Step 顺序、数据传递格式、质量门标准、跨 Step 失败回退
- **抽象边界**：调用技能/Pipeline 入口，不干预内部执行细节
- **铁律层**：5 条铁律 + Step 置信度门 + 红旗信号

**与 Qiushi/Pensieve 的区别**：
- Qiushi (workflows)：只定义编排，不包含验证逻辑
- Pensieve (Pipeline)：单点闭环，不做跨技能编排
- Task-Workflow：跨技能编排 + 质量门 + 失败回退

---

## 编排器边界

| 编排器负责 | 编排器不负责 |
|-----------|-------------|
| 何时触发哪个技能 | 技能内部执行方法 |
| 步骤间数据传递（传递哪些字段） | 字段的格式化模板 |
| 质量门（通过/不通过） | 技能使用原则 |
| 失败回退路径 | Pipeline 内部 Task 顺序 |
| 调用 Pensieve Pipeline 入口 | Pensieve Pipeline 内部 Task 1-N 实现 |

## Pensieve init 自动处理

每个 Workflow 的 Step 0 自动检测 `<project>/.pensieve/`：
- 不存在 → 静默执行 `pensieve init`（幂等操作，不询问用户）
- 已存在 → 跳过

`pensieve init` 是幂等的（init.md 保证不覆盖已有数据），用户无需手动触发。

## 铁律

1. **调查完成前不提方案** — investigation-first 完成标准表全部满足前，禁止提出解决方案
2. **计划完成前不写代码** — writing-plans 完成前禁止开始执行
3. **验证通过前不称完成** — verification 命令输出 "0 failures" / "exit 0" 前禁止声称完成
4. **质量门未通过不推进** — 当前步骤未通过质量门时禁止进入下一步
5. **循环 ≥ 3 次必须停止** — 除非用户明确要求，否则质疑架构设计

## 红旗信号

执行过程中出现以下信号时立即停止当前步骤：

- **调查阶段**：使用模糊词或未经验证的假设描述问题
- **设计阶段**：用"先这样，后面再改"妥协于已知问题
- **执行阶段**：用"应该可以了"替代实际验证
- **循环阶段**：每次循环都发现新问题（架构级问题的信号）

## 工作流路由

### 3 个标准工作流

| 编号 | 名称 | 适用场景 | 步骤数 | Pensieve 调用 |
|------|------|---------|--------|---------------|
| 1 | 通用执行 | 新功能迭代、Bug 修复，程序生命周期核心 | 11 步（Step 0-10） | reviewing-code(1) + committing(1) + wand |
| 2 | 新项目启动 | 从零开始，目标已知路径未知（战略层） | 9 步（Step 0-8） | reviewing-code(1) + committing(1) + wand |
| 3 | 品味提升 | 代码可工作但品味不佳（重构层） | 11 步（Step 0-10） | reviewing-code(2) + committing(1) + wand |

### W1 通用执行 技能串联

```
Step 0 Pensieve 知识库初始化检测（自动）
  → investigation-first(含 wand) → contradiction → mass-line → concentrate-forces
  → 分支[新功能: brainstorming → executing-plans(含 TDD) | Bug: systematic-debugging]
  → karpathy-guidelines
  → pensieve.run-when-reviewing-code (Pipeline 入口)
  → criticism-self-criticism
  → verification-before-completion
  → pensieve.run-when-committing (Pipeline 入口)
```

### W2 新项目启动 技能串联

```
Step 0 Pensieve 知识库初始化检测（自动）
  → investigation-first(含 wand) → mass-line → contradiction → spark-prairie-fire
  → protracted-strategy
  → pensieve.run-when-reviewing-code (Pipeline 入口，审查战略文档)
  → verification-before-completion (战略文档写入 docs/strategy/)
  → pensieve.run-when-committing (Pipeline 入口，沉淀战略决策并提交)
```

### W3 品味提升 技能串联

```
Step 0 Pensieve 知识库初始化检测（自动）
  → investigation-first(含 wand)
  → pensieve.run-when-reviewing-code (前置基线 Pipeline 入口)
  → contradiction → brainstorming(含 writing-plans) → executing-plans
  → karpathy-guidelines
  → pensieve.run-when-reviewing-code (回归审查 Pipeline 入口)
  → verification-before-completion
  → criticism-self-criticism
  → pensieve.run-when-committing (Pipeline 入口)
```

### 不适用场景

- 琐碎单步任务（格式化代码、查函数签名、简单问答）→ 不走 Workflow，直接执行

### 自动选择逻辑

按优先级顺序判断（高优先级先判断）：

```
1. 是否从零开始新项目？    → 是 → W2（新项目启动）
2. 代码品味是否需提升？    → 是 → W3（品味提升）
3. 其他所有开发任务        → W1（通用执行）
```

用户可通过 `/task-workflow <编号>` 手动覆盖。

### 跨 Workflow 组合模式

单个 Workflow 完成后，根据情况衔接下一个：

```
W1 完成 → 建议 W3（品味提升，如果审查发现品味问题）
W2 完成 → 建议 W1（逐功能实现）
```

规则：前一个 Workflow 验证通过后才能启动下一个。

## v3.0 重大变化

相比 v2.0 的关键改造：

1. **Pensieve init 保留 Step 0 自动检测**：幂等执行，已存在跳过，用户无需手动触发
2. **Pensieve Pipeline 白盒入口调用**：不再拆解 Pipeline 内部 Task
3. **新增 pensieve-wand 集成**：W1/W2/W3 的 Step 1 内嵌检索
4. **新增 run-when-committing 集成**：W1/W3 末尾用 Pipeline 替代独立 self-improve + 手动提交
5. **W1 修复铁律 2 违反**：新功能必经 brainstorming → executing-plans
6. **W3 修复铁律 3 违反**：新增 verification-before-completion
7. **W3 新增 criticism-self-criticism**：捕获重构隐性问题
8. **W2 新增 mass-line**：补全多源调研环节

## 相关文档

- [执行规范](docs/execution-standards.md) - 编排器职责边界、数据传递规范、质量门标准
- [技能映射表](integrations/skill-mapping.md) - 三大技能库在 workflows 中的使用映射
- [Pensieve 集成规则](integrations/pensieve.md) - Pipeline 入口调用协议、wand 集成、逐 Workflow 调用规则
- [Workflow 1: 通用执行](workflows/1-general-execution.md)
- [Workflow 2: 新项目启动](workflows/2-new-project.md)
- [Workflow 3: 品味提升](workflows/3-taste-improvement.md)
