---
name: Pensieve 集成规则
description: Pensieve 在 task-workflow 中的调用规则——Pipeline 入口白盒调用 + 内部黑盒自治
---

# Pensieve 集成规则

## 调用哲学：白盒入口 + 黑盒内部

```
task-workflow 调用 pensieve.<specific-pipeline>
                ↑                              ↑
       白盒：显式指定调谁              黑盒：Pipeline 内部 Task 自治
```

### 关键约定

1. **task-workflow 调用 Pensieve Pipeline 入口**（不读取 Pipeline 内部 Task 定义）
2. **Pipeline 内部 Task 由 Pensieve 自治执行**（task-workflow 不干预顺序、不替换 Task）
3. **task-workflow 只接收 Pipeline 的结构化输出**（品味评分、致命问题、沉淀状态）
4. **不调用 Pensieve 单独工具**（除一次性的 init / refine 由用户手动触发）

### 抽象边界禁令

| 禁止行为 | 为什么禁止 |
|----------|-----------|
| 在 Workflow 步骤里枚举 Pipeline 内部 Task 1-6 | 抽象泄漏，Pensieve Pipeline 升级会破坏 Workflow |
| 拆解 Pipeline，只调用其中部分 Task | 违反 Pipeline 自治原则，破坏 Pipeline 内部数据流 |
| 在 Pipeline 之外重复调用 self-improve | reviewing-code Task 6 + committing Task 2 已覆盖沉淀 |
| 在 Workflow Step 0 强制 init | init 是一次性操作，违反工具语义 |

---

## task-workflow 调用范围

### 调用入口（3 类）

| 调用入口 | 调用方式 | 触发场景 |
|---------|---------|---------|
| `pensieve-wand` agent | 派遣 agent 检索 | Workflow Step 1 内嵌（investigation-first 阶段） |
| `pensieve.run-when-reviewing-code` Pipeline | 入口调用 | 代码审查节点（含战略文档审查） |
| `pensieve.run-when-committing` Pipeline | 入口调用 | 沉淀+提交节点（W1/W3 末尾） |

### 不在 task-workflow 调用范围

```
init / upgrade / migrate / doctor / refine
  ↓
用户级一次性操作或维护操作
通过 /pensieve <tool> 命令独立调用，不进入 Workflow Step
```

### 编排器与 Pensieve 职责边界

| 编排器（task-workflow）负责 | Pensieve 负责 |
|---------------------------|---------------|
| 何时调用哪个 Pipeline | Pipeline 内部 Task 顺序、完成标准、失败回退 |
| 接收 Pipeline 结构化输出，做下一步路由 | 审查标准、置信度阈值、沉淀分类逻辑 |
| 跨 Step 数据流（Pipeline 之间不知道彼此） | 单 Pipeline 内 Task 之间的数据流 |
| 5 条铁律 + Step 置信度门 | Pipeline 内部信号判断（≥ 80） |

---

## Pipeline 入口调用协议

### 调用前提

1. 项目已执行 `pensieve init`
   - 优先使用 `<project>/.pensieve/pipelines/run-when-*.md`
   - 回退到全局模板 `~/.claude/skills/pensieve/.src/templates/pipeline.run-when-*.md`
2. Knowledge 同理：
   - 优先 `<project>/.pensieve/knowledge/`
   - 回退 `~/.claude/skills/pensieve/.src/templates/knowledge/`

### 调用方式

```
task-workflow Step N 执行体:
  1. 准备调用上下文（审查范围 / 待提交变更）
  2. 调用 Pipeline 入口：触发对应 Pipeline 执行
  3. 等待 Pipeline 自治完成全部 Task
  4. 接收 Pipeline 输出（品味评分 / 沉淀路径 / 提交结果）
  5. 编排器基于输出做质量门判断与下一步路由
```

### 输出契约

#### run-when-reviewing-code 的输出契约

| Workflow 字段 | 来源 | 类型 |
|-------------|------|------|
| `taste_score` | Pipeline 报告中 CRITICAL/WARNING 计数推导 | string ("好品味"/"凑合"/"垃圾") |
| `fatal_issues` | Pipeline Task 4 已验证 CRITICAL 条目 | list |
| `warnings` | Pipeline Task 4 已验证 WARNING 条目 | list |
| `directions` | Pipeline Task 5 报告中每条问题的修复建议 | list |
| `confidence` | Pipeline Task 4 已验证问题的最低置信度 | number |
| `improvement_paths` | Pipeline Task 6 沉淀路径（或 "无新增沉淀"） | list |

