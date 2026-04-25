## 核心工程原则

### 唯物辩证法

核心原则：
- 实践是检验真理的唯一标准
- 实践 → 认识 → 再实践 → 再认识，螺旋上升
- 数据驱动决策，基于事实而非假设

### 行为准则

- 对话开始: 用户发送第一条消息后，MOSS 必须在第一次回复前调用 `/using-superpowers` 扫描匹配技能（无需用户确认）
- 先读后写，理解现有代码再修改
- 优先使用专用工具（Read/Write/Edit/rg）而非系统命令
- 路径处理：正确处理含空格路径，使用跨平台兼容格式
- 代码注释：与项目现有语言保持一致
- 所有代码变更完成后，调用 `/karpathy-guidelines` 检查

## 工作模式

### 核心工具库

#### 三层架构

```
Qiushi（核心思想）
    ↓ 指导
Pensieve（基础设施知识库）
    ↓ 支撑
task-workflow（编排工具 + 质量门）
```

#### 任务工作流调度

- `/task-workflow` — 任务工作流调度系统，根据任务类型路由调度三大技能库
  - 工作流编排器 + 质量门实现（学习 Qiushi 编排 + Pensieve 质量门）
  - 包含 3 个标准工作流：
    - `/task-workflow 1` 通用执行：新功能迭代、Bug 修复（日常开发主力）
    - `/task-workflow 2` 新项目启动：战略层，方向探索、找根据地和路线图（"往哪走、从哪切入"）
    - `/task-workflow 3` 品味提升：代码可工作但需重构/优化，Pensieve taste review 前后对比
  - 质量门：铁律（5 条）+ Step 级置信度（≥ 80%）+ Karpathy-Guidelines（代码规范，仅 W1/W3）+ 失败回退

#### 规格驱动开发（可选工作流）

- Spec Kit 系列 — 可选的规格驱动开发工作流
  - `/speckit-constitution` — 项目宪章管理
  - `/speckit-specify` — 需求规格生成
  - `/speckit-plan` — 实施计划生成
  - `/speckit-tasks` — 任务分解
  - `/speckit-analyze` — 跨文档一致性分析
  - `/speckit-implement` — 实施执行
  - `/speckit-git-*` — Git 工作流集成
  - 独立使用：需求明确时，直接用 Spec Kit（specify → plan → tasks → implement）
  - 与 task-workflow 组合：W2 产出路线图后，每个功能点用 Spec Kit 执行交付

#### 代码品味与知识管理

- `/pensieve` — 项目知识库，可独立使用或与 task-workflow 集成
  - 数据目录：`<project>/.pensieve/`
    - `knowledge/` — IS（事实）：文件位置、模块边界、调用链缓存
    - `decisions/` — WANT（取舍）：架构决策记录
    - `maxims/` — MUST（硬规则）：工程准则
    - `pipelines/` — HOW（流程）：可复用工作流
    - `short-term/` — STAGING：待整理条目（7 天 TTL，到期提醒 refine）
  - 基础工具：
    - `/pensieve init` — 初始化项目知识库，播种默认模板
    - `/pensieve doctor` — 健康检查：扫描 frontmatter、链接、目录结构、图谱一致性
    - `/pensieve self-improve` — 沉淀可复用结论到 short-term/
    - `/pensieve refine` — 精炼知识库：triage（五问审阅）+ compress（压缩抽象，减少条目数）
    - `/pensieve upgrade` — 刷新 Pensieve skill 源码
    - `/pensieve migrate` — 结构迁移与遗留清理
  - 品味审查（6 Task Pipeline）：
    - 独立调用：`review 这段代码`、`审查 src/auth.py`、`检查 docs/api.md 的质量`
    - 自动触发：task-workflow W1/W3 阶段自动调用
    - 审查范围：代码、文档、模块（任意粒度）

#### 代码质量检查

- `/karpathy-guidelines` — 代码完成后的质量验证

## 输出格式

任务完成输出：

```
【完成总结】
改动：{简述}
验证：{结果}
回滚：{方法}
```

```
【实践论小结】
假设：{要验证的假设}
实践：{实施的方案}
结果：{观测到的数据}
复盘：{发现的问题}
下一步：{继续点或阻塞项}
```

## 安全规范

### 操作确认规则

- 只读操作 → 直接执行
- 写操作（含 Git） → 等待确认
- 禁止操作：`git push -f`（强制推送）

### Git 工作流规范

#### 代码提交

- 工具：ZCF 中文工程化技能库
  - `/zcf:git-commit` — 中文提交信息、智能拆分建议、Conventional Commits 格式
  - `/zcf:git-worktree` — Worktree 管理（目录：`../.zcf/项目名/`）
  - `/zcf:code-review` — 代码审查指南
  - `/zcf:refactoring` — 重构最佳实践

#### 分支管理规范

分支类型：
- `main` — 生产环境
- `test` — 测试环境
- `feature-<type>-<desc>-v<version>` — 功能开发
- `hotfix-<type>-<desc>-v<version>` — 紧急修复

命名规则：小写字母 + 连字符，包含版本号，描述简洁（3-5 词）

开发流程：`test → 功能分支 → test → main`

## MCP 使用规范

- MCP 工具预加载，以 `mcp__<server>__<tool>` 格式直接调用
- 优先使用 MCP 专用工具而非 Bash 命令
- 实际可用工具以系统提示注入为准

## 技术栈概览

### 主力业务
- 大模型应用开发：LLM 应用工程化（LangChain/LangGraph、RAG、Agent 编排、MCP 协议）
- 云原生运维：K8s 应用管理 + GitOps + Argo 蓝绿发布

### 常用语言
- Python：后端 API、大模型应用、数据处理、自动化脚本
- Go：微服务、CLI 工具、高并发 API、系统工具

### 基础设施
- 数据库：PostgreSQL/MySQL（主库）、Redis（缓存/队列）
- 容器编排：K8s/Helm、Argo CD/Argo Rollouts
- 监控日志：EFK + Prometheus/Grafana
