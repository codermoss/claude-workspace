---
name: 新项目启动
description: 从零开始，目标已知路径未知
workflow_id: 2
pensieve_integration: pipeline-entry-call
---

# Workflow 2: 新项目启动

## 适用场景

- 从零开始的新项目
- 目标已知但路径未知
- 需要建立根据地并制定战略
- 资源有限，需要分阶段推进

## 不适用

- 已有项目添加新功能 → Workflow 1
- 已有项目重构 → Workflow 3
- 生产故障 → 直接修复，不走 Workflow

## 前置说明

W2 的 Step 0 自动检测并初始化项目知识库。`pensieve init` 是幂等操作（已存在不覆盖），无副作用，无需用户确认。新项目场景下 init 几乎必然触发。

## 设计原则

1. 战略层 Workflow，全流程产出战略文档而非代码
2. 多源调研先于矛盾分析（mass-line 在 contradiction 之前）
3. Pensieve 调用：仅用 run-when-reviewing-code 审查战略文档（无代码故不调用 run-when-committing）

## 数据流图

```
Step 0   Pensieve 知识库初始化检测（自动）
    ↓
Step 1   investigation-first（含 pensieve-wand 检索）
    ↓ {现状, 关键约束, 已知陷阱, 关键发现, 置信度}
Step 2   mass-line
    ↓ {多源反馈, 共识需求, 信息缺口, 置信度}
Step 3   contradiction-analysis
    ↓ {主要矛盾, 解决策略, 置信度}
Step 4   spark-prairie-fire
    ↓ {根据地, 发展路线图, 置信度}
Step 5   protracted-strategy
    ↓ {当前阶段, 核心任务, 完整战略, 置信度}
Step 6   pensieve.run-when-reviewing-code（Pipeline 入口调用，审查战略文档）
    ↓ {品味评分, 致命问题, 改进方向, Task 6 自动沉淀架构决策}
Step 7   verification-before-completion
    ↓ {方案完整性, 可行性, 证据, 置信度}
    ↓ 战略文档写入 docs/strategy/YYYY-MM-DD-<project>.md
Step 8   pensieve.run-when-committing（Pipeline 入口调用，知识库总结与提交）
    ↓ Task 1 沉淀判断 + Task 2 沉淀战略决策 + Task 3 提交战略文档
```

## 执行步骤

### Step 0: Pensieve 知识库初始化检测

**技能**: Pensieve — 调用 `init` 工具（幂等操作）

**执行**: 自动检测 `<project>/.pensieve/` 是否存在：
- 不存在 → 静默执行 `pensieve init`（幂等，不询问）
  - W2 是新项目启动场景，几乎必然触发首次 init
  - 创建 `maxims/`、`decisions/`、`knowledge/`、`pipelines/` 四个子目录
  - 播种默认模板 + 默认 pipeline + taste-review knowledge
  - 执行基线探索（即使新项目空仓库，也会产出初始化报告）
- 已存在 → 跳过

**质量门**: `.pensieve/` 目录存在且包含四个子目录
**通过 →**: Step 1

---

### Step 1: 调查研究（含 pensieve-wand 检索）

**技能**: investigation-first
**Pensieve 集成**: 派遣 pensieve-wand agent 检索项目知识库

**读取输入**:
- `task.project_description`
- `task.constraints`（可选）
- `task.existing_assets`（可选）

**执行流程**:
1. 派遣 pensieve-wand agent，传入项目描述关键词
   - 即使新项目，wand 也可检索到历史项目的架构决策、技术选型经验
2. 接收 wand 输出（已知信息 / 已知陷阱 / 建议路径 / 待探索）
3. 基于 wand 结果做补充调查（避免重复探索）

**输出字段**:

**现状**：项目当前状态描述

**关键约束**：
- 约束1
- 约束2

**已知陷阱**（来自 pensieve-wand）：
- 陷阱1（来源：路径）

**关键发现**：
- 发现1
- 发现2

**判断**：初步判断

**置信度**：≥ 80%

**质量门**:
- 现状和约束字段非空
- 关键发现 ≥ 2 项
- pensieve-wand 已检索（命中或明确未命中）
- 置信度 ≥ 80%

**不通过 →**: 要求用户补充项目描述
**通过 →**: Step 2

---

### Step 2: 群众路线（多源调研）

**技能**: mass-line

**读取输入**: Step 1 的 `现状`、`关键约束`、`关键发现`

**信息源清单**（新项目语境）:
- 用户初步想法（用户说了什么）
- 同类项目经验（git history、开源项目、文档）
- 技术生态现状（库/框架的成熟度、社区活跃度）
- 利益相关方需求（用户、团队、运维、合规）
- 已有可依托资产（任何现存代码、文档、基础设施）

**输出字段**:

