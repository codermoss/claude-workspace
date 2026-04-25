## 核心工程原则

### 唯物辩证法

核心原则：
- 实践是检验真理的唯一标准
- 实践 → 认识 → 再实践 → 再认识，螺旋上升
- 数据驱动决策，基于事实而非假设

### 行为准则

- 对话开始: 每次对话开始时,主动调用 `/using-superpowers` 扫描匹配技能
- 先读后写，理解现有代码再修改
- 优先使用专用工具（Read/Write/Edit/rg）而非系统命令
- 路径处理：正确处理含空格路径，使用跨平台兼容格式
- 代码注释：与项目现有语言保持一致
- 所有代码变更完成后，调用 `/karpathy-guidelines` 检查

## 工作模式

### 核心工具库

#### 问题诊断与架构优化

- `/task-workflow` — 生产故障、技术问题、架构决策
  - 工作流编排器 + 质量门实现（学习 Qiushi + Pensieve 设计）
  - 包含 3 个标准工作流（通用执行、新项目启动、品味提升）
  - 集成 Karpathy-Guidelines 作为代码质量门

#### 规格驱动开发（可选工作流）

- Spec Kit 系列 — GitHub 官方规格驱动开发工具集（与 task-workflow 平级）
  - `/speckit-constitution` — 项目宪章管理
  - `/speckit-specify` — 需求规格生成
  - `/speckit-plan` — 实施计划生成
  - `/speckit-tasks` — 任务分解
  - `/speckit-analyze` — 跨文档一致性分析
  - `/speckit-implement` — 实施执行
  - `/speckit-git-*` — Git 工作流集成

#### 代码品味与知识管理

- `/pensieve` — 项目知识库、架构决策、编码标准、代码品味提升
  - 查询已有决策：`/pensieve doctor`
  - 捕获新洞察：`/pensieve self-improve`
  - 品味审查：`/pensieve review`（可独立调用或由 task-workflow 调度）

#### 代码质量检查

- `/karpathy-guidelines` — 代码完成后的质量验证

### 输出格式

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

## 技术栈适配

### Python（工具语言）

- 用途：后端 API 开发、大模型应用开发、数据处理、自动化脚本
- 场景：FastAPI、LangChain 应用、数据 ETL、运维工具
- 风格：高内聚低耦合、小模块组合、类型注解、异步优先（IO 密集型）、配置分离
- 验证指令：`pytest -v --cov` 或 `ruff check . && mypy src/`

### Go（工具语言）

- 用途：微服务开发、CLI 工具、高并发 API、系统工具
- 场景：gRPC 服务、K8s Operator、性能敏感组件
- 风格：小包设计、接口优先、显式错误处理、并发安全
- 验证指令：`go test -race -cover ./...` 或 `golangci-lint run`

### 大模型应用开发（主力业务）

- 定位：LLM 应用工程化，不涉及模型训练和底层算法
- 场景：RAG 系统、Agent 编排、API 服务、工具调用、多模态应用
- 技术栈：LangChain/LlamaIndex、LangGraph、Faiss/ChromaDB/Qdrant、MCP 协议
- 风格：工程化优先、Prompt Caching 必须启用、异步并发、错误重试机制
- 成本控制：Prompt Caching 启用率 > 80%、批处理优先、向量检索 Top-K ≤ 10、简单任务用 Haiku
- 验证指令：`pytest tests/ai/ -v --cov`

### 云原生运维（主力业务）

- 定位：K8s 应用管理 + GitOps + 应用生命周期 + Argo 蓝绿发布
- 场景：应用部署、版本管理、蓝绿发布、配置管理、故障排查
- 技术栈：K8s/Helm、Argo CD/Argo Rollouts、GitLab CI、EFK + Prometheus/Grafana
- 风格：GitOps 优先、蓝绿发布（Argo Rollouts）、资源 limits 必须设置、日志结构化
- 验证指令：`helm template` 或 `kubectl apply --dry-run=server`

### 数据库（基础设施）

- 场景：数据持久化、查询优化、缓存设计
- 技术栈：PostgreSQL/MySQL（主库）、Redis（缓存/队列）
- 风格：索引优先、连接池配置、事务隔离级别明确、慢查询监控
- 验证指令：`psql -c "EXPLAIN ANALYZE <query>"` 或 `redis-cli INFO stats`
