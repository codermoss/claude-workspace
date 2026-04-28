---
name: 品味提升
description: 根据 Pensieve 审查结果改进代码品味
workflow_id: 3
pensieve_triggers: 2 + self-improve
---

# Workflow 3: 品味提升

## 适用场景

- 代码可工作但品味不佳
- 需要重构、性能优化、安全加固

## 不适用

- 代码功能不正常 → Workflow 1
- 紧急生产故障 → 直接修复，不走 Workflow
- 新功能开发 → Workflow 1

## 数据流图

```
Step 0: Pensieve init
    ↓
Step 1: investigation-first
    ↓ {代码状态, 问题清单[], 改进目标, 影响范围, 置信度}
Step 2: Pensieve taste review（前置审查，完整 6 Task Pipeline）
    ↓ {before_taste_score, fatal_issues[], directions[], 置信度}
Step 3: contradiction-analysis
    ↓ {核心问题, 切入点, 优先级[], 置信度}
Step 4: brainstorming（含 writing-plans）
    ↓ {plan_file, stages[], recommended, 置信度}
Step 5: executing-plans
    ↓ {results[], tests_passed, behavior_unchanged, 置信度}
Step 6: karpathy-guidelines（代码检查）
    ↓ {check_result, violations[], issues[], 置信度}
Step 7: Pensieve taste review（回归评审，完整 6 Task Pipeline）
    ↓ {after_taste_score, regression_issues[], evidence[], 置信度}
    ↓ 输出 markdown 内容（品味审查报告）
Step 8: Pensieve self-improve
    ↓ 沉淀品味改造模式 → short-term/
```

## 执行步骤

### Step 0: 项目知识库初始化

**技能**: Pensieve — 调用 `init` + `doctor` 工具

**执行**: 
1. 直接调用 Pensieve init
   - Pensieve init 自己判断是否需要初始化
   - Pensieve init 保证幂等性（可重复调用）

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

**质量门**: Pensieve init 执行完成，doctor 检查通过（无 MUST_FIX）
**通过 →**: Step 1

---

### Step 1: 调查研究
**技能**: investigation-first
**读取输入**:
- `task.target_files`
- `task.test_files`
- `task.git_log`（`git log --oneline <file> -10`）

**输出字段**:

**代码状态**：代码现状描述

**问题清单**：
- 问题1
- 问题2

**改进目标**：重构/性能/安全

**影响范围**：影响范围

**置信度**：85%

**质量门**: 代码分析完整，改进目标明确，置信度 ≥ 80%
**不通过 →**: 要求用户指定具体文件/模块
**通过 →**: Step 2

---

### Step 2: Pensieve taste review（前置审查）

