---
name: 需求澄清与实施规划
description: 将模糊需求转换为清晰需求文档和技术实施规划
workflow_id: 2
pensieve_triggers: 1 + self-improve
---

# Workflow 2: 需求澄清与实施规划

## 适用场景

- 需求模糊，不知道从哪开始
- 有想法但不清晰，需要结构化梳理
- 多轮交互产出的方案，需要整理成文档和计划

## 不适用

- 需求已明确（有 PRD） → 直接用 Spec Kit
- 单个功能/Bug → Workflow 1
- 代码重构 → Workflow 3

## 数据流图

```
Step 0: Pensieve init + doctor
    ↓ 初始化项目知识库 + 健康检查
Step 1: investigation-first
    ↓ {现状, 目标, 约束, 关键发现}
Step 2: mass-line
    ↓ {多源反馈, 共识问题, 量化指标}
Step 3: contradiction-analysis
    ↓ {核心问题, 主要矛盾, 关键决策点}
Step 4: 场景路由（自动判断）
    ├─ 场景 A → spark-prairie-fire
    │   ↓ {根据地, 发展路线图}
    ├─ 场景 B → protracted-strategy
    │   ↓ {阶段划分, 局部进攻点}
    ├─ 场景 C → overall-planning
    │   ↓ {辩证关系图, 平衡方案}
    └─ 场景 D → 跳过
Step 5: 输出需求文档
    ↓ requirements.md
Step 6: writing-plans
    ↓ plan.md
Step 7: Pensieve self-improve
    ↓ 沉淀可复用结论 → short-term/
```

## 执行步骤

### Step 0: 项目知识库初始化

**技能**: Pensieve — 调用 `init` + `doctor` 工具

**执行**: 
1. 直接调用 Pensieve init
   - Pensieve init 自己判断是否需要初始化
   - Pensieve init 保证幂等性（可重复调用）
   - W2 不关心内部逻辑

2. 执行 doctor 健康检查
   - 确保 .pensieve/ 结构正确
   - 检查 frontmatter、链接、目录结构
   - 验证种子文件完整性

**执行命令**:
```bash
# 初始化
bash "${PENSIEVE_SKILL_ROOT:-$HOME/.claude/skills/pensieve}/.src/scripts/init-project-data.sh"

# 健康检查
bash "${PENSIEVE_SKILL_ROOT:-$HOME/.claude/skills/pensieve}/.src/scripts/run-doctor.sh" --strict
```

**产出结构**:
```
<project>/.pensieve/
  ├── maxims/          # 工程准则（MUST）
  ├── decisions/       # 架构决策（WANT）
  ├── knowledge/       # 事实缓存（IS）
  ├── pipelines/       # 可复用工作流（HOW）
  ├── short-term/      # 待整理条目（7天TTL）
  └── state.md         # 生命周期状态
```

**质量门**: Pensieve init 执行完成，doctor 检查通过（无 MUST_FIX）
**通过 →**: Step 1

---

### Step 1: 调查研究

**技能**: investigation-first

**自动行为**（investigation-first 的 Step 2）:
- 检查 `<project>/.pensieve/` 是否存在（Step 0 已确保初始化）
- 查阅 knowledge/decisions/maxims/pipelines
- 将已知信息纳入调查提纲，作为调查起点
- 标注哪些是"已知"（来自知识库），哪些是"待调查"（需要补充）
- 避免重复探索已缓存内容

**读取输入**:
- 用户的模糊描述
- 已有的方案文档（如果有）
- 相关背景资料（如果有）
- **Pensieve 知识库**（Step 0 已初始化）

**输出字段**:

**现状**：当前情况描述

**目标**：想要达成的目标

**约束条件**：
- 约束1
- 约束2

**关键发现**：
- 发现1（来自知识库）
- 发现2（新调查）

**置信度**：≥ 80%

**质量门**: 现状和目标明确，约束清晰，置信度 ≥ 80%
**不通过 →**: 补充信息
**通过 →**: Step 2

---

### Step 2: 群众路线

**技能**: mass-line

