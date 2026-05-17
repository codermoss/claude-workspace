---
name: 品味提升
description: 根据 Pensieve 审查结果改进代码品味（含改造前后对比）
workflow_id: 3
pensieve_integration: pipeline-entry-call
---

# Workflow 3: 品味提升

## 适用场景

- 代码可工作但品味不佳
- 需要重构、性能优化、安全加固

## 不适用

- 代码功能不正常 → Workflow 1
- 紧急生产故障 → 直接修复，不走 Workflow
- 新功能开发 → Workflow 1

## 前置说明

W3 的 Step 0 自动检测并初始化项目知识库。`pensieve init` 是幂等操作（已存在不覆盖），无副作用，无需用户确认。

## 设计原则

1. 重构决策必须基于客观证据（前置 taste review 提供基线）
2. 改造前/后双 Pipeline 调用形成量化对比
3. 修复原 W3 缺失的 verification 与 criticism（铁律 3 修复）
4. Pensieve 调用：2 次 run-when-reviewing-code + 1 次 run-when-committing

## 数据流图

```
Step 0   Pensieve 知识库初始化检测（自动）
    ↓
Step 1   investigation-first（含 pensieve-wand 检索）
    ↓ {代码状态, 问题清单, 已知陷阱, 改进目标, 影响范围, 置信度}
Step 2   pensieve.run-when-reviewing-code（前置基线，Pipeline 入口调用）
    ↓ {改造前品味评分, 致命问题, 改进方向, Task 6 沉淀问题模式}
Step 3   contradiction-analysis
    ↓ {核心问题, 切入点, 优先级, 置信度}
Step 4   brainstorming（含 writing-plans）
    ↓ {设计文档, 计划文件, 阶段划分, 置信度}
Step 5   executing-plans
    ↓ {执行结果, 测试状态, 行为变化, 置信度}
Step 6   karpathy-guidelines
    ↓ {规范检查结果, 违规项, 置信度}
Step 7   pensieve.run-when-reviewing-code（回归审查，Pipeline 入口调用）
    ↓ {改造后品味评分, 回归问题, 证据, Task 6 沉淀改造经验}
Step 8   verification-before-completion
    ↓ {测试通过, build 成功, 行为无变化, 证据, 置信度}
Step 9   criticism-self-criticism
    ↓ {效果评估, 经验教训, 改进措施, 置信度}
Step 10  pensieve.run-when-committing（Pipeline 入口调用）
    ↓ Task 1 沉淀判断 + Task 2 沉淀重构总结 + Task 3 原子提交
```

## 执行步骤

### Step 0: Pensieve 知识库初始化检测

**技能**: Pensieve — 调用 `init` 工具（幂等操作）

**执行**: 自动检测 `<project>/.pensieve/` 是否存在：
- 不存在 → 静默执行 `pensieve init`（幂等，不询问）
  - 创建 `maxims/`、`decisions/`、`knowledge/`、`pipelines/` 四个子目录
  - 播种默认模板 + 默认 pipeline + taste-review knowledge
  - 执行基线探索（git log + 代码热点），产出可沉淀候选清单
- 已存在 → 跳过

**质量门**: `.pensieve/` 目录存在且包含四个子目录
**通过 →**: Step 1

---

### Step 1: 调查研究（含 pensieve-wand 检索）

**技能**: investigation-first
**Pensieve 集成**: 派遣 pensieve-wand agent 检索项目知识库

**读取输入**:
- `task.target_files`
- `task.test_files`
- `task.git_log`（`git log --oneline <file> -10`）

**执行流程**:
1. 派遣 pensieve-wand agent，传入目标文件/模块关键词
   - 重构最大风险是推翻已有架构决策，wand 必须先检索 decisions/
2. 接收 wand 输出（已知信息 / 已知陷阱 / 历史决策 / 待探索）
3. 基于 wand 结果识别"不可推翻的已有决策"，作为重构边界

**输出字段**:

**代码状态**：代码现状描述

