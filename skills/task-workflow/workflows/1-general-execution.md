---
name: 通用执行
description: 新功能迭代与Bug修复，程序生命周期核心工作流
workflow_id: 1
pensieve_triggers: 1 + self-improve
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

## 设计原则

本 Workflow 是完整认知流水线，10 步全部必须执行。不设条件跳过——进入 Workflow 的任务本身就值得完整思考。

practice-cognition（Step 5）是认知哲学层，TDD 和 systematic-debugging 是实践方法层：
- practice-cognition 管"我处于哪个认识阶段？假说是什么？循环何时终止？"
- TDD/systematic-debugging 管"怎么写代码？怎么验证？"

两者是上下层关系，不冗余。

## 数据流图

```
Step 0: Pensieve init
    ↓
Step 1: investigation-first
    ↓ {问题/需求, 约束, 边界条件, 关键发现, 置信度}
Step 2: contradiction-analysis
    ↓ {主要矛盾, 矛盾性质, 假说清单, 置信度}
Step 3: mass-line
    ↓ {多源反馈, 共识问题, 量化指标, 置信度}
Step 4: concentrate-forces
    ↓ {主攻目标, 暂缓清单, 完成信号, 置信度}
Step 5: practice-cognition（认知哲学层）
    ├─ 新功能 → 内含 test-driven-development（实践方法层）
    │   ↓ {测试用例, 实现代码, 测试结果, 实践发现, 置信度}
    └─ Bug修复 → 内含 systematic-debugging（实践方法层）
        ↓ {根因, 修复代码, 验证结果, 实践发现, 置信度}
Step 6: karpathy-guidelines
    ↓ {规范检查结果, 违规项, 置信度}
Step 7: Pensieve taste review
    ↓ {品味评分, 致命问题, 警告问题, 改进方向, 置信度}
Step 8: criticism-self-criticism
    ↓ {效果评估, 经验教训, 改进措施, 置信度}
Step 9: verification-before-completion
    ↓ {验证结果, 证据, 副作用, 置信度}
    ↓ 输出 markdown 报告
Step 10: Pensieve self-improve
    ↓ 沉淀经验到 short-term/
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
- `task.description`（问题描述或功能需求）
- `task.related_files`（可选）
- `task.constraints`（可选）
- `task.recent_changes`（可选，`git log --oneline -10`）

**输出字段**:

**问题/需求**：问题或需求的明确描述

**约束条件**：
- 约束1
- 约束2

**边界条件**：
- 边界1
- 边界2
- 边界3

**关键发现**：
- 发现1
- 发现2

**置信度**：≥ 80%

**质量门**: 问题/需求明确，约束清晰，边界条件 ≥ 3 项，置信度 ≥ 80%
**不通过 →**: 补充信息，重新调查
**通过 →**: Step 2

---

### Step 2: 矛盾分析

**技能**: contradiction-analysis

**读取输入**: Step 1 的 `问题/需求`、`约束条件`、`关键发现`

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

**选择理由**：
- 影响范围：高/中/低
- 解决难度：高/中/低
- 依赖关系：是否被其他任务依赖

**暂缓清单**：
- 暂缓任务1（理由）
- 暂缓任务2（理由）

**完成信号**：什么情况下认为主攻目标完成

**置信度**：≥ 80%

**质量门**: 主攻目标明确，暂缓清单完整，完成信号可验证，置信度 ≥ 80%
**不通过 →**: 返回 Step 2 重新分析优先级
**通过 →**: Step 5

---

### Step 5: 实践认识论

**技能**: practice-cognition（认知哲学层）

**执行说明**:

本 Step 是实践→认识→再实践→再认识的完整循环。首先宣告当前所处的认识阶段，然后根据任务类型选择实践方法：

- **新功能** → 使用 test-driven-development（红→绿→重构循环）
- **Bug修复** → 使用 systematic-debugging（根因→模式→假说→实施循环）

practice-cognition 负责：阶段判断、假说框架、循环终止条件
TDD/systematic-debugging 负责：具体代码编写和验证

**读取输入**: Step 4 的 `主攻目标`、`完成信号`

**阶段判断**（每次进入必须先做）:
- "我目前处于：**感性认识阶段**" → 读代码/文档，记录事实
- "我目前处于：**理性认识形成阶段**" → 写出假说
- "我目前处于：**实践验证阶段**" → 运行/执行，观察结果 vs 预期
- "我目前处于：**总结升华阶段**" → 写出学到的东西

**循环终止条件**（开始前明确说明）:
"当 [Step 4 的完成信号] 时，本轮实践认识循环完成。"

**输出字段**:

**认识阶段**：当前所处阶段

**假说**：本轮的假说内容

**实践方法**：test-driven-development / systematic-debugging

**测试用例**（TDD 时）：
- 正常场景：描述 — 结果：通过
- 边界场景：描述 — 结果：通过
- 异常场景：描述 — 结果：通过

**根因**（systematic-debugging 时）：根因描述

**实现代码**：代码位置和变更摘要

**测试结果**：0 failures

**实践发现**：实践中发现的新认识

**循环次数**：1

**置信度**：≥ 80%

**质量门**:
- TDD：覆盖正常/边界/异常 3 种场景，测试结果 0 failures，置信度 ≥ 80%
- systematic-debugging：根因明确，修复最小化，验证通过，置信度 ≥ 80%
- practice-cognition：循环终止条件达成，有实践发现

**证伪 →**: 返回 Step 2 重新形成假说（循环 ≥ 3 次时停止，质疑架构设计）
**证实 →**: Step 6

---

### Step 6: 编码规范检查

**技能**: karpathy-guidelines

**读取输入**: Step 5 的代码变更

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

**不通过 →**: 应用修改建议，返回 Step 5 重新编写代码（最多 2 次）
**通过 →**: Step 7

---

### Step 7: Pensieve 品味审查

**调用协议**: 按 [Taste Review 调用协议](../integrations/pensieve.md#taste-review-调用协议) 执行完整 6 Task Pipeline

**审查对象**: Step 6 通过的代码变更

**目的**: 检查代码品味，识别设计问题

**输出字段**:

**品味评分**：好品味/凑合/垃圾

**致命问题**（CRITICAL 级别）：
- 致命问题1（附证据、代码定位）
- （如有）

**警告问题**（WARNING 级别）：
- （如有）

**改进方向**：
- 改进方向1
- （如有）

**置信度**：≥ 80%

**质量门**: 审查完成（6 Task 全部执行），置信度 ≥ 80%
**不通过（taste_score = "垃圾"）→**: 返回 Step 5 改进代码
**通过 →**: Step 8

---

### Step 8: 批评与自我批评

**技能**: criticism-self-criticism

**读取输入**: Step 1-7 全部输出，Step 5 的 `循环次数`、`实践发现`

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

**输出字段**:

**验证结果**：通过

**测试覆盖率**：覆盖率数据

**证据**：
- 证据1
- 证据2
- 证据3
- 证据4
- 证据5

**副作用**：无

**置信度**：≥ 90%

**markdown 输出**（任务完成报告）: 需求/问题、主要矛盾、实践方法、实现摘要、品味评分、经验教训、验证证据、置信度

**质量门**: 问题已解决/功能已完成，测试通过，证据 ≥ 5 条，无副作用，置信度 ≥ 90%，markdown 内容已输出
**不通过 →**: 返回 Step 5 补充实现或验证
**通过 →**: Step 10

---

### Step 10: Pensieve 知识沉淀

**技能**: Pensieve — 调用 `self-improve` 工具

**读取输入**:
- Step 5 的 `根因`（bug修复时）、`实践发现`
- Step 7 的 `品味评分`、`致命问题`
- Step 8 的 `经验教训`、`改进措施`

**执行内容**:
1. 将调试策略和排查路径沉淀为 `knowledge/`
2. 将修复策略/设计选择理由沉淀为 `decisions/`
3. 若发现跨项目适用的铁律，沉淀为 `maxims/`
4. 将品味问题模式沉淀为 `maxims/`（若跨项目适用）
5. 所有新建条目写入 `short-term/`，7天后提醒 promote 或删除

**质量门**: 至少 1 条可复用结论被写入 short-term/，或无新增结论时明确说明

---

## 注意事项

1. **Pensieve init**: Step 0 显式执行，确保项目知识库先行
2. **Pensieve taste review**: 1 次，完整 6 Task Pipeline，不做裁剪
3. **Pensieve self-improve**: 1 次，verification 通过后自动执行
4. **karpathy-guidelines**: 1 次，代码产出后必须执行
5. **practice-cognition 循环**: Step 5 内置实践→认识循环，证伪时返回 Step 2，≥ 3 次停止并质疑架构
6. **执行分支**: Step 5 根据任务类型二选一（TDD 或 systematic-debugging），不要同时触发
7. **时间预估**: 短（1-3 小时，简单功能）到长（多轮验证，复杂问题）

## 相关技能

- [investigation-first](../../investigation-first/SKILL.md)
- [contradiction-analysis](../../contradiction-analysis/SKILL.md)
- [mass-line](../../mass-line/SKILL.md)
- [concentrate-forces](../../concentrate-forces/SKILL.md)
- [practice-cognition](../../practice-cognition/SKILL.md)
- [test-driven-development](../../test-driven-development/SKILL.md)
- [systematic-debugging](../../systematic-debugging/SKILL.md)
- [karpathy-guidelines](../../karpathy-guidelines/SKILL.md)
- [criticism-self-criticism](../../criticism-self-criticism/SKILL.md)
- [verification-before-completion](../../verification-before-completion/SKILL.md)
- [Pensieve](../../pensieve/SKILL.md)