**多源反馈**：
- [信息源1]：关键事实
- [信息源2]：关键事实
- [信息源3]：关键事实

**共识需求**：各信息源共同指向的核心需求

**信息缺口**：
- 缺口1（建议补充方式）
- 缺口2

**置信度**：≥ 80%

**质量门**: 信息源 ≥ 3 个，共识需求明确，置信度 ≥ 80%
**不通过 →**: 扩大信息源收集
**通过 →**: Step 3

---

### Step 3: 矛盾分析

**技能**: contradiction-analysis

**读取输入**: Step 1 的 `现状`、`关键约束`、`关键发现` + Step 2 的 `共识需求`

**输出字段**:

**矛盾清单**：
- 矛盾1
- 矛盾2

**主要矛盾**：主要矛盾描述

**矛盾性质**：对抗性/非对抗性

**解决方法**：解决方法描述

**置信度**：≥ 80%

**质量门**:
- 矛盾清单 ≥ 2 项
- 主要矛盾已标注
- 置信度 ≥ 80%

**不通过 →**: 返回 Step 1 补充调查
**通过 →**: Step 4

---

### Step 4: 星火燎原

**技能**: spark-prairie-fire
**读取输入**: Step 3 的 `主要矛盾`、`解决方法`

**执行说明**:
本 Step 内置流寇主义检查（3 问），无需外部质量检查：
1. 上一个切入点是否已经验证稳固？
2. 这次扩展是否从上一个根据地自然延伸？
3. 新增的内容是否可复用？

**输出字段**:

**条件评估**：具备/不具备

**根据地**：根据地选择及理由

**发展路线图**：
- 第1步：具体行动 → 成功标志：可验证标志
- 第2步：具体行动 → 成功标志：可验证标志
- 第3步：具体行动 → 成功标志：可验证标志

**置信度**：≥ 80%

**质量门**:
- 根据地具体可验证
- 发展路线图 ≥ 3 步
- 流寇主义检查 3 问全部通过
- 置信度 ≥ 80%

**条件评估为"不具备" →**: 返回 Step 1，调查如何创造条件
**通过 →**: Step 5

---

### Step 5: 持久战略

**技能**: protracted-strategy
**读取输入**: Step 4 的 `根据地`、`发展路线图`

**输出字段**:

**态势评估**：
- 我方优势：优势描述
- 我方劣势：劣势描述
- 关键障碍：障碍描述

**阶段划分**（3 个阶段）：
- 战略防御期：目标 | 方法 | 关键动作
- 战略相持期：目标 | 方法 | 关键动作
- 战略反攻期：目标 | 方法 | 关键动作

**局部进攻点**：可立即执行的切入点

**阶段检查点**：
- 检查点1
- 检查点2

**当前阶段**：战略防御期

**核心任务**：核心任务描述

**完整战略**：完整战略规划

**置信度**：≥ 80%

**质量门**:
- 阶段划分 = 3 个
- 局部进攻点可立即执行
- 阶段检查点可验证
- 置信度 ≥ 80%

**不通过 →**: 返回 Step 4 调整根据地
**通过 →**: Step 6

---

### Step 6: Pensieve 战略审查（Pipeline 入口调用）

**调用方式**: 调用 `pensieve.run-when-reviewing-code` Pipeline 入口（白盒调用）

**调用约定**: task-workflow 不读取 Pipeline 内部 Task 定义。Pipeline 内部 6 Task 由 Pensieve 自治执行。

**审查对象**: Step 4+5 产出的战略文档（根据地分析、发展路线图、阶段战略）

**审查适配说明**: Pipeline 内 Knowledge 模板基于代码维度，但其品味信号（嵌套深度 / 模块边界 / 接口简洁 / 抽象层次）对战略文档同样适用——
- 嵌套深度 → 阶段嵌套
- 模块边界 → 阶段边界
- 接口简洁 → 阶段切换条件清晰
- 抽象层次 → 战略文档抽象是否恰当

**Pipeline 内置能力**（task-workflow 视为黑盒）:
- 基线探索 / 上下文准备 / 逐文件审查 / 候选验证 / 报告生成
- Task 6 自动沉淀架构决策为 decision、技术选型为 knowledge

**接收输出**:

**品味评分**：好品味/凑合/垃圾

**致命问题**（CRITICAL 级别）：
- 致命问题1（附证据、定位）

**警告问题**（WARNING 级别）：
- 警告1（如有）

**改进方向**：
- 改进方向1

**沉淀状态**：Pipeline Task 6 是否产出沉淀（路径或"无新增沉淀"）

**置信度**：≥ 80%

**质量门**: Pipeline 执行完成，置信度 ≥ 80%
**不通过（taste_score = "垃圾"）→**: 返回 Step 4 调整根据地/路线图
**通过 →**: Step 7

---

### Step 7: 验证