**问题清单**：
- 问题1
- 问题2

**已知陷阱**（来自 pensieve-wand）：
- 陷阱1（来源：路径）

**已有架构决策**（来自 pensieve-wand decisions/）：
- 决策1（不可推翻 / 可重新评估）

**改进目标**：重构/性能/安全

**影响范围**：影响范围

**置信度**：≥ 80%

**质量门**: 代码分析完整，改进目标明确，pensieve-wand 已检索，置信度 ≥ 80%
**不通过 →**: 要求用户指定具体文件/模块
**通过 →**: Step 2

---

### Step 2: Pensieve 前置基线审查（Pipeline 入口调用）

**调用方式**: 调用 `pensieve.run-when-reviewing-code` Pipeline 入口（白盒调用）

**调用约定**: task-workflow 不读取 Pipeline 内部 Task 定义。Pipeline 内部 6 Task 由 Pensieve 自治执行。

**审查对象**: Step 1 定位的目标代码（改造前状态）

**目的**: 建立"改造前"品味基线，提供客观证据为后续矛盾分析所用

**Pipeline 内置能力**（task-workflow 视为黑盒）:
- 基线探索 / 上下文准备 / 逐文件审查 / 候选验证 / 报告生成
- Task 6 自动沉淀"致命问题模式"为 short-term/maxims/

**接收输出**:

**改造前品味评分**：好品味/凑合/垃圾

**致命问题**（CRITICAL 级别）：
- 致命问题1（附证据、代码定位）
- 致命问题2

**警告问题**（WARNING 级别）：
- 警告1（如有）

**改进方向**：
- 改进方向1
- 改进方向2

**沉淀状态**：Pipeline Task 6 沉淀的问题模式路径

**置信度**：≥ 80%

**质量门**: Pipeline 执行完成，置信度 ≥ 80%
**通过 →**: Step 3

---

### Step 3: 矛盾分析

**技能**: contradiction-analysis
**读取输入**: Step 1 的 `问题清单`、`改进目标` + Step 2 的 `致命问题`、`改进方向`

**输出字段**:

**核心问题**：核心问题描述

**切入点**：切入点

**优先级**：
- 优先级1
- 优先级2

**置信度**：≥ 80%

**质量门**: 核心问题明确，切入点具体，置信度 ≥ 80%
**不通过 →**: 返回 Step 2 重新审查
**通过 →**: Step 4

---

### Step 4: 方案探索与计划编写

**技能**: brainstorming（内置 writing-plans）

brainstorming 的 Checklist Step 9 要求 "Transition to implementation — invoke writing-plans skill"。本 Step 等待 brainstorming 完整执行完毕后，直接获取 `plan_file`。

**读取输入**: Step 3 的 `核心问题`、`优先级`

**执行流程**:
1. brainstorming 探索方案 → 呈现设计 → 用户审批
2. brainstorming 内部调用 writing-plans 编写实现计划
3. 产出计划文件

**输出字段**:

**推荐方案**：方案描述

**计划文件**：计划文件路径

**阶段划分**：
- 阶段1
- 阶段2

**置信度**：≥ 80%

**质量门**: 用户已审批设计（HARD-GATE），计划无占位符，每阶段有验证点，置信度 ≥ 80%
**不通过 →**: 返回 Step 3 重新分析矛盾
**通过 →**: Step 5

---

### Step 5: 执行计划

**技能**: executing-plans
**读取输入**: Step 4 的 `计划文件`

**输出字段**:

**执行结果**：
- 阶段1结果
- 阶段2结果

**测试状态**：通过

**行为变化**：无（重构应保持外部行为不变）

**置信度**：≥ 80%

**质量门**: 所有阶段完成，测试通过，外部行为无变化，置信度 ≥ 80%
**不通过 →**: 回滚当前步骤，返回 Step 3 重新分析
**通过 →**: Step 6

---

### Step 6: 编码规范检查