**调用协议**: 按 [Taste Review 调用协议](../integrations/pensieve.md#taste-review-调用协议) 执行完整 6 Task Pipeline

**审查对象**: Step 1 定位的目标代码

**目的**: 建立"改造前"品味基线

**输出字段**:

**改造前品味评分**：好品味/凑合/垃圾

**致命问题**（CRITICAL 级别）：
- 致命问题1（附证据、代码定位）
- 致命问题2

**警告问题**（WARNING 级别）：
- （如有）

**改进方向**：
- 改进方向1
- 改进方向2

**置信度**：≥ 80%

**质量门**: 审查完成（6 Task 全部执行），置信度 ≥ 80%
**通过 →**: Step 3

---

### Step 3: 矛盾分析
**技能**: contradiction-analysis
**读取输入**: Step 2 的 `致命问题`、`改进方向`

**输出字段**:

**核心问题**：核心问题描述

**切入点**：切入点

**优先级**：
- 优先级1
- 优先级2

**置信度**：85%

**质量门**: 核心问题明确，切入点具体，置信度 ≥ 80%
**不通过 →**: 返回 Step 2 重新审查
**通过 →**: Step 4

---

### Step 4: 方案探索与计划编写

**技能**: brainstorming（内部包含 writing-plans）

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

**置信度**：85%

**质量门**: 计划可执行，每阶段有验证点，置信度 ≥ 80%
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

**行为变化**：无

**置信度**：90%

**质量门**: 所有阶段完成，测试通过，外部行为无变化，置信度 ≥ 80%
**不通过 →**: 回滚当前步骤，返回 Step 3 重新分析
**通过 →**: Step 6

---

### Step 6: 编码规范检查（代码）
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

**置信度**：92%

**修改建议**：如何修正

**质量门**:
- 无未请求功能
- 无过度抽象
- 变更可追溯
- 置信度 ≥ 80%

**不通过 →**: 应用修改建议，返回 Step 5 重新执行（最多 2 次）
**通过 →**: Step 7

---

### Step 7: Pensieve taste review（回归评审）

**调用协议**: 同 Step 2，完整 6 Task Pipeline，确保审查标准一致

**审查对象**: Step 6 通过的改造后代码（与 Step 2 相同的文件范围）

**目的**: 验证品味提升，检测回归问题

**执行步骤**:
1. 对改造后的代码再次执行完整 6 Task taste review
2. 产出"改造后"品味评分
3. 与 Step 2 的"改造前"品味评分对比

**品味提升判定**:
- "垃圾" → "凑合" 或 "好品味" → **提升**
- "凑合" → "好品味" → **提升**
- "好品味" → "好品味" 且致命问题减少 → **提升**
- 评分降低 → **未提升**，返回 Step 4 重新选方案

**输出字段**:

**改造后品味评分**：好品味/凑合/垃圾

**品味提升**：是/否（对比 Step 2 的 `before_taste_score`）

**回归问题**（改造引入的新问题）：
- （如有）

**证据**：
- 证据1：改造前评分 vs 改造后评分
- 证据2：致命问题修复情况
- 证据3：测试通过情况
- 证据4：功能行为无变化
- 证据5：代码结构改善（如嵌套深度↓、函数长度↓）

**置信度**：≥ 90%

**markdown 输出**（品味审查报告）: 改造前品味评分、改造后品味评分、致命问题修复情况、回归问题、证据、置信度

**质量门**: 品味评分提升（或保持"好品味"），功能无变化，测试通过，致命问题已消除，无新增回归问题，证据 ≥ 5 条，置信度 ≥ 90%，markdown 内容已输出
**不通过（品味未提升）→**: 返回 Step 4 重新选方案
**不通过（功能变化）→**: 回滚 → 返回 Step 5
**不通过（引入回归问题）→**: 返回 Step 6 修正

---

### Step 8: Pensieve self-improve（知识沉淀）

**技能**: Pensieve — 调用 `self-improve` 工具

**读取输入**:
- Step 2 的 `改造前品味评分`、`致命问题`
- Step 7 的 `改造后品味评分`、`品味提升`、`证据`

**执行内容**:
1. 将本次发现的品味问题模式沉淀为 `maxims/`（若跨项目适用）
2. 将改造策略选择沉淀为 `decisions/`
3. 将品味提升方法和量化数据沉淀为 `knowledge/`
4. 所有新建条目写入 `short-term/`，7天后提醒 promote 或删除

**质量门**: 至少 1 条品味模式或改造策略被写入 short-term/，或无新增结论时明确说明

---

## 注意事项

1. **Pensieve init**: Step 0 显式执行，确保项目知识库先行
2. **Pensieve taste review 双重调用**: Step 2 建立"改造前"基线，Step 7 执行"改造后"回归评审——两次调用同一 pipeline（完整 6 Task），确保标准一致
3. **Pensieve self-improve**: 1 次（verification 通过后自动执行）
4. **karpathy-guidelines 触发频率**: 1 次（代码检查）
5. **brainstorming 内部串联 writing-plans**: Workflow 编排器不重复调用 writing-plans
6. **时间预估**: 中等（取决于改进范围）

## 相关技能

- [investigation-first](../../investigation-first/SKILL.md)
- [contradiction-analysis](../../contradiction-analysis/SKILL.md)
- [brainstorming](../../brainstorming/SKILL.md)
- [writing-plans](../../writing-plans/SKILL.md)
- [executing-plans](../../executing-plans/SKILL.md)
- [karpathy-guidelines](../../karpathy-guidelines/SKILL.md)
- [Pensieve](../../pensieve/SKILL.md)
