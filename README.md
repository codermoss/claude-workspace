# MOSS / Claude Workspace

领航员空间站控制系统 550W — Claude Code 配置与技能库。

## 结构

```
.claude/
├── CLAUDE.md          # 全局工程指令
├── output-styles/     # 输出风格定义
├── skills/            # 技能库（cc-switch 同步）
└── README.md
```

## 远程

https://github.com/codermoss/claude-workspace.git

## 技能库组成

### 核心技能库

#### Qiushi（求是）- 方法论技能
基于唯物辩证法的 11 个方法论技能：
- `investigation-first` - 调查研究
- `contradiction-analysis` - 矛盾分析
- `practice-cognition` - 实践认识论
- `mass-line` - 群众路线
- `criticism-self-criticism` - 批评与自我批评
- `protracted-strategy` - 持久战略
- `concentrate-forces` - 集中兵力
- `spark-prairie-fire` - 星火燎原
- `overall-planning` - 统筹兼顾
- `arming-thought` - 武装思想
- `workflows` - 工作流组合

#### Superpowers - 工程实践技能
14 个工程实践技能：
- `brainstorming` - 方案探索
- `writing-plans` - 计划编写
- `executing-plans` - 执行计划
- `test-driven-development` - TDD
- `systematic-debugging` - 系统化调试
- `verification-before-completion` - 证据验证
- `karpathy-guidelines` - 编码规范检查
- 等

#### Pensieve - 品味审查
项目知识库和工作流路由器：
- 四层知识模型（maxims / decisions / knowledge / pipelines）
- 自动沉淀经验
- 品味审查 Pipeline

#### Task-Workflow - 工作流编排器
学习 Qiushi + Pensieve 设计的工作流编排系统：
- 3 个标准工作流（通用执行、新项目启动、品味提升）
- 编排层 + 质量门实现
- 集成 Karpathy-Guidelines 作为代码质量门

### 扩展技能库

#### Spec Kit - 规格驱动开发
GitHub 官方规格驱动开发工具集：
- `spec-workflow` - 规格工作流编排
- `spec-workflow-mcp` - MCP 服务器集成
- 基于规格文档驱动开发流程
- 自动化需求追踪和验证

#### ZCF - 工程化工具集
中文工程化技能库：
- `zcf:git-commit` - Git 提交规范
- `zcf:code-review` - 代码审查
- `zcf:refactor` - 重构指南
- 等工程化最佳实践

#### Linux Performance Analysis
Linux 性能分析技能库：
- CPU 性能分析
- 内存分析
- I/O 分析
- 网络分析

## 安装

```bash
# 克隆仓库到 Claude Code skills 目录
git clone https://github.com/codermoss/claude-workspace.git ~/.claude

# skills/ 由 cc-switch 同步到 .claude/skills/
```

## 使用

技能会自动加载到 Claude Code 中。使用 `/技能名` 调用，例如：

```
/task-workflow
/investigation-first
/pensieve
```

## 设计思想

### Qiushi（求是）
- 实践是检验真理的唯一标准
- 实践 → 认识 → 再实践 → 再认识，螺旋上升
- 数据驱动决策，基于事实而非假设

### Task-Workflow
- 编排层：定义 Step 顺序、数据传递格式、质量门标准（学习 Qiushi）
- 质量门层：提供验证逻辑、失败回退机制（学习 Pensieve）

### Pensieve
- 四层知识模型：maxims（铁律）、decisions（决策）、knowledge（事实）、pipelines（流程）
- 自动沉淀经验，持续生长的项目记忆

## 致谢

本技能库基于以下开源项目构建，感谢原作者的贡献：

### 核心技能库

- **Superpowers** - [@obra](https://github.com/obra)
  https://github.com/obra/superpowers
  工程实践技能库，提供 brainstorming、writing-plans、executing-plans 等生产级技能

- **Qiushi（求是）** - [@HughYau](https://github.com/HughYau)
  https://github.com/HughYau/qiushi-skill
  基于唯物辩证法的方法论技能库，提供 investigation-first、contradiction-analysis、practice-cognition 等思维框架

- **Pensieve** - [@kingkongshot](https://github.com/kingkongshot)
  https://github.com/kingkongshot/Pensieve
  项目知识库和工作流路由器，提供四层知识模型和品味审查 Pipeline

### 扩展技能库

- **Spec Kit** - [@github](https://github.com/github)
  https://github.com/github/spec-kit
  GitHub 官方规格驱动开发工具集

- **ZCF（中文工程化）** - [@UfoMiao](https://github.com/UfoMiao)
  https://github.com/UfoMiao/zcf
  中文工程化技能库，提供 Git 提交规范、代码审查、重构指南等最佳实践

- **Linux Performance Analysis** - [@simple-tec](https://github.com/simple-tec)
  https://github.com/simple-tec/linux-performance-annlysis-skill
  Linux 性能分析技能库，涵盖 CPU、内存、I/O、网络四大领域的性能诊断和优化

- **Task-Workflow** - 本仓库原创
  学习 Qiushi + Pensieve 设计的工作流编排器，集成 Karpathy-Guidelines 作为质量门实现

- **Karpathy-Guidelines** - 基于 [Andrej Karpathy](https://x.com/karpathy) 的 LLM 编码陷阱观察
  编码规范检查，减少 LLM 常见编码错误

## 许可证

MIT

本仓库遵循 MIT 许可证。各技能库的原始许可证请参考对应的上游仓库。

## 贡献

欢迎提交 Issue 和 Pull Request。
