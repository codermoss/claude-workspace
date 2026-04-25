---
name: Pensieve 集成规则
description: Pensieve 在 workflows 中的调用规则——taste review 协议、完整流程触发规则、字段映射
---

# Pensieve 集成规则

## 职责边界

### task-workflow 调用范围

```
Pensieve 完整流程（知识管理系统生命周期）
  init → doctor → self-improve → refine → ...
  不在 task-workflow 编排范围内（用户通过 /pensieve 命令独立操作）

Pensieve taste review（品味审查能力）
  pipeline.run-when-reviewing-code（6 Task）+ knowledge/taste-review/content.md
  task-workflow 唯一调用的 Pensieve 能力

Pensieve self-improve（知识沉淀）
  Workflow 完成后自动触发，沉淀经验到 short-term/
```

### 编排器职责

- **task-workflow 负责**：定义在哪个 Workflow 的哪个 Step 调用 Pensieve 的哪种能力
- **Pensieve 负责**：审查标准、审查流程、置信度阈值——全部由 Pipeline 和 Knowledge 自行定义

task-workflow 不定义 Pensieve 的审查行为，只做条件控制的编排入口。

---

## Taste Review 调用协议

### 协议概述

task-workflow 编排器不调用 Pensieve 的某个"工具"，而是读取 Pensieve 的 pipeline 模板 + knowledge 模板，按 pipeline 定义的 Task 顺序执行完整 6 Task 审查流程。

**Pensieve taste review = 完整 6 Task Pipeline，不做裁剪。**

### 前置条件

1. 项目已执行 `pensieve init`（项目 `.pensieve/` 目录存在）
   → 使用 `<project>/.pensieve/pipelines/run-when-reviewing-code.md`
2. 项目未初始化
   → 回退到全局模板 `~/.claude/skills/pensieve/.src/templates/pipeline.run-when-reviewing-code.md`
3. Knowledge 同理：
   → 优先 `<project>/.pensieve/knowledge/taste-review/content.md`
   → 回退 `~/.claude/skills/pensieve/.src/templates/knowledge/taste-review/content.md`

### 执行流程

编排器按 pipeline 的 6 个 Task 顺序执行：

```
Task 1: 基线探索（git log + 代码热点）
    ↓
Task 2: 准备审查上下文（确定范围、技术语言、风险点）
    ↓
Task 3: 逐文件审查（对照 taste-review/content.md）
    ↓
Task 4: 验证候选并过滤误报（移除置信度 < 80 项）
    ↓
Task 5: 生成可执行审查报告（CRITICAL → WARNING 排序）
    ↓
Task 6: 沉淀可复用结论（可选，写入 decision/knowledge）
```

每个 Task 的执行标准、输入输出见 pipeline 文件本身。编排器不定义 Task 内部行为，只负责顺序推进和质量门检查。

### 输出映射（Pipeline → Workflow 字段）

Pipeline Task 5 产出的审查报告为自然语言文本。编排器从报告中提取以下结构化字段：

| Workflow 字段 | 提取规则 | 类型 |
|-------------|---------|------|
| `taste_score` | 0 CRITICAL + 0 WARNING → "好品味"<br>0 CRITICAL + ≤3 WARNING → "凑合"<br>≥1 CRITICAL → "垃圾" | string |
| `fatal_issues` | Pipeline Task 4 已验证问题中 severity=CRITICAL 的条目 | list |
| `warnings` | Pipeline Task 4 已验证问题中 severity=WARNING 的条目 | list |
| `directions` | Pipeline Task 5 报告中每条问题的修复建议 | list |
| `confidence` | Pipeline Task 4 已验证问题列表的最低置信度 | number |

### 三级品味评分的推导依据

评分标准来源于 `knowledge/taste-review/content.md` 的预警信号表：

