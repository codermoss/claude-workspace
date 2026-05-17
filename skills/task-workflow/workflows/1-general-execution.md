---
name: 通用执行
description: 新功能迭代与Bug修复，程序生命周期核心工作流
workflow_id: 1
pensieve_integration: pipeline-entry-call
---

# Workflow 1: 通用执行

## 适用场景

- 新功能开发与迭代
- Bug 修复与根因分析
- 程序生命周期两大核心问题

## 不适用

- 从零开始新项目 → Workflow 2
- 代码品味提升 → Workflow 3
- 琐碎单步任务（格式化、查函数签名、简单问答）→ 不走 Workflow，直接执行

## 前置说明

W1 的 Step 0 自动检测并初始化项目知识库。`pensieve init` 是幂等操作（已存在不覆盖），无副作用，无需用户确认。

## 设计原则

1. 思想方法论调度（Qiushi）+ 工程执行调度（Superpower）+ 项目知识库调度（Pensieve）三者协同
2. Pensieve 调用方式：Pipeline 入口白盒调用，内部 Task 黑盒自治
3. 新功能与 Bug 修复分支化执行：新功能必须经过设计审批与计划编写（铁律 2），Bug 修复走系统化调试

## 数据流图

```
Step 0  Pensieve 知识库初始化检测（自动）
    ↓
Step 1  investigation-first（含 pensieve-wand 检索）
    ↓ {问题/需求, 约束, 边界条件, 已知陷阱, 关键发现, 置信度}
Step 2  contradiction-analysis
    ↓ {主要矛盾, 矛盾性质, 假说清单, 置信度}
Step 3  mass-line
    ↓ {多源反馈, 共识问题, 量化指标, 置信度}
Step 4  concentrate-forces
    ↓ {主攻目标, 暂缓清单, 完成信号, 置信度}
Step 5  执行分支
    ├─ 新功能 → brainstorming → executing-plans (内含 TDD)
    │   ↓ {设计文档, 计划文件, 测试用例, 实现代码, 测试结果, 置信度}
    └─ Bug 修复 → systematic-debugging
        ↓ {根因, 修复代码, 验证结果, 置信度}
Step 6  karpathy-guidelines
    ↓ {规范检查结果, 违规项, 置信度}
Step 7  pensieve.run-when-reviewing-code（Pipeline 入口调用）
    ↓ {品味评分, 致命问题, 改进方向, Task 6 自动沉淀}
Step 8  criticism-self-criticism
    ↓ {效果评估, 经验教训, 改进措施, 置信度}
Step 9  verification-before-completion
    ↓ {验证结果, 证据, 副作用, 置信度}
    ↓ 输出 markdown 任务完成报告
Step 10 pensieve.run-when-committing（Pipeline 入口调用）
    ↓ Task 1 沉淀判断 + Task 2 自动沉淀 + Task 3 原子提交
```

## 执行步骤

### Step 0: Pensieve 知识库初始化检测

**技能**: Pensieve — 调用 `init` 工具（幂等操作）

**执行**: 自动检测 `<project>/.pensieve/` 是否存在：
- 不存在 → 静默执行 `pensieve init`（幂等，不询问）
  - 创建 `maxims/`、`decisions/`、`knowledge/`、`pipelines/` 四个子目录
  - 播种默认模板 + 默认 pipeline + taste-review knowledge
  - 执行基线探索（git log + 代码热点），产出可沉淀候选清单（不自动写入）
- 已存在 → 跳过（已存在不破坏，详见 init.md 行 1 幂等保证）

**质量门**: `.pensieve/` 目录存在且包含 `maxims/`、`decisions/`、`knowledge/`、`pipelines/` 四个子目录
**通过 →**: Step 1

---

### Step 1: 调查研究（含 pensieve-wand 检索）

**技能**: investigation-first
**Pensieve 集成**: 派遣 pensieve-wand agent 检索项目知识库

**读取输入**:
- `task.description`（问题描述或功能需求）
- `task.related_files`（可选）
- `task.constraints`（可选）
- `task.recent_changes`（可选，`git log --oneline -10`）

**执行流程**:
1. 派遣 pensieve-wand agent，传入任务关键词
   - System 1：MEMORY.md 关键词命中则零工具调用直答
   - System 2：未命中则启动图谱探索（预算 ≤ 10 工具调用）
2. 接收 pensieve-wand 返回的「已知信息 / 已知陷阱 / 建议路径 / 待探索」
3. 基于 wand 结果做补充调查（避免重复探索已缓存知识）
4. 整合输出最终调查结论

