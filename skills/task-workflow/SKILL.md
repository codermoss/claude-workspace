---
name: task-workflow
description: 任务工作流调度系统 - 根据任务类型路由调度三大技能库，提供 3 个标准工作流组合
version: 2.0.0
---

# Task-Workflow - 任务工作流调度系统

> "政策和策略是党的生命，各级领导同志务必充分注意，万万不可粗心大意。"

## 设计思想

Task-Workflow 学习两大技能库的设计模式：

| 学习对象 | 学习内容 | 体现位置 |
|---------|---------|---------|
| Qiushi (workflows) | 工作流编排：任务顺序、数据传递格式、终止条件 | 3 个 Workflow 定义（Step 序列、输入输出字段、质量门） |
| Pensieve (Pipeline) | 质量门实现：四层体系（铁律 + Step置信度 + Karpathy-Guidelines + 失败回退） | 执行规范、Karpathy-Guidelines 集成 |

**核心职责**：
- **编排层**：定义 Step 顺序、数据传递格式、质量门标准（学习 Qiushi）
- **质量门层**：提供验证逻辑（Karpathy-Guidelines）、失败回退机制（学习 Pensieve）

**与 Qiushi/Pensieve 的区别**：
- Qiushi (workflows)：只定义编排，不包含验证逻辑
- Pensieve (Pipeline)：包含完整 Task Blueprint + 验证逻辑
- Task-Workflow：编排 + 质量门（两者结合）

---

## 编排器边界

| 编排器负责 | 编排器不负责 |
|-----------|-------------|
| 何时触发哪个技能 | 技能内部执行方法 |
| 步骤间数据传递（传递哪些字段） | 字段的格式化模板 |
| 质量门（通过/不通过） | 技能使用原则 |
| 失败回退路径 | |

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

| 编号 | 名称 | 适用场景 | 技能串联 | Pensieve |
|------|------|---------|---------|----------|
| 1 | 通用执行 | 新功能迭代、Bug 修复，程序生命周期核心 | investigation-first → contradiction-analysis → mass-line → concentrate-forces → practice-cognition(TDD/systematic-debugging) → karpathy-guidelines → [Pensieve taste review] → criticism-self-criticism → verification-before-completion → [Pensieve self-improve] | 1 + self-improve |
| 2 | 新项目启动 | 从零开始，目标已知路径未知 | pensieve init → investigation-first → contradiction-analysis → spark-prairie-fire → protracted-strategy → [Pensieve taste review] → verification-before-completion → [Pensieve self-improve] | 1 + self-improve |
| 3 | 品味提升 | 代码可工作但品味不佳 | investigation-first → [Pensieve taste review(前置)] → contradiction-analysis → brainstorming(含writing-plans) → executing-plans → karpathy-guidelines → [Pensieve taste review(回归)] → [Pensieve self-improve] | 2 + self-improve |

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

## 相关文档

- [执行规范](docs/execution-standards.md) - 编排器职责边界、数据传递规范、质量门标准
- [技能映射表](integrations/skill-mapping.md) - 三大技能库在 workflows 中的使用映射
- [Pensieve 集成规则](integrations/pensieve.md) - Pensieve 触发条件和执行方式
- [Workflow 1: 通用执行](workflows/1-general-execution.md)
- [Workflow 2: 新项目启动](workflows/2-new-project.md)
- [Workflow 3: 品味提升](workflows/3-taste-improvement.md)
