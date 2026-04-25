---
name: 新项目启动
description: 从零开始，目标已知路径未知
workflow_id: 2
pensieve_triggers: 1 + self-improve
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

## 数据流图

```
Step 0: pensieve init
    ↓
Step 1: investigation-first
    ↓ {现状, 关键约束, 关键发现, 置信度}
Step 2: contradiction-analysis
    ↓ {主要矛盾, 解决策略, 置信度}
Step 3: spark-prairie-fire
    ↓ {根据地, 发展路线图, 置信度}
Step 4: protracted-strategy
    ↓ {当前阶段, 核心任务, 完整战略, 置信度}
Step 5: Pensieve taste review
    ↓ {品味评分, 致命问题, 改进方向, 置信度}
Step 6: verification-before-completion
    ↓ {verified, 最终方案, 置信度}
    ↓ 输出 markdown 内容（项目启动方案）
Step 7: Pensieve self-improve
    ↓ 沉淀架构决策和技术选型 → short-term/
```

## 执行步骤

### Step 0: 项目知识库初始化

**技能**: Pensieve — 调用 `init` 工具

**执行**: 检测 `<project>/.pensieve/` 是否存在：
- 不存在 → 自动执行 `pensieve init`，播种默认 maxims/decisions/knowledge/pipelines
- 已存在 → 跳过

**质量门**: `.pensieve/` 目录存在且包含 `maxims/`、`decisions/`、`knowledge/`、`pipelines/` 四个子目录
**通过 →**: Step 1

---

### Step 1: 调查研究

**技能**: investigation-first
**读取输入**:
- `task.project_description`
- `task.constraints`（可选）
- `task.existing_assets`（可选）

**输出字段**:

**现状**：项目当前状态描述

**关键约束**：
- 约束1
- 约束2

**关键发现**：
- 发现1
- 发现2

**判断**：初步判断

**置信度**：85%

**质量门**:
- 现状和约束字段非空
- 关键发现 ≥ 2 项
- 置信度 ≥ 80%

**不通过 →**: 要求用户补充项目描述
**通过 →**: Step 2

---

### Step 2: 矛盾分析

**技能**: contradiction-analysis
**读取输入**: Step 1 的 `现状`、`关键约束`、`关键发现`

**输出字段**:

**矛盾清单**：
- 矛盾1
- 矛盾2

**主要矛盾**：主要矛盾描述

**矛盾性质**：对抗性/非对抗性

**解决方法**：解决方法描述

**置信度**：85%

**质量门**:
- 矛盾清单 ≥ 2 项
- 主要矛盾已标注
- 置信度 ≥ 80%

**不通过 →**: 返回 Step 1 补充调查
**通过 →**: Step 3

---

### Step 3: 星火燎原

**技能**: spark-prairie-fire
**读取输入**: Step 2 的 `主要矛盾`、`解决方法`

**执行说明**:
本 Step 内置流寇主义检查（3问），无需外部质量检查：
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

**置信度**：85%

**质量门**:
- 根据地具体可验证
- 发展路线图 ≥ 3 步
- 流寇主义检查 3 问全部通过
- 置信度 ≥ 80%

**条件评估为"不具备" →**: 返回 Step 1，调查如何创造条件
**通过 →**: Step 4

---

### Step 4: 持久战略

**技能**: protracted-strategy
**读取输入**: Step 3 的 `根据地`、`发展路线图`

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

**置信度**：85%

**质量门**:
- 阶段划分 = 3 个
- 局部进攻点可立即执行
- 阶段检查点可验证
- 置信度 ≥ 80%

**不通过 →**: 返回 Step 3 调整根据地
**通过 →**: Step 5

---

### Step 5: Pensieve 品味审查