**执行说明**:
在 AI 语境下，"群众"是一切持有真实情况的信息源：
- 用户需求（用户说了什么）
- 代码库现状（实际情况是什么——读代码）
- git history（历史经验是什么）
- 测试用例/运行结果（现实反馈是什么）
- 相关文档/注释（前人智慧是什么）
- **Pensieve 知识库**（已探索的文件位置、模块边界、调用链、架构决策）

**读取输入**: Step 1 的全部输出

**输出字段**:

**多源反馈**：
- [用户需求]：关键事实
- [代码库现状]：关键事实
- [git history]：关键事实
- [测试用例/运行结果]：关键事实
- [相关文档/注释]：关键事实
- [Pensieve 知识库]：关键事实

**共识问题**：各信息源共同指向的问题

**量化指标**：
- 指标1：值
- 指标2：值

**置信度**：≥ 80%

**质量门**: 信息源 ≥ 3 个，共识问题明确，有量化数据，置信度 ≥ 80%
**不通过 →**: 扩大信息源收集
**通过 →**: Step 3

---

### Step 3: 矛盾分析

**技能**: contradiction-analysis

**读取输入**: Step 1-2 的全部输出

**输出字段**:

**矛盾清单**：
- 矛盾1：[A] vs [B]
- 矛盾2：[A] vs [B]

**主要矛盾**：⭐ 主要矛盾描述（理由）

**矛盾性质**：对抗性/非对抗性

**关键决策点**：
- 决策点1：选项 A vs 选项 B
- 决策点2：选项 A vs 选项 B

**置信度**：≥ 80%

**质量门**: 主要矛盾明确，关键决策点清晰，置信度 ≥ 80%
**不通过 →**: 返回 Step 1
**通过 →**: Step 4

---

### Step 4: 场景路由（自动判断）

**执行**: 基于 Step 1-3 的输出，自动判断场景类型并调用对应 skill

**场景判断逻辑**:

```
if Step 1 的"现状" 包含"从零开始"/"无现有系统"/"全新项目"
   AND Step 1 的"约束" 包含"资源有限"/"团队小"/"预算少":
   → 场景 A：从零开始 + 资源极少
   → 调用 spark-prairie-fire

elif Step 1 的"目标" 包含"长期"/"分阶段"/"持续推进"
   OR Step 3 的"主要矛盾" 包含"短期无法完成"/"需要积累":
   → 场景 B：长期任务 + 需分阶段
   → 调用 protracted-strategy

elif Step 3 的"矛盾清单" ≥ 3
   AND Step 3 的"关键决策点" 包含"平衡"/"权衡"/"trade-off":
   → 场景 C：多目标需平衡
   → 调用 overall-planning

else:
   → 场景 D：简单需求
   → 跳过，直接进入 Step 5
```

**场景 A 产出**（spark-prairie-fire）:

**条件评估**：具备/不具备

**根据地**：根据地选择及理由

**发展路线图**：
- 第1步：具体行动 → 成功标志：可验证标志
- 第2步：具体行动 → 成功标志：可验证标志
- 第3步：具体行动 → 成功标志：可验证标志

**流寇主义检查**（内置）：
- [ ] 上一个切入点是否已经验证稳固？
- [ ] 这次扩展是否从上一个根据地自然延伸？
- [ ] 新增的内容是否可复用？

**置信度**：≥ 80%

---

**场景 B 产出**（protracted-strategy）:

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

---

**场景 C 产出**（overall-planning）:

**辩证关系清单**：
1. [A] ↔ [B]：目前偏向 [A/B/均衡]，偏向程度：[轻微/明显/严重]
2. [C] ↔ [D]：目前偏向 [C/D/均衡]，偏向程度：……

**当前阶段的平衡点**：
- [A] vs [B]：当前阶段优先 [A]，但确保 [B] 不低于 [最低限度]
  原因：……

**失衡预警指标**：
- 如果出现 [具体信号]，说明 [A] vs [B] 已经失衡

**置信度**：≥ 80%

---

**场景 D 产出**:
- 无（直接使用 Step 1-3 的输出）

---

**质量门**: 场景判断准确，对应 skill 执行完成，置信度 ≥ 80%
**不通过 →**: 返回对应 Step 调整
**通过 →**: Step 5

---

### Step 5: 输出需求文档