**技能**: verification-before-completion
**读取输入**: Step 4 的 `根据地`、`发展路线图`、Step 5 的 `当前阶段`、`核心任务`、`完整战略` 和 Step 6 的 `品味评分`、`致命问题`

**输出字段**:

**方案完整性**：完整/不完整

**可行性**：可行/存疑

**证据充分性**：充分/不足

**证据**：
- 证据1
- 证据2
- 证据3

**置信度**：≥ 90%

**战略文档输出**: 写入 `docs/strategy/YYYY-MM-DD-<project>.md`，内容包含：
- 现状与约束
- 主要矛盾
- 根据地方案
- 发展路线图
- 完整战略规划
- 品味评分
- 置信度

**质量门**:
- 方案完整性 = "完整"
- 可行性 = "可行"
- 置信度 ≥ 90%
- 战略文档已写入 `docs/strategy/`

**不通过 →**: 返回对应 Step 补充
**通过 →**: Step 8

---

### Step 8: Pensieve 知识库总结与提交（Pipeline 入口调用）

**调用方式**: 调用 `pensieve.run-when-committing` Pipeline 入口（白盒调用）

**调用约定**: task-workflow 不读取 Pipeline 内部 Task 定义。Pipeline 内部 3 Task 由 Pensieve 自治执行。

**目的**: 让 W2 完成项目知识库的螺旋式增长——战略决策入库，下一轮 W1 实施时 pensieve-wand 可检索复用。

**Pipeline 内置能力**（task-workflow 视为黑盒）:
- Task 1: 判断本次启动是否有可沉淀洞察（W2 几乎必然命中"架构决策"和"模式发现"信号）
- Task 2: 提取战略洞察 + 分类 + 写入 short-term/
  - 根据地选择理由 → decisions/
  - 技术选型与架构方向 → decisions/
  - 项目上下文（架构边界、目录结构）→ knowledge/
  - 跨项目启动原则 → maxims/
- Task 3: 原子化提交 Step 7 写入的 `docs/strategy/YYYY-MM-DD-<project>.md`

**接收输出**:

**沉淀状态**：N 条战略洞察已写入 short-term/，路径列表

**提交状态**：M 个原子 commit 已完成，commit hash 列表

**短期记忆提示**：若 short-term/ 有到期条目，提示运行 `/pensieve refine`

**质量门**: Pipeline 执行完成，git log 可见战略文档提交，或 Pipeline 失败回退已记录
**不通过 →**: 按 Pipeline 失败回退规则处理（参考 pipeline.run-when-committing.md 第 124-128 行）

---

## 注意事项

1. **Step 0 Pensieve init 自动检测**: 幂等操作，已存在则跳过；不存在则静默初始化（W2 新项目几乎必然触发）
2. **pensieve-wand 内嵌于 Step 1**: 检索历史项目经验，避免重复探索
3. **mass-line 新增**: 修复原 W2 缺失的多源调研环节
4. **Pensieve Pipeline 双次入口调用**: Step 6 reviewing-code（审查战略文档）+ Step 8 committing（沉淀战略决策并提交）
5. **战略文档入 git**: Step 7 写入 `docs/strategy/YYYY-MM-DD-<project>.md`，Step 8 由 Pipeline Task 3 提交
6. **三 W 沉淀对齐**: W2 末尾增加 Step 8 后，W1/W2/W3 三个工作流均以 `run-when-committing` 收尾，知识库飞轮在三个 W 中同步增长
7. **karpathy-guidelines 不触发**: 全流程无代码产出
8. **战略文档质量**: 由 spark-prairie-fire 内置流寇检查 + protracted-strategy 内置阶段评估 + Pensieve taste review 品味审查 + verification-before-completion 验证四重保障
9. **时间预估**: 中（取决于项目复杂度）

## 后续工作

W2 完成后建议：
1. **Workflow 1** 逐功能实现发展路线图中的第1步
2. **protracted-strategy** 到达阶段检查点时重新评估阶段

## 相关技能

- [investigation-first](../../investigation-first/SKILL.md)
- [pensieve-wand](../../pensieve/.src/templates/agents/pensieve-wand.md)（agent，Step 1 内嵌）
- [mass-line](../../mass-line/SKILL.md)
- [contradiction-analysis](../../contradiction-analysis/SKILL.md)
- [spark-prairie-fire](../../spark-prairie-fire/SKILL.md)
- [protracted-strategy](../../protracted-strategy/SKILL.md)
- [verification-before-completion](../../verification-before-completion/SKILL.md)
- [Pensieve run-when-reviewing-code](../../pensieve/.src/templates/pipeline.run-when-reviewing-code.md)（Step 6 入口调用）
- [Pensieve run-when-committing](../../pensieve/.src/templates/pipeline.run-when-committing.md)（Step 8 入口调用）
