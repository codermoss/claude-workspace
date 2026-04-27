---
name: 技能映射表
description: 三大技能库在 workflows 中的使用映射
---

# 技能映射表

## 三大技能库职责

### Qiushi（11 个方法论技能）

**职责**：唯物辩证法方法论基础，提供思维框架

| 技能 | 职责 |
|------|------|
| arming-thought | 建立总原则，路由下游技能 |
| investigation-first | 调查研究 |
| contradiction-analysis | 矛盾分析 |
| practice-cognition | 实践认识论 |
| mass-line | 群众路线 |
| criticism-self-criticism | 批评与自我批评 |
| protracted-strategy | 持久战略 |
| concentrate-forces | 集中兵力 |
| spark-prairie-fire | 星火燎原 |
| overall-planning | 统筹兼顾 |
| workflows | 工作流组合 |

### Superpowers（14 个工程实践技能）

**职责**：工程实践，提供具体方法

| 技能 | 职责 |
|------|------|
| using-superpowers | 自动扫描匹配技能 |
| brainstorming | 方案探索 |
| writing-plans | 计划编写 |
| executing-plans | 执行计划 |
| test-driven-development | TDD |
| systematic-debugging | 系统化调试 |
| verification-before-completion | 证据验证 |
| requesting-code-review | 请求代码审查 |
| receiving-code-review | 接收代码审查 |
| dispatching-parallel-agents | 并行任务调度 |
| subagent-driven-development | 子代理驱动开发 |
| using-git-worktrees | Git Worktree 管理 |
| finishing-a-development-branch | 完成开发分支 |
| writing-skills | 编写技能 |

### Pensieve（品味审查）

**职责**：品味审查（代码/提交/方案），通过 Pipeline 机制执行。详见 [integrations/pensieve.md](pensieve.md)。

---

## Task-Workflow 质量门

### Karpathy-Guidelines（编码规范检查）

**职责**：Task-Workflow 的质量门实现，提供编码规范验证逻辑（类似 Pensieve Pipeline 的 Task 4 验证步骤）

**检查内容**：
- 原则 1：Think Before Coding（假设显性化、多方案对比）
- 原则 2：Simplicity First（无未请求功能、无过度抽象）
- 原则 3：Surgical Changes（外科手术式修改、变更可追溯）
- 原则 4：Goal-Driven Execution（成功标准可验证）

**集成方式**：
- W1 Step 6：practice-cognition 后自动调用
- W3 Step 6：executing-plans 后自动调用
- 质量门标准：置信度 ≥ 80%、违反原则 ≤ 2 项

**设计参考**：学习 Pensieve Pipeline 的验证逻辑（Task Blueprint + 信号判断规则 + 失败回退）

详见 [integrations/karpathy-guidelines.md](karpathy-guidelines.md)。


## Workflow 技能映射

### Workflow 1: 通用执行

```
pensieve init(前置) → investigation-first → contradiction-analysis → mass-line → concentrate-forces → practice-cognition(内含TDD/systematic-debugging) → karpathy-guidelines → [Pensieve taste review] → criticism-self-criticism → verification-before-completion → [Pensieve self-improve]
输出: markdown 任务完成报告
```

### Workflow 2: 新项目启动

```
pensieve init → investigation-first → contradiction-analysis → spark-prairie-fire → protracted-strategy → [Pensieve taste review] → verification-before-completion → [Pensieve self-improve]
输出: markdown 项目启动方案
```

### Workflow 3: 品味提升

```
investigation-first → Pensieve taste review(前置) → contradiction-analysis → brainstorming(含writing-plans) → executing-plans → karpathy-guidelines(代码检查) → Pensieve taste review(回归评审) → [Pensieve self-improve]
输出: markdown 品味审查报告
```

## 相关文档

- [SKILL.md](../SKILL.md)
- [执行规范](../docs/execution-standards.md)
- [Pensieve 集成规则](pensieve.md)