#### run-when-committing 的输出契约

| Workflow 字段 | 来源 | 类型 |
|-------------|------|------|
| `improvement_paths` | Pipeline Task 2 沉淀路径列表 | list |
| `commit_hashes` | Pipeline Task 3 原子提交 hash 列表 | list |
| `short_term_due` | Pipeline 末尾短期记忆提示 | bool / 提示文本 |

### 三级品味评分推导

评分标准来源于 `knowledge/taste-review/content.md` 的预警信号表：

**CRITICAL 信号**（任一触发"垃圾"评分）：
- 嵌套层级 > 3 层
- 函数长度 > 100 行
- 异常处理压倒主逻辑
- 浅模块（接口复杂度接近实现复杂度）
- 信息泄漏（模块内部决策暴露到外部）

**WARNING 信号**（累积 ≥ 4 时降级为"凑合"）：
- 局部变量 > 10
- 防御式默认值泛滥
- 命名困难
- fallback 掩盖上游问题

**评分规则**：
- 0 CRITICAL + 0 WARNING → "好品味"
- 0 CRITICAL + ≤3 WARNING → "凑合"
- ≥1 CRITICAL → "垃圾"

---

## pensieve-wand Agent 集成

### 触发位置

W1/W2/W3 的 Step 1 investigation-first 内部派遣。

### 派遣方式

```
派遣 pensieve-wand agent，传入任务关键词
  ↓
agent 工作模式（双系统决策）:
  ├─ System 1: MEMORY.md 关键词命中 → 零工具调用直答
  └─ System 2: 未命中 → 启动图谱探索（预算 ≤ 10 工具调用）
  ↓
返回结构化输出:
  - 已知信息（缓存知识 / 文件路径 / 模块边界 / 历史决策）
  - 已知陷阱（过往错误 / 边缘情况 / 失败模式）
  - 建议路径（最高效前进路径）
  - 待探索（缓存知识中的空白）
```

### 编排器职责

- 派遣 wand 并接收结果
- 将"已知陷阱"作为后续 contradiction-analysis 的证据输入
- 将"已有架构决策"作为重构边界（W3 特别重要）
- 将"待探索"作为 Step 1 后续调查的方向

### 不替代的部分

pensieve-wand 是**调查辅助**，不替代 investigation-first 自身的调查纪律。investigation-first 的「七项注意」仍需遵守。

---

## init 自动检测规则

### 触发时机

每个 Workflow 的 Step 0 自动检测 `<project>/.pensieve/`。

### 执行逻辑

```
if .pensieve/ 不存在:
    静默执行 pensieve init（幂等操作，不询问用户）
    init 内部:
      - 创建 maxims/decisions/knowledge/pipelines/ 四个子目录
      - 播种默认模板 + 默认 pipeline + taste-review knowledge
      - 执行基线探索（git log + 代码热点），产出可沉淀候选清单
    继续 Step 1
else:
    跳过，继续 Step 1
```

### 设计理由

1. **幂等保证**: init.md 行 1 明确"幂等，不覆盖已有用户数据"，重复调用零风险
2. **减少用户心智负担**: 用户无需记得手动 init，工作流自动处理
3. **基线探索副作用**: init 执行时会做一次基线探索，为 Step 1 investigation 提供初始线索
4. **触发条件天然控频**: 已存在则跳过，老项目零成本

### 与原 v3.0 设计的差异

```
v3.0 初版（错误）:               修正后（正确）:
"前置条件，用户手动执行"     →    "Step 0 自动检测，幂等执行"
不存在则停止 Workflow        →    不存在则静默初始化
用户必须记得 /pensieve init  →    工作流自动处理
```

修正理由：原设计把工程负担转嫁给用户，违反工作流编排"减少决策"的原则。

---

## 逐 Workflow 调用规则

### 总览

| Workflow | pensieve-wand | run-when-reviewing-code | run-when-committing |
|----------|---------------|-------------------------|--------------------|
| W1: 通用执行 | Step 1 内嵌 | Step 7（1 次） | Step 10（1 次） |
| W2: 新项目启动 | Step 1 内嵌 | Step 6（1 次，审查战略文档） | Step 8（1 次，沉淀战略+提交战略文档） |
| W3: 品味提升 | Step 1 内嵌 | Step 2 + Step 7（2 次） | Step 10（1 次） |

### W1: 通用执行

**run-when-reviewing-code**: Step 7 调用，审查 Step 6 karpathy-guidelines 通过后的代码变更。Pipeline Task 6 自动沉淀品味问题模式。

