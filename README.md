# Claude Workspace

> 领航员空间站控制系统 550W — Claude Code 全局配置与技能库

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/Skills-43-orange)](#技能库)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-550W-black)](https://claude.ai/code)

## 目录

- [仓库结构](#仓库结构)
- [快速开始](#快速开始)
- [技能库](#技能库)
- [设计思想](#设计思想)
- [技能同步](#技能同步)
- [致谢](#致谢)
- [许可证](#许可证)

## 仓库结构

```
.claude/
├── .gitignore              # Git 忽略规则（排除运行时文件）
├── CLAUDE.md               # 全局工程指令（146 行，Markdown 格式）
│                           #   - 唯物辩证法核心原则
│                           #   - 工作模式与核心工具库路由
│                           #   - 5 大技术栈适配规范
│                           #   - Git 工作流与安全规范
├── output-styles/          # AI 输出风格定义
│   └── moss.md             #   - MOSS 风格（204 行）
│                           #   - 角色身份、交互协议、语言特征
│                           #   - 系统化标签、数据呈现方式
├── skills/                 # 技能库（43 个技能，155+ 文件）
│   ├── Qiushi/             #   - 11 个唯物辩证法方法论
│   ├── Superpowers/        #   - 14 个工程实践技能
│   ├── SpecKit/            #   - 14 个规格驱动开发工具
│   └── Standalone/         #   -  4 个独立专项技能
└── README.md               #  -
```

**远程仓库：** https://github.com/codermoss/claude-workspace.git

## 快速开始

```bash
# 克隆到 Claude Code 配置目录
git clone https://github.com/codermoss/claude-workspace.git ~/.claude
```

Claude Code 启动时自动加载 `CLAUDE.md` 和 `skills/` 目录下的全部技能。使用 `/技能名` 调用：

```
/task-workflow              # 工作流编排
/investigation-first        # 调查研究
/pensieve                   # 知识库管理
```

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

### 独立专项技能

| 技能 | 说明 | 核心功能 |
|------|------|----------|
| `task-workflow` | 工作流编排器 | 3 个标准工作流 + 质量门，集成 Karpathy-Guidelines |
| `Pensieve` | 项目知识库 | 四层知识模型（maxims/decisions/knowledge/pipelines），自动沉淀经验 |
| `karpathy-guidelines` | 编码规范检查 | 基于 Karpathy 的 LLM 编码陷阱观察，代码质量门 |
| `linux-performance-annlysis-skill` | Linux 性能分析 | CPU/内存/I/O/网络四维诊断与优化报告 |

## 设计思想

### 方法论基础

```
实践 → 认识 → 再实践 → 再认识（螺旋上升）
```

- 实践是检验真理的唯一标准
- 数据驱动决策，基于事实而非假设
- 从调查（investigation-first）到矛盾分析（contradiction-analysis）到实践验证（practice-cognition）

### 工作流编排

```
编排层 → 定义 Step 顺序、数据传递格式、质量门标准
   │
   ▼
质量门层 → 验证逻辑 + 失败回退机制 + 证据检查
```

- **task-workflow** 编排流程，集成 Pensieve 知识沉淀和 Karpathy-Guidelines 代码审查
- 通用执行 / 新项目启动 / 品味提升 三条标准路径

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

| 项目 | 作者 | 类型 |
|------|------|------|
| [Superpowers](https://github.com/obra/superpowers) | [@obra](https://github.com/obra) | 工程实践技能 |
| [Qiushi（求是）](https://github.com/HughYau/qiushi-skill) | [@HughYau](https://github.com/HughYau) | 唯物辩证法方法论 |
| [Pensieve](https://github.com/kingkongshot/Pensieve) | [@kingkongshot](https://github.com/kingkongshot) | 知识库与工作流路由 |
| [Spec Kit](https://github.com/github/spec-kit) | [@github](https://github.com/github) | 规格驱动开发工具集 |
| [Linux Performance Analysis](https://github.com/simple-tec/linux-performance-annlysis-skill) | [@simple-tec](https://github.com/simple-tec) | Linux 性能分析 |
| [Karpathy Guidelines](https://x.com/karpathy) | Andrej Karpathy | LLM 编码最佳实践 |
| Task-Workflow | 本仓库原创 | 工作流编排器 |

## 许可证

[MIT](LICENSE) — 本仓库自身遵循 MIT 许可证。各技能库的原始许可证请参考其上游仓库。