**输出字段**:

**问题/需求**：问题或需求的明确描述

**约束条件**：
- 约束1
- 约束2

**边界条件**：
- 边界1
- 边界2
- 边界3

**已知陷阱**（来自 pensieve-wand）：
- 陷阱1（来源：knowledge/decision/maxim 路径）

**关键发现**：
- 发现1
- 发现2

**置信度**：≥ 80%

**质量门**: 问题/需求明确，约束清晰，边界条件 ≥ 3 项，pensieve-wand 已检索（命中或明确未命中），置信度 ≥ 80%
**不通过 →**: 补充信息，重新调查
**通过 →**: Step 2

---

### Step 2: 矛盾分析

**技能**: contradiction-analysis

**读取输入**: Step 1 的 `问题/需求`、`约束条件`、`已知陷阱`、`关键发现`

**输出字段**:

**矛盾清单**：
- 矛盾1
- 矛盾2

**主要矛盾**：主要矛盾描述

**矛盾性质**：对抗性/非对抗性

**假说清单**：
- 假说1（优先级：high）— 验证方式：验证方式
- 假说2（优先级：medium）— 验证方式：验证方式

**置信度**：≥ 80%

**质量门**: 主要矛盾明确，假说可验证，验证方式具体 ≥ 3 步，置信度 ≥ 80%
**不通过 →**: 返回 Step 1 补充调查
**通过 →**: Step 3

---

### Step 3: 群众路线

**技能**: mass-line

**读取输入**: Step 1 的 `问题/需求`、`约束条件` 和 Step 2 的 `主要矛盾`、`假说清单`

**信息源清单**（AI 语境下的"群众"）:
- 用户明确需求（用户说了什么）
- 代码库现状（实际情况是什么——读代码）
- git history（历史经验是什么）
- 测试用例/运行结果（现实反馈是什么）
- 相关文档/注释（前人智慧是什么）

**输出字段**:

**多源反馈**：
- [信息源1]：关键事实
- [信息源2]：关键事实
- [信息源3]：关键事实

**共识问题**：各信息源共同指向的问题

**量化指标**：
- 指标1：值
- 指标2：值

**置信度**：≥ 80%

**质量门**: 信息源 ≥ 3 个，共识问题明确，有量化数据，置信度 ≥ 80%
**不通过 →**: 扩大信息源收集
**通过 →**: Step 4

---

### Step 4: 集中兵力

**技能**: concentrate-forces

**读取输入**: Step 2 的 `假说清单` 和 Step 3 的 `共识问题`、`量化指标`

**输出字段**:

**主攻目标**：选定的首要任务

**任务类型**：新功能 / Bug 修复（决定 Step 5 分支）

**选择理由**：
- 影响范围：高/中/低
- 解决难度：高/中/低
- 依赖关系：是否被其他任务依赖

**暂缓清单**：
- 暂缓任务1（理由）
- 暂缓任务2（理由）

**完成信号**：什么情况下认为主攻目标完成

**置信度**：≥ 80%

**质量门**: 主攻目标明确，任务类型已分类，暂缓清单完整，完成信号可验证，置信度 ≥ 80%
**不通过 →**: 返回 Step 2 重新分析优先级
**通过 →**: Step 5（按任务类型分支）

---

### Step 5: 执行分支

**根据 Step 4 的 `任务类型` 选择分支，二选一执行。**

#### 分支 A: 新功能（task_type = "新功能"）

**技能**: brainstorming → executing-plans（brainstorming 内置 writing-plans + TDD 调用）

**读取输入**: Step 4 的 `主攻目标`、`完成信号`

**执行流程**:
1. **brainstorming**：探索方案 → 提出 2-3 种设计 → 用户审批
2. **brainstorming 内部调用 writing-plans**：编写 bite-sized 实现计划（含 TDD 测试用例）
3. **executing-plans**：按计划逐 Task 执行（红→绿→重构循环 + 频繁提交）

**输出字段**:

**设计文档**：docs/superpowers/specs/YYYY-MM-DD-*-design.md 路径

**计划文件**：docs/superpowers/plans/YYYY-MM-DD-*.md 路径

**测试用例**：
- 正常场景：描述 — 结果：通过
- 边界场景：描述 — 结果：通过
- 异常场景：描述 — 结果：通过

**实现代码**：代码位置和变更摘要

**测试结果**：0 failures

**置信度**：≥ 80%