**技能**: karpathy-guidelines
**读取输入**: Step 5 的 `执行结果`、代码变更范围

**检查内容**:
- 原则 2: 简洁性
- 原则 3: 外科手术式修改

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
- 变更可追溯
- 置信度 ≥ 80%

**不通过 →**: 应用修改建议，返回 Step 5 重新执行（最多 2 次）
**通过 →**: Step 7

---

### Step 7: Pensieve 回归审查（Pipeline 入口调用）

**调用方式**: 调用 `pensieve.run-when-reviewing-code` Pipeline 入口（与 Step 2 同一 Pipeline，确保审查标准一致）

**调用约定**: task-workflow 不读取 Pipeline 内部 Task 定义。Pipeline 内部 6 Task 由 Pensieve 自治执行。

**审查对象**: Step 6 通过的改造后代码（与 Step 2 相同的文件范围）

**目的**: 验证品味提升 + 检测回归问题 + 沉淀改造经验

**Pipeline 内置能力**（task-workflow 视为黑盒）:
- 基线探索 / 上下文准备 / 逐文件审查 / 候选验证 / 报告生成
- Task 6 自动沉淀"改造经验"为 short-term/decisions/（与 Step 2 沉淀的"问题模式"内容互补）

**品味提升判定**（task-workflow 编排器执行）:
- "垃圾" → "凑合" 或 "好品味" → **提升**
- "凑合" → "好品味" → **提升**
- "好品味" → "好品味" 且致命问题减少 → **提升**
- 评分降低 → **未提升**，返回 Step 4 重新选方案

**接收输出**:

**改造后品味评分**：好品味/凑合/垃圾

**品味提升**：是/否（对比 Step 2 的 `改造前品味评分`）

**回归问题**（改造引入的新问题）：
- 回归问题1（如有）

**证据**：
- 证据1：改造前评分 vs 改造后评分
- 证据2：致命问题修复情况
- 证据3：测试通过情况
- 证据4：功能行为无变化
- 证据5：代码结构改善（如嵌套深度↓、函数长度↓）

**沉淀状态**：Pipeline Task 6 沉淀的改造经验路径

**置信度**：≥ 90%

**质量门**: 品味评分提升（或保持"好品味"），无新增回归问题，证据 ≥ 5 条，置信度 ≥ 90%
**不通过（品味未提升）→**: 返回 Step 4 重新选方案
**不通过（功能变化）→**: 回滚 → 返回 Step 5
**不通过（引入回归问题）→**: 返回 Step 6 修正
**通过 →**: Step 8

---

### Step 8: 验证

**技能**: verification-before-completion

**读取输入**: Step 5 的 `执行结果`、Step 7 的 `回归问题`、`证据`

**铁律**: NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE

**执行**:
1. 运行测试命令，确认 0 failures（重构最大风险是回归，必须有 fresh evidence）
2. 运行 build 命令，确认 exit 0
3. 验证用户可见行为无变化（重构硬约束）
4. 检查无副作用（无意外文件变更、无未提交孤儿代码）

**输出字段**:

**验证结果**：通过

**测试覆盖率**：覆盖率数据

**证据**：
- 证据1（测试命令 + 输出 0 failures）
- 证据2（build 命令 + exit 0）
- 证据3（行为对比：改造前 vs 改造后）
- 证据4（diff 范围检查）
- 证据5（性能/品味量化对比）

**副作用**：无

**置信度**：≥ 90%

**质量门**: 测试通过（fresh evidence），build 成功，行为无变化，证据 ≥ 5 条，置信度 ≥ 90%
**不通过 →**: 返回 Step 5 补充实现或验证
**通过 →**: Step 9

---

### Step 9: 批评与自我批评

**技能**: criticism-self-criticism

**读取输入**: Step 1-8 全部输出，重点审视：
- Step 7 的 `回归问题`（重构是否引入隐性问题）
- Step 4 的 `推荐方案` vs Step 7 的 `品味提升`（方案选择是否合理）
- Step 5 的循环次数（执行是否反复）

