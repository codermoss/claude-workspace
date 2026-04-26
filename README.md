# Claude Workspace

> 生产级 Claude Code 配置工作空间，集成技能库与工程方法论

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/Skills-43-orange)](#技能库)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Compatible-black)](https://claude.ai/code)

## 目录

- [项目概述](#项目概述)
- [仓库结构](#仓库结构)
- [快速开始](#快速开始)
- [架构设计](#架构设计)
- [技能库](#技能库)
- [设计理念](#设计理念)
- [技能同步](#技能同步)
- [致谢](#致谢)
- [许可证](#许可证)

## 项目概述

一个综合性的 Claude Code 工作空间，集成了：

- **唯物辩证法方法论**：基于证据的决策与迭代改进
- **三层架构**：Qiushi（哲学层） → Pensieve（基础设施层） → task-workflow（编排层）
- **43 个生产级技能**：覆盖从调查到部署的完整开发生命周期
- **质量门系统**：多层验证机制，集成 Karpathy Guidelines
- **知识管理**：通过 Pensieve 自动捕获和复用经验

## 仓库结构

```
.claude/
├── .gitignore              # 运行时文件排除规则
├── CLAUDE.md               # 全局工程指令（154 行）
│                           #   - 唯物辩证法核心原则
│                           #   - 三层架构路由规则
│                           #   - Git 工作流与安全规范
│                           #   - 技术栈适配（Python/Go/K8s/LLM）
├── output-styles/          # AI 输出风格定义
│   └── moss.md             #   - MOSS 人格（204 行）
│                           #   - 交互协议与语言模式
├── skills/                 # 技能库（43 个技能，121 个文件）
│   ├── Qiushi/             #   - 11 个唯物辩证法方法论技能
│   ├── Superpowers/        #   - 14 个工程实践技能
│   ├── SpecKit/            #   - 14 个规格驱动开发工具
│   └── Standalone/         #   -  4 个专项技能
└── README.md               # 本文件
```

**远程仓库：** https://github.com/codermoss/claude-workspace.git

## 快速开始

```bash
# 克隆到 Claude Code 配置目录
git clone https://github.com/codermoss/claude-workspace.git ~/.claude
```

Claude Code 启动时自动加载 `CLAUDE.md` 和 `skills/` 目录下的所有技能。使用 `/技能名` 调用：

```
/task-workflow              # 工作流编排
/investigation-first        # 基于证据的调查
/pensieve                   # 知识库管理
```

## 架构设计

### 三层架构

```
┌─────────────────────────────────────────┐
│  Qiushi（哲学层）                        │
│  唯物辩证法原则                          │
└──────────────────┬──────────────────────┘
                   │ 指导
┌──────────────────▼──────────────────────┐
│  Pensieve（基础设施层）                  │
│  知识库 + 品味审查                       │
└──────────────────┬──────────────────────┘
                   │ 支撑
┌──────────────────▼──────────────────────┐
│  task-workflow（编排层）                 │
│  工作流路由 + 质量门                     │
└─────────────────────────────────────────┘
```

### 核心组件

#### 1. 任务工作流编排器

`/task-workflow` — 将任务路由到合适的技能链，并执行质量门检查

**三个标准工作流：**

- **W1: 通用执行** — 功能迭代与 Bug 修复（日常开发主力）
  - 触发场景：新功能需求、Bug 报告、代码修改
  - 质量门：Karpathy Guidelines + Pensieve 品味审查
  
- **W2: 新项目启动** — 新项目的战略探索
  - 触发场景："启动新项目"、"探索方向"、"寻找切入点"
  - 输出：分阶段的路线图
  - 集成：产出路线图后，每个功能点可用 Spec Kit 执行交付
  
- **W3: 品味提升** — 代码可工作但需要重构/优化
  - 触发场景："提升代码质量"、"重构"、"优化"
  - 流程：Pensieve 品味审查 → 重构 → 前后对比

**质量门系统（4 层）：**

1. **铁律（5 条）**：不可违反的约束（如"保持用户可见行为不变"）
2. **步骤级置信度**：每个步骤必须达到 ≥80% 置信度才能继续
3. **Karpathy Guidelines**：LLM 编码最佳实践（仅在 W1/W3 应用）
4. **失败回退**：质量门失败时自动回滚

#### 2. Pensieve 知识库

`/pensieve` — 项目知识管理，自动捕获经验

**数据模型（4 层）：**

```
<project>/.pensieve/
├── maxims/        # MUST：铁律（绝不违反）
├── decisions/     # WANT：架构决策（含上下文）
├── knowledge/     # IS：事实（文件位置、模块边界、调用链）
└── pipelines/     # HOW：可复用工作流
```

**核心工具：**

- `/pensieve init` — 初始化知识库，播种默认模板
- `/pensieve doctor` — 健康检查（frontmatter、链接、目录结构）
- `/pensieve self-improve` — 捕获新洞察到短期暂存区
- `/pensieve refine` — 精炼知识库（五问审阅 + 压缩抽象）
- `/pensieve upgrade` — 更新 Pensieve 技能源码
- `/pensieve migrate` — 结构迁移与遗留清理

**品味审查（6 任务流水线）：**

- **独立调用**：`review this code`、`审查 src/auth.py`、`检查 docs/api.md 的质量`
- **自动触发**：在 task-workflow W1/W3 阶段自动运行
- **审查范围**：代码、文档、模块（任意粒度）

#### 3. 规格驱动开发（可选）

Spec Kit 提供完整的 规格 → 计划 → 任务 → 实现 流水线：

- **独立使用**：需求明确时，直接使用 Spec Kit
- **与 task-workflow 组合**：W2 产出路线图后，每个功能点用 Spec Kit 执行交付

**关键命令：**

- `/speckit-specify` — 生成功能规格
- `/speckit-plan` — 创建实施计划
- `/speckit-tasks` — 分解为依赖排序的任务
- `/speckit-implement` — 执行任务列表
- `/speckit-analyze` — 跨文档一致性检查

## 技能库

### Qiushi（求是）— 方法论

基于唯物辩证法的思维框架，提供从调查到决策的完整方法论链：

| 技能 | 说明 | 核心功能 |
|------|------|----------|
| `arming-thought` | 武装思想 | 每次对话校准"实事求是"总原则 |
| `investigation-first` | 调查研究 | 决策前事实收集与一手信息验证 |
| `contradiction-analysis` | 矛盾分析 | 多因素冲突下的优先级排序 |
| `practice-cognition` | 实践认识论 | 实践→认识→再实践螺旋验证 |
| `mass-line` | 群众路线 | 多方意见收集与整合 |
| `criticism-self-criticism` | 批评与自我批评 | 工作验收与复盘改进 |
| `concentrate-forces` | 集中兵力 | 多任务并发时的资源聚焦 |
| `overall-planning` | 统筹兼顾 | 多目标动态平衡决策 |
| `protracted-strategy` | 持久战略 | 长期目标的阶段性规划 |
| `spark-prairie-fire` | 星火燎原 | 从零起步的最小可行路径 |
| `workflows` | 工作流组合 | 多技能协同编排 |

**致谢：** Qiushi 技能库由 [@HughYau](https://github.com/HughYau) 开发，基于唯物辩证法原理构建。[查看原仓库](https://github.com/HughYau/qiushi-skill)

### Superpowers — 工程实践

覆盖完整开发生命周期的工程方法论：

| 技能 | 说明 | 核心功能 |
|------|------|----------|
| `brainstorming` | 方案探索 | 创意发散与方案对比评估 |
| `writing-plans` | 计划编写 | 多步骤任务计划文档化 |
| `executing-plans` | 执行计划 | 按计划分步实现与进度追踪 |
| `test-driven-development` | TDD | 测试先行开发流程 |
| `systematic-debugging` | 系统化调试 | 根因追踪与防御性排查 |
| `verification-before-completion` | 完成前验证 | 声称完成前的证据检查 |
| `using-git-worktrees` | 工作树管理 | Git Worktree 隔离开发环境 |
| `using-superpowers` | 技能发现 | 自动扫描匹配可用技能 |
| `dispatching-parallel-agents` | 并行代理 | 无依赖任务的并行调度 |
| `subagent-driven-development` | 子代理开发 | 代理驱动的分治实现 |
| `finishing-a-development-branch` | 分支收尾 | 开发完成后的合并与清理 |
| `receiving-code-review` | 接收审查 | 审查反馈的系统化处理 |
| `requesting-code-review` | 请求审查 | 代码审查请求与上下文准备 |
| `writing-skills` | 技能编写 | 新技能的创建与验证规范 |

**致谢：** Superpowers 技能库由 [@obra](https://github.com/obra) 创建和维护。[查看原仓库](https://github.com/obra/superpowers)

### Spec Kit — 规格驱动开发

GitHub 官方规格驱动开发工具集，文档→计划→任务→实现的完整链路：

| 技能 | 说明 |
|------|------|
| `speckit-constitution` | 项目宪章管理 |
| `speckit-specify` | 需求规格生成 |
| `speckit-clarify` | 规格澄清（最多 5 个定向问题） |
| `speckit-plan` | 实施计划生成 |
| `speckit-tasks` | 任务分解（依赖排序） |
| `speckit-analyze` | 跨文档一致性分析 |
| `speckit-implement` | 按任务列表执行实施 |
| `speckit-checklist` | 基于需求的定制检查表 |
| `speckit-taskstoissues` | 任务→GitHub Issues 转换 |
| `speckit-git-commit` | Spec Kit 命令后的自动提交 |
| `speckit-git-feature` | 功能分支创建 |
| `speckit-git-initialize` | Git 仓库初始化 |
| `speckit-git-remote` | 远端仓库检测 |
| `speckit-git-validate` | 分支命名规范校验 |

**致谢：** Spec Kit 由 GitHub 官方开发和维护。[查看原仓库](https://github.com/github/spec-kit)

### 独立专项技能

| 技能 | 说明 | 核心功能 | 致谢 |
|------|------|----------|------|
| `task-workflow` | 工作流编排器 | 3 个标准工作流 + 质量门，集成 Karpathy-Guidelines | 本仓库原创 |
| `Pensieve` | 项目知识库 | 四层知识模型（maxims/decisions/knowledge/pipelines），自动沉淀经验 | [@kingkongshot](https://github.com/kingkongshot) - [原仓库](https://github.com/kingkongshot/Pensieve) |
| `karpathy-guidelines` | 编码规范检查 | 基于 Andrej Karpathy 的 LLM 编码陷阱观察，代码质量门 | [@forrestchang](https://github.com/forrestchang) - [原仓库](https://github.com/forrestchang/andrej-karpathy-skills) |
| `linux-performance-annlysis-skill` | Linux 性能分析 | CPU/内存/I/O/网络四维诊断与优化报告 | [@simple-tec](https://github.com/simple-tec) - [原仓库](https://github.com/simple-tec/linux-performance-annlysis-skill) |

## 设计理念

### 方法论基础

```
实践 → 认识 → 再实践 → 再认识（螺旋上升）
```

- **实践是检验真理的唯一标准**：所有决策基于实际验证，而非假设
- **数据驱动决策**：基于事实和证据，而非直觉或经验
- **完整方法论链**：从调查（investigation-first）到矛盾分析（contradiction-analysis）到实践验证（practice-cognition）

### 工作流编排

```
编排层 → 定义 Step 顺序、数据传递格式、质量门标准
   │
   ▼
质量门层 → 验证逻辑 + 失败回退机制 + 证据检查
```

- **task-workflow** 编排流程，集成 Pensieve 知识沉淀和 Karpathy-Guidelines 代码审查
- 通用执行 / 新项目启动 / 品味提升 三条标准路径
- 四层质量门系统确保输出质量

### 知识沉淀

Pensieve 四层知识模型持续生长：

| 层级 | 内容 | 示例 |
|------|------|------|
| **maxims** | 铁律，不可违反 | "保持用户可见行为不变" |
| **decisions** | 架构决策，含上下文 | "选择方案 A 因为 X，拒绝 B 因为 Y" |
| **knowledge** | 事实性知识 | 文件结构、模块边界、API 约定 |
| **pipelines** | 可执行流程 | 提交时运行、代码审查时运行 |

## 技能同步

`skills/` 由 [cc-switch](https://github.com/codermoss/cc-switch) 统一管理，与 `~/.cc-switch/skills/` 保持同步。更新技能源后 cc-switch 自动同步到本地并推送至本仓库。

```
.cc-switch/skills/  ──cc-switch 同步──>  .claude/skills/  ──git push──>  GitHub
```

## 致谢

本仓库技能库基于以下开源项目的贡献构建：

| 项目 | 作者 | 类型 | 许可证 |
|------|------|------|--------|
| [Superpowers](https://github.com/obra/superpowers) | [@obra](https://github.com/obra) | 工程实践技能 | MIT |
| [Qiushi（求是）](https://github.com/HughYau/qiushi-skill) | [@HughYau](https://github.com/HughYau) | 唯物辩证法方法论 | MIT |
| [Pensieve](https://github.com/kingkongshot/Pensieve) | [@kingkongshot](https://github.com/kingkongshot) | 知识库与工作流路由 | MIT |
| [Spec Kit](https://github.com/github/spec-kit) | [@github](https://github.com/github) | 规格驱动开发工具集 | MIT |
| [Linux Performance Analysis](https://github.com/simple-tec/linux-performance-annlysis-skill) | [@simple-tec](https://github.com/simple-tec) | Linux 性能分析 | MIT |
| [Karpathy Guidelines](https://github.com/forrestchang/andrej-karpathy-skills) | [@forrestchang](https://github.com/forrestchang) | LLM 编码最佳实践 | MIT |

特别感谢所有原作者的开源贡献，使得这个综合性工作空间得以构建。

## 许可证

[MIT](LICENSE) — 本仓库自身遵循 MIT 许可证。各技能库的原始许可证请参考其上游仓库。