**质量门**:
- 用户已审批设计（brainstorming HARD-GATE）
- 计划文件无占位符（writing-plans Self-Review 通过）
- 覆盖正常/边界/异常 3 种场景
- 测试结果 0 failures
- 置信度 ≥ 80%

**不通过 →**: 返回对应技能（brainstorming 重新设计 / writing-plans 补全计划 / executing-plans 修正实现）
**通过 →**: Step 6

#### 分支 B: Bug 修复（task_type = "Bug 修复"）

**技能**: systematic-debugging（4 阶段）

**读取输入**: Step 4 的 `主攻目标`、`完成信号`

**执行流程**（按 systematic-debugging 4 阶段）:
1. **Phase 1: Root Cause Investigation**（读错误 / 复现 / 检查近期变更 / 多组件证据收集 / 数据流追踪）
2. **Phase 2: Pattern Analysis**（找工作示例 / 对比参考 / 识别差异 / 理解依赖）
3. **Phase 3: Hypothesis and Testing**（形成假说 / 最小化测试 / 单变量验证）
4. **Phase 4: Implementation**（创建失败测试 → 实施单一修复 → 验证）

**输出字段**:

**根因**：根因描述（必须穿过症状指向源头）

**修复代码**：代码位置和变更摘要

**验证结果**：失败测试已转为通过

**修复尝试次数**：N 次（≥ 3 次需触发架构质疑）

**置信度**：≥ 80%

**质量门**:
- 根因明确（不是症状）
- 修复最小化（外科手术式）
- 失败测试创建并通过
- 修复尝试次数 < 3
- 置信度 ≥ 80%

**不通过 →**: 返回 Phase 1 重新调查；尝试 ≥ 3 次时停止并质疑架构
**通过 →**: Step 6

---

### Step 6: 编码规范检查

**技能**: karpathy-guidelines

**读取输入**: Step 5 的代码变更（无论分支 A 或 B）

**检查内容**:
- 原则 2: 简洁性（无未请求功能、无过度抽象、代码可压缩）
- 原则 3: 外科手术式修改（无无关变更、匹配现有风格、变更可追溯）

**输出字段**:

**规范检查结果**：通过/不通过

**违反原则**：
- 原则2（如有）
- 原则3（如有）

**具体问题**：
- 文件：路径 | 行号：范围 | 问题：描述 | 建议：修改方案

**置信度**：≥ 80%

**修改建议**：如何修正

**质量门**:
- 无未请求功能
- 无过度抽象
- 修改最小化
- 变更可追溯
- 置信度 ≥ 80%

**不通过 →**: 应用修改建议，返回 Step 5 重新执行（最多 2 次）
**通过 →**: Step 7

---

### Step 7: Pensieve 代码审查（Pipeline 入口调用）

**调用方式**: 调用 `pensieve.run-when-reviewing-code` Pipeline 入口（白盒调用）

**调用约定**: task-workflow 不读取 Pipeline 内部 Task 定义，只调用入口并接收结构化输出。Pipeline 内部 6 Task 顺序由 Pensieve 自治执行。

**审查对象**: Step 6 通过的代码变更

**Pipeline 内置能力**（task-workflow 视为黑盒）:
- 基线探索（git log + 代码热点）
- 审查上下文准备
- 逐文件审查
- 候选验证与过滤（置信度 ≥ 80）
- 生成审查报告
- 自动沉淀可复用结论（Pipeline Task 6，写入 short-term/）

**接收输出**:

**品味评分**：好品味/凑合/垃圾

**致命问题**（CRITICAL 级别）：
- 致命问题1（附证据、代码定位）

**警告问题**（WARNING 级别）：
- 警告1（如有）

**改进方向**：
- 改进方向1

**沉淀状态**：Pipeline Task 6 是否产出沉淀（路径或"无新增沉淀"）

**置信度**：≥ 80%

**质量门**: Pipeline 执行完成（task-workflow 不干预内部），置信度 ≥ 80%
**不通过（taste_score = "垃圾"）→**: 返回 Step 5 改进代码
**通过 →**: Step 8

---

### Step 8: 批评与自我批评

**技能**: criticism-self-criticism

**读取输入**: Step 1-7 全部输出

**输出字段**:

**效果评估**：达标/未达标

**经验教训**：
- 经验教训1
- 经验教训2

**改进措施**：
- 改进措施1
- 改进措施2

**置信度**：≥ 80%

**质量门**: 效果有量化数据，改进措施可操作，置信度 ≥ 80%
**不通过 →**: 返回 Step 5 补充实践
**通过 →**: Step 9

---

### Step 9: 验证