**重构反思重点**:
- 改造是否引入新的耦合或抽象错位？
- 测试是否覆盖到改造的所有路径？
- 是否存在"虽通过但意图不清"的修改？

**输出字段**:

**效果评估**：达标/未达标

**经验教训**：
- 经验教训1（重构方法论层面）
- 经验教训2

**改进措施**：
- 改进措施1
- 改进措施2

**置信度**：≥ 80%

**质量门**: 效果有量化数据（前/后品味评分对比），改进措施可操作，置信度 ≥ 80%
**不通过 →**: 返回 Step 7 重新审查
**通过 →**: Step 10

---

### Step 10: Pensieve 沉淀与提交（Pipeline 入口调用）

**调用方式**: 调用 `pensieve.run-when-committing` Pipeline 入口（白盒调用）

**调用约定**: task-workflow 不读取 Pipeline 内部 Task 定义。Pipeline 内部 3 Task 由 Pensieve 自治执行。

**Pipeline 内置能力**（task-workflow 视为黑盒）:
- Task 1: 判断本次重构是否有可沉淀洞察（重构通常命中"架构决策"和"新模式发现"信号）
- Task 2: 提取重构总结 + 分类 + 写入 short-term/
  - 重构策略选择 → decisions/
  - 品味提升量化数据 → knowledge/
  - 跨项目品味原则 → maxims/
- Task 3: 按变更原因聚类 + 原子化提交

**接收输出**:

**沉淀状态**：N 条洞察已写入 short-term/，路径列表

**提交状态**：M 个原子 commit 已完成，commit hash 列表

**短期记忆提示**：若 short-term/ 有到期条目，提示运行 `/pensieve refine`

**质量门**: Pipeline 执行完成，git log 可见原子提交，或 Pipeline 失败回退已记录
**不通过 →**: 按 Pipeline 失败回退规则处理

---

## 注意事项

1. **Step 0 Pensieve init 自动检测**: 幂等操作，已存在则跳过；不存在则静默初始化
2. **pensieve-wand 内嵌于 Step 1**: 重构必须先检索已有架构决策
3. **Pensieve Pipeline 双次入口调用**: Step 2 前置基线 + Step 7 回归审查，使用同一 Pipeline 确保标准一致
4. **两次 Pipeline Task 6 沉淀内容互补**: Step 2 沉淀"问题模式"，Step 7 沉淀"改造经验"
5. **修复原 W3 P0 铁律违反**: 新增 Step 8 verification-before-completion（铁律 3）
6. **新增 Step 9 criticism**: 重构容易引入隐性问题，需事后方法论反思
7. **不再独立调用 self-improve**: Pipeline 内置沉淀能力（reviewing-code Task 6 × 2 + committing Task 2）
8. **brainstorming 内部串联 writing-plans**: Workflow 编排器不重复调用 writing-plans
9. **时间预估**: 中等（取决于改进范围）

## 相关技能

- [investigation-first](../../investigation-first/SKILL.md)
- [pensieve-wand](../../pensieve/.src/templates/agents/pensieve-wand.md)（agent，Step 1 内嵌）
- [contradiction-analysis](../../contradiction-analysis/SKILL.md)
- [brainstorming](../../brainstorming/SKILL.md)
- [writing-plans](../../writing-plans/SKILL.md)（brainstorming 内置）
- [executing-plans](../../executing-plans/SKILL.md)
- [karpathy-guidelines](../../karpathy-guidelines/SKILL.md)
- [verification-before-completion](../../verification-before-completion/SKILL.md)
- [criticism-self-criticism](../../criticism-self-criticism/SKILL.md)
- [Pensieve run-when-reviewing-code](../../pensieve/.src/templates/pipeline.run-when-reviewing-code.md)（Step 2 + Step 7 入口调用）
- [Pensieve run-when-committing](../../pensieve/.src/templates/pipeline.run-when-committing.md)（Step 10 入口调用）