**run-when-committing**: Step 10 调用，自动判断沉淀价值（6 个信号）+ 自动沉淀全流程经验 + 原子化提交。替代原设计中的独立 self-improve + 用户手动 git commit。

### W2: 新项目启动

**run-when-reviewing-code**: Step 6 调用，审查 Step 4+5 产出的战略文档（根据地、发展路线图、阶段战略）。Pipeline 内 Knowledge 模板基于代码维度，但品味信号对战略文档同样适用：
- 嵌套深度 → 阶段嵌套
- 模块边界 → 阶段边界
- 接口简洁 → 阶段切换条件清晰
- 抽象层次 → 战略抽象是否恰当

Pipeline Task 6 自动沉淀架构决策为 decision、技术选型为 knowledge。

**run-when-committing**: Step 8 调用，目的是让 W2 完成项目知识库的螺旋式增长——战略决策入库，下一轮 W1 实施时 pensieve-wand 可检索复用。沉淀范围：根据地选择理由 → decisions/ / 技术选型 → decisions/ / 项目上下文 → knowledge/ / 跨项目启动原则 → maxims/。Pipeline Task 3 提交 Step 7 写入 `docs/strategy/YYYY-MM-DD-<project>.md` 的战略文档。

### W3: 品味提升

**run-when-reviewing-code**: Step 2（前置基线）+ Step 7（回归审查），共 2 次调用。

两次调用使用同一 Pipeline 确保标准一致：
- Step 2: 建立"改造前"品味基线，Task 6 沉淀"问题模式"
- Step 7: 验证"改造后"品味提升，Task 6 沉淀"改造经验"

两次 Task 6 沉淀内容**完全互补**，不冗余。

**品味提升判定**（Step 7，编排器执行）:
- "垃圾" → "凑合" 或 "好品味" → **提升**
- "凑合" → "好品味" → **提升**
- "好品味" → "好品味" 且致命问题减少 → **提升**
- 评分降低 → **未提升**，返回 Step 4 重新选方案

**run-when-committing**: Step 10 调用，沉淀重构总结（重构策略 → decisions / 品味提升量化数据 → knowledge / 跨项目品味原则 → maxims）+ 原子化提交。

---

## 与原设计的关键差异

| 维度 | 原设计 | 改造后 |
|------|--------|--------|
| Pensieve 调用方式 | 拆解 Task 1-6 显式列出 | Pipeline 入口调用，内部黑盒 |
| init 处理 | Step 0 自动执行（含基线探索） | Step 0 自动检测 + 幂等执行（保持 v2.0 模式，仅说明"幂等"语义） |
| pensieve-wand | 未使用 | Step 1 内嵌 |
| self-improve | 独立 Step 调用（重复调度） | 移除，由 Pipeline 内置沉淀替代 |
| 提交流程 | 用户手动 git commit | run-when-committing Pipeline 自动 |
| Pipeline 复用 | 仅 reviewing-code | reviewing-code + committing 双 Pipeline |
| 抽象边界 | 暴露 6 Task 内部 | 仅依赖 Pipeline SKILL 接口 |

## 失败回退

### Pipeline 调用失败

按 Pipeline 自身的失败回退规则（参考各 Pipeline 文件末尾"失败回退"章节）：

- `run-when-reviewing-code` 失败回退：参考 pipeline.run-when-reviewing-code.md 第 152-156 行
- `run-when-committing` 失败回退：参考 pipeline.run-when-committing.md 第 124-128 行

编排器不重写 Pipeline 失败回退逻辑，只接收失败信号并按质量门规则停止当前 Step。

### pensieve-wand 失败

- agent 调用超时或返回空 → 标记"知识库无相关条目"，继续 Step 1 自身调查
- agent 返回错误 → 记录错误，跳过 wand 输出，依赖 investigation-first 自身完成

---

## 相关文档

- [Pensieve SKILL.md](../../pensieve/SKILL.md)
- [Pensieve run-when-reviewing-code Pipeline](../../pensieve/.src/templates/pipeline.run-when-reviewing-code.md)
- [Pensieve run-when-committing Pipeline](../../pensieve/.src/templates/pipeline.run-when-committing.md)
- [Pensieve taste-review Knowledge](../../pensieve/.src/templates/knowledge/taste-review/content.md)
- [Pensieve pensieve-wand Agent](../../pensieve/.src/templates/agents/pensieve-wand.md)
- [Pensieve tool-boundaries](../../pensieve/.src/references/tool-boundaries.md)
- [技能映射表](skill-mapping.md)
- [执行规范](../docs/execution-standards.md)