**技能**: verification-before-completion

**读取输入**: Step 5 全部输出 + Step 8 的 `改进措施`

**铁律**: NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE

**执行**:
1. 运行测试命令，确认 0 failures
2. 运行 build 命令，确认 exit 0
3. 验证用户原始需求逐条满足
4. 检查无副作用（无意外文件变更、无未提交孤儿代码）

**输出字段**:

**验证结果**：通过

**测试覆盖率**：覆盖率数据

**证据**：
- 证据1（命令 + 输出）
- 证据2
- 证据3
- 证据4
- 证据5

**副作用**：无

**置信度**：≥ 90%

**markdown 输出**（任务完成报告）: 需求/问题、主要矛盾、实践方法、实现摘要、品味评分、经验教训、验证证据、置信度

**质量门**: 问题已解决/功能已完成，测试通过（fresh evidence），证据 ≥ 5 条，无副作用，置信度 ≥ 90%
**不通过 →**: 返回 Step 5 补充实现或验证
**通过 →**: Step 10

---

### Step 10: Pensieve 沉淀与提交（Pipeline 入口调用）

**调用方式**: 调用 `pensieve.run-when-committing` Pipeline 入口（白盒调用）

**调用约定**: task-workflow 不读取 Pipeline 内部 Task 定义，只调用入口。Pipeline 内部 3 Task（沉淀判断 + 自动沉淀 + 原子提交）由 Pensieve 自治执行。

**Pipeline 内置能力**（task-workflow 视为黑盒）:
- Task 1: 判断本次提交是否有可沉淀洞察（6 个信号）
- Task 2: 提取洞察 + 分类（IS→knowledge / WANT→decision / MUST→maxim）+ 写入 short-term/
- Task 3: 按变更原因聚类 + 原子化提交（祈使句标题 + 解释"为什么"）

**接收输出**:

**沉淀状态**：N 条洞察已写入 short-term/，路径列表

**提交状态**：M 个原子 commit 已完成，commit hash 列表

**短期记忆提示**：若 short-term/ 有到期条目，提示运行 `/pensieve refine`

**质量门**: Pipeline 执行完成，git log 可见原子提交，或 Pipeline 失败回退已记录
**不通过 →**: 按 Pipeline 失败回退规则处理（参考 pipeline.run-when-committing.md 第 124-128 行）

---

## 注意事项

1. **Step 0 Pensieve init 自动检测**: 幂等操作，已存在则跳过；不存在则静默初始化（不询问），含基线探索副作用
2. **pensieve-wand 内嵌于 Step 1**: 利用项目知识库避免重复探索
3. **Pensieve Pipeline 白盒调用 + 内部黑盒**: task-workflow 调用 Pipeline 入口，不干预内部 Task
4. **self-improve 不再独立调用**: Pipeline 已内置沉淀能力（reviewing-code Task 6 + committing Task 2）
5. **Step 5 分支二选一**: 新功能走 brainstorming → executing-plans（修复铁律 2），Bug 修复走 systematic-debugging
6. **karpathy-guidelines 1 次**: 代码产出后必须执行
7. **时间预估**: 短（1-3 小时，简单功能）到长（多轮验证，复杂问题）

## 相关技能

- [investigation-first](../../investigation-first/SKILL.md)
- [pensieve-wand](../../pensieve/.src/templates/agents/pensieve-wand.md)（agent，Step 1 内嵌）
- [contradiction-analysis](../../contradiction-analysis/SKILL.md)
- [mass-line](../../mass-line/SKILL.md)
- [concentrate-forces](../../concentrate-forces/SKILL.md)
- [brainstorming](../../brainstorming/SKILL.md)（新功能分支）
- [writing-plans](../../writing-plans/SKILL.md)（brainstorming 内置）
- [executing-plans](../../executing-plans/SKILL.md)（新功能分支）
- [test-driven-development](../../test-driven-development/SKILL.md)（executing-plans 内置）
- [systematic-debugging](../../systematic-debugging/SKILL.md)（Bug 修复分支）
- [karpathy-guidelines](../../karpathy-guidelines/SKILL.md)
- [criticism-self-criticism](../../criticism-self-criticism/SKILL.md)
- [verification-before-completion](../../verification-before-completion/SKILL.md)
- [Pensieve run-when-reviewing-code](../../pensieve/.src/templates/pipeline.run-when-reviewing-code.md)（Step 7 入口调用）
- [Pensieve run-when-committing](../../pensieve/.src/templates/pipeline.run-when-committing.md)（Step 10 入口调用）