**执行**: 将 Step 1-4 的输出整理为结构化 markdown 文档

**文档结构**:

```markdown
# 需求文档：{项目名称}

## 1. 背景与目标

**现状**：{Step 1 的现状}

**目标**：{Step 1 的目标}

**约束条件**：
- {Step 1 的约束1}
- {Step 1 的约束2}

## 2. 多源信息汇总

{Step 2 的多源反馈}

**共识问题**：{Step 2 的共识问题}

**量化指标**：
- {Step 2 的指标1}
- {Step 2 的指标2}

## 3. 核心问题

{Step 3 的核心问题}

**主要矛盾**：{Step 3 的主要矛盾}

**矛盾性质**：{Step 3 的矛盾性质}

## 4. 关键决策

{Step 3 的关键决策点列表}

## 5. 实施方案

{根据场景类型，插入对应内容}

**场景 A（从零开始）**：
- 根据地：{spark-prairie-fire 的根据地}
- 发展路线：
  - 第1步：{具体行动} → 成功标志：{标志}
  - 第2步：{具体行动} → 成功标志：{标志}
  - 第3步：{具体行动} → 成功标志：{标志}

**场景 B（长期任务）**：
- 当前阶段：{protracted-strategy 的当前阶段}
- 阶段划分：
  - 防御期：{目标} | {方法} | {关键动作}
  - 相持期：{目标} | {方法} | {关键动作}
  - 反攻期：{目标} | {方法} | {关键动作}
- 局部进攻点：{protracted-strategy 的局部进攻点}

**场景 C（多目标平衡）**：
- 辩证关系图：{overall-planning 的辩证关系对列表}
- 平衡方案：{overall-planning 的平衡点}
- 失衡预警：{overall-planning 的失衡预警指标}

**场景 D（简单需求）**：
- 基于 Step 3 的分析，直接描述解决方案

## 6. 后续行动

**建议执行方式**：
- 如果是场景 A/B → 使用 Workflow 1 逐任务执行
- 如果是场景 C → 使用 Workflow 1 + overall-planning 持续平衡
- 如果是新功能开发 → 使用 Spec Kit（`/speckit-specify` 基于本文档）

**首个任务**：
- 场景 A：{发展路线图第1步}
- 场景 B：{局部进攻点}
- 场景 C：{平衡方案的第一步}
- 场景 D：{从 Step 3 提取的首要行动}
```

**输出位置**: `./requirements.md` 或 `./{项目名}-requirements.md`

**质量门**: 文档结构完整，内容清晰，后续行动明确
**通过 →**: Step 6

---

### Step 6: 生成实施计划

**技能**: writing-plans

**读取输入**: Step 5 的 requirements.md

**执行说明**:
- writing-plans 基于 requirements.md 生成技术实施计划
- 包含：文件结构、任务分解、测试策略、提交步骤
- 产出格式：bite-sized tasks（每步 2-5 分钟）

**输出字段**:

**计划文件路径**：`docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md`

**计划内容**:
```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---

## File Structure

**Files to create:**
- `path/to/file1.py` - Purpose
- `path/to/file2.py` - Purpose

**Files to modify:**
- `path/to/existing.py:123-145` - What changes

---

### Task 1: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

- [ ] **Step 1: Write the failing test**
- [ ] **Step 2: Run test to verify it fails**
- [ ] **Step 3: Write minimal implementation**
- [ ] **Step 4: Run test to verify it passes**
- [ ] **Step 5: Commit**

### Task 2: ...
```

**质量门**: plan.md 生成完成，包含完整的任务分解和测试策略
**通过 →**: Step 7

---

### Step 7: Pensieve 知识沉淀

**技能**: Pensieve — 调用 `self-improve` 工具

**读取输入**:
- Step 1 的 `现状`、`目标`、`约束`、`关键发现`
- Step 2 的 `多源反馈`、`共识问题`、`量化指标`
- Step 3 的 `核心问题`、`主要矛盾`、`关键决策点`
- Step 4 的战略分析结果：
  - 场景 A：`根据地选择理由`、`发展路线图`
  - 场景 B：`阶段划分策略`、`态势评估`、`局部进攻点`
  - 场景 C：`平衡方案`、`辩证关系图`、`失衡预警指标`