**CRITICAL 信号**（触发"垃圾"评分）：
- 嵌套层级 > 3 层
- 函数长度 > 100 行
- 异常处理压倒主逻辑
- 浅模块（接口复杂度接近实现复杂度）
- 信息泄漏（模块内部决策暴露到外部）

**WARNING 信号**（累积 ≥ 4 时降级为"凑合"）：
- 局部变量 > 10
- 防御式默认值泛滥
- 命名困难（难以命名、难以解释）
- fallback 掩盖上游问题

编排器在提取时，逐条对照 Pipeline Task 4 的已验证问题，统计 CRITICAL 和 WARNING 数量，按上述规则计算 `taste_score`。

---

## init 自动执行规则

### 触发时机

Workflow 启动时（W1/W3），检测 `<project>/.pensieve/` 是否存在。

### 执行逻辑

```
if .pensieve/ 不存在:
    静默执行 pensieve init
    不询问用户（init 是安全的建设性操作：创建目录 + 播种模板）
    继续 Workflow
else:
    跳过
```

### 例外

- **W2（新项目启动）**: init 作为 Step 0 显式执行，确保项目知识库与项目同生
- **W1/W2/W3**: 全部在启动时检查并自动 init（.pensieve/ 不存在时静默执行，不询问）

---

## 逐 Workflow 触发规则

### 总览

| Workflow | init | taste review | self-improve |
|----------|------|-------------|-------------|
| W1: 通用执行 | 自动（不存在时） | 1 | 1 |
| W2: 新项目启动 | Step 0 | 1 | 1 |
| W3: 品味提升 | 自动（不存在时） | 2 | 1 |

### W1: 通用执行

**taste review**: **1 次，无条件触发。** W1 是完整认知流水线，10 步全部执行。taste review 在 Step 7（karpathy-guidelines 之后），审查全部代码变更。使用完整 6 Task Pipeline，不做裁剪。

**审查对象**: Step 6 karpathy-guidelines 通过后的代码变更

**self-improve 沉淀内容**:
- 调试策略和排查路径 → `knowledge/`
- 修复策略/设计选择理由 → `decisions/`
- 跨项目适用的调试原则 → `maxims/`
- 品味问题模式 → `maxims/`（若跨项目适用）

### W2: 新项目启动

**taste review**: **1 次，无条件触发。** W2 产出战略文档（根据地分析、发展路线图、战略规划），taste review 审查设计品味——抽象层次是否恰当、模块边界是否清晰、接口是否简洁、阶段划分是否合理。使用完整 6 Task Pipeline，不做裁剪。

**审查对象**: Step 3+4 产出的战略文档

**self-improve 沉淀内容**:
- 根据地选择理由和技术选型 → `decisions/`
- 架构方向选择 → `decisions/`
- 项目上下文（目录结构、模块边界） → `knowledge/`

### W3: 品味提升

**taste review**: **2 次，无条件触发。**
- Step 2（前置审查）: 建立改造前品味基线
- Step 7（回归评审）: 验证改造后品味提升，检测回归

两次调用使用同一 pipeline（完整 6 Task）和同一 knowledge，确保审查标准一致。

**品味提升判定**（Step 7）:
- "垃圾" → "凑合" 或 "好品味" → **提升**
- "凑合" → "好品味" → **提升**
- "好品味" → "好品味" 且致命问题减少 → **提升**
- 评分降低 → **未提升**，返回 Step 4 重新选方案

**self-improve 沉淀内容**:
- 品味问题模式 → `maxims/`（跨项目适用时）
- 改造策略选择 → `decisions/`
- 品味提升方法和量化数据 → `knowledge/`

---

## 相关文档

- [Pensieve SKILL.md](../../pensieve/SKILL.md)
- [Pensieve Pipeline](../../pensieve/.src/templates/pipeline.run-when-reviewing-code.md)
- [Pensieve Taste Review Knowledge](../../pensieve/.src/templates/knowledge/taste-review/content.md)
- [技能映射表](skill-mapping.md)
- [执行规范](../docs/execution-standards.md)