**调用协议**: 按 [Taste Review 调用协议](../integrations/pensieve.md#taste-review-调用协议) 执行完整 6 Task Pipeline

**审查对象**: Step 3+4 产出的战略文档（根据地分析、发展路线图、战略规划）

**目的**: 审查设计品味——抽象层次是否恰当、模块边界是否清晰、接口是否简洁、阶段划分是否合理。品味信号（嵌套深度、模块边界、接口品味、抽象层次）对设计文档同样适用。

**输出字段**:

**品味评分**：好品味/凑合/垃圾

**致命问题**（CRITICAL 级别）：
- 致命问题1（附证据、定位）
- （如有）

**警告问题**（WARNING 级别）：
- （如有）

**改进方向**：
- 改进方向1
- （如有）

**置信度**：≥ 80%

**质量门**: 审查完成（6 Task 全部执行），置信度 ≥ 80%
**不通过（taste_score = "垃圾"）→**: 返回 Step 3 调整根据地/路线图
**通过 →**: Step 6

---

### Step 6: 验证

**技能**: verification-before-completion
**读取输入**: Step 3 的 `根据地`、`发展路线图`、Step 4 的 `当前阶段`、`核心任务`、`完整战略` 和 Step 5 的 `品味评分`、`致命问题`

**输出字段**:

**方案完整性**：完整/不完整

**可行性**：可行/存疑

**证据充分性**：充分/不足

**证据**：
- 证据1
- 证据2
- 证据3

**置信度**：95%

**markdown 输出**（项目启动方案）: 现状与约束、主要矛盾、根据地方案、发展路线图、完整战略规划、品味评分、置信度

**质量门**:
- 方案完整性 = "完整"
- 可行性 = "可行"
- 置信度 ≥ 90%
- markdown 内容已输出

**不通过 →**: 返回对应 Step 补充
**通过 →**: Step 7

---

### Step 7: Pensieve self-improve（知识沉淀）

**技能**: Pensieve — 调用 `self-improve` 工具

**读取输入**:
- Step 3 的 `根据地`、`发展路线图`
- Step 4 的 `态势评估`、`完整战略`
- Step 5 的 `品味评分`、`致命问题`
- Step 6 的 `验证结果`

**执行内容**:
1. 将根据地选择理由和技术选型沉淀为 `decisions/`
2. 将架构方向选择沉淀为 `decisions/`
3. 若发现跨项目适用的启动原则，沉淀为 `maxims/`
4. 将项目上下文（目录结构、模块边界）沉淀为 `knowledge/`
5. 所有新建条目写入 `short-term/`，7天后提醒 promote 或删除

**质量门**: 至少 1 条架构决策或技术选型被写入 short-term/，或无新增结论时明确说明

---

## 注意事项

1. **Pensieve init**: Step 0 自动执行，项目知识库与项目同生，不询问
2. **Pensieve taste review**: 1 次（Step 5），审查战略文档品味（抽象层次、模块边界、接口简洁性、阶段划分合理性），完整 6 Task Pipeline，不做裁剪
3. **Pensieve self-improve**: 1 次（verification 通过后自动执行）
4. **karpathy-guidelines**: 不触发。W2 全流程无代码产出
5. **战略文档质量**: 由 spark-prairie-fire 内置流寇检查 + protracted-strategy 内置阶段评估 + Pensieve taste review 品味审查 + verification-before-completion 验证四重保障
6. **时间预估**: 中（取决于项目复杂度）

## 后续工作

W2 完成后建议：
1. **Workflow 1** 逐功能实现发展路线图中的第1步
2. **protracted-strategy** 到达阶段检查点时重新评估阶段

## 相关技能

- [investigation-first](../../investigation-first/SKILL.md)
- [contradiction-analysis](../../contradiction-analysis/SKILL.md)
- [spark-prairie-fire](../../spark-prairie-fire/SKILL.md)
- [protracted-strategy](../../protracted-strategy/SKILL.md)
- [verification-before-completion](../../verification-before-completion/SKILL.md)
- [Pensieve](../../pensieve/SKILL.md)