- Step 5 的 `requirements.md`
- Step 6 的 `plan.md`

**执行内容**:

1. **将架构决策沉淀为 `decisions/`**：
   - 场景 A：根据地选择理由（为什么选这个切入点）
   - 场景 B：阶段划分策略（为什么这样分阶段）
   - 场景 C：平衡方案（为什么这样平衡多个目标）
   
2. **将技术选型沉淀为 `decisions/`**：
   - 从 plan.md 提取技术栈选择理由
   - 从 requirements.md 提取关键决策点的选择结果
   
3. **将项目上下文沉淀为 `knowledge/`**：
   - 主要矛盾分析（核心问题是什么）
   - 多源信息汇总（各信息源的关键事实）
   - 目录结构、模块边界（从 plan.md 提取）
   
4. **若发现跨项目适用的原则，沉淀为 `maxims/`**：
   - 从 Step 4 的战略分析中提取通用原则
   - 例如：流寇主义检查的教训、阶段划分的原则、平衡方案的模式
   
5. **所有新建条目写入 `short-term/`**：
   - 7天后提醒 promote 或删除
   - 命名规范：
     - `short-term/decisions/{date}-{conclusion}.md`
     - `short-term/knowledge/{name}/content.md`
     - `short-term/maxims/{one-sentence-conclusion}.md`

**执行命令**:
```bash
bash "${PENSIEVE_SKILL_ROOT:-$HOME/.claude/skills/pensieve}/.src/scripts/maintain-project-state.sh" --event self-improve --note "W2 需求澄清完成"
```

**质量门**: 至少 1 条架构决策或技术选型被写入 short-term/，或无新增结论时明确说明
**通过 →**: 完成

---

## 注意事项

1. **Pensieve init 是 Step 0**：确保后续 step 都能利用知识库
2. **Pensieve 自己控制触发条件**：W2 直接调用，Pensieve 判断是否需要初始化
3. **doctor 在 Step 0 执行**：确保知识库结构正确
4. **investigation-first 自动查阅知识库**：Step 0 已初始化，Step 1 直接利用
5. **mass-line 是必选步骤**：W2 的核心能力就是"多源信息整合"
6. **场景路由自动判断**：基于 Step 1-3 的输出，无需用户选择
7. **Step 6 调用 writing-plans**：产出技术实施计划
8. **Pensieve self-improve 是 Step 7**：沉淀可复用结论到 short-term/
9. **不调用 brainstorming**：Step 4 已完成方案探索，不需要重复
10. **不调用 concentrate-forces**：W2 不执行任务，只澄清需求
11. **不调用 practice-cognition 和 criticism-self-criticism**：W2 不执行代码，不复盘
12. **时间预估**：60-120 分钟（取决于场景复杂度）

## 产出物

W2 完成后产出：

1. **.pensieve/** — 项目知识库（Step 0）
   - maxims/ — 工程准则
   - decisions/ — 架构决策
   - knowledge/ — 事实缓存
   - pipelines/ — 可复用工作流
   - short-term/ — 待整理条目（Step 7 新增）

2. **requirements.md** — 需求文档（Step 5）
   - 背景与目标
   - 多源信息汇总
   - 核心问题与主要矛盾
   - 实施方案（根据场景类型）
   - 后续行动建议

3. **plan.md** — 实施计划（Step 6）
   - 文件结构
   - 任务分解（bite-sized tasks）
   - 测试策略
   - 提交步骤

## 后续工作

拿着 `plan.md`，直接执行：

```
/executing-plans（基于 plan.md）
  → 逐任务执行
  → 完成实施
```

或者使用 Workflow 1 逐任务执行。

## 相关技能

- [investigation-first](../../investigation-first/SKILL.md)
- [mass-line](../../mass-line/SKILL.md)
- [contradiction-analysis](../../contradiction-analysis/SKILL.md)
- [spark-prairie-fire](../../spark-prairie-fire/SKILL.md)
- [protracted-strategy](../../protracted-strategy/SKILL.md)
- [overall-planning](../../overall-planning/SKILL.md)
- [writing-plans](../../writing-plans/SKILL.md)
- [Pensieve](../../Pensieve/SKILL.md)
