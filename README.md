# BK-Lite 架构图集

基于 [TencentBlueKing/bk-lite](https://github.com/TencentBlueKing/bk-lite) 上游 master（同步至 `65fc3d6759`，2026-10-28）绘制的完整架构理解图，共 13 张，由 [Archify](https://github.com/tt-a1i/archify) 生成：每个 HTML 是独立交互图（主题切换 / 缩放 / 搜索 / 引导视图），同名 JSON 为可复现规格。

---

# BK-Lite 架构图索引

本目录收录 BK-Lite 的完整架构理解图。建议按“总览 → 请求 → 数据与执行 → 运行与治理”的顺序阅读。

## 阅读路线

### A. 总览与入口

| 图 | 类型 | 说明 |
|---|---|---|
| [项目总览](bklite-architecture.html) | architecture | Web、Django、Celery、NATS、CMDB、Agents 与外部基础设施总览 |
| [Web 请求时序](bklite-request-sequence.html) | sequence | 浏览器 → Next.js → API Proxy → Django → 业务模块 → 数据层 |

### B. 核心业务闭环

| 图 | 类型 | 说明 |
|---|---|---|
| [CMDB 采集闭环](bklite-cmdb-collection.html) | workflow | 采集任务、NATS、Stargazer/Collector、外部资源与图谱写入 |
| [作业与补丁执行](bklite-job-execution.html) | workflow | Job/Patch API、执行记录、Celery、NATS、Executor 与回调 |
| [APM 数据流](bklite-apm-dataflow.html) | dataflow | OTLP、Collector、NATS、VictoriaTraces、APM API、策略与告警 |

### C. 运行机制与源码导航

| 图 | 类型 | 说明 |
|---|---|---|
| [Server 启动依赖](bklite-server-startup.html) | workflow | 环境加载、迁移、batch_init、Supervisor、Django、Celery、NATS Listener |
| [NATS 消息拓扑](bklite-nats-topology.html) | dataflow | Core NATS RPC、JetStream、采集 Agent、执行 Agent 与回调状态 |
| [Celery 生命周期](bklite-celery-lifecycle.html) | lifecycle | 创建、排队、执行、成功、重试、失败、取消 |
| [Agent 能力分层](bklite-agent-capabilities.html) | architecture | Webhookd、Edge Bundle、Stargazer、Collector、Executor 的职责边界 |
| [业务模块导航](bklite-module-navigation.html) | architecture | `server/apps` 下 CMDB、Monitor、Alerts、Job、Patch、APM、Log、OpsPilot 等模块 |

### D. 数据、权限与部署

| 图 | 类型 | 说明 |
|---|---|---|
| [权限与多租户边界](bklite-permission-boundary.html) | architecture | Session、OpenAPI Gateway、组织范围、RBAC、资源过滤与审计 |
| [CMDB 双存储](bklite-cmdb-storage.html) | architecture | 关系数据库与 FalkorDB/Neo4j 的职责分工及查询汇合 |
| [部署拓扑](bklite-deployment-topology.html) | architecture | Web/Server 容器、RabbitMQ、Redis、NATS、Agent、Collector 与目标资源 |

## 如何使用

每张 HTML 都是独立交互式图，支持主题切换、缩放、搜索、节点聚焦和引导视图。对应的 `.json` 是可复现规格，便于继续修订或导出。

## 事实边界

- 项目核心是一个模块化 Django 单体，不是多个独立 HTTP 微服务。
- 前端主要路径是 `Browser → Next.js API Proxy → Django /api/v1`。
- Celery 默认 Broker 是 RabbitMQ；Redis 主要承担缓存和状态用途，除非部署配置显式覆盖。
- NATS 是 Server 与 Agents 的核心同步/异步通信总线。
- CMDB 同时使用 Django 关系模型与 FalkorDB/Neo4j 图数据库。

## 校验说明

- 每张图使用 Archify `showcase` 质量配置。
- 交付前要求 9/9 静态检查通过、0 composition errors、0 warnings。
- 浏览器证据使用 Archify `visual-check`，覆盖 1440×900、1600×1000、1920×1080、2048×1320，以及浅色/深色主题。
- `visual-check` 的 `visualReview` 仍需人工或图像评审，不将自动化检查冒充人工审美评审。

## 来源

图基于上游仓库 master 同步后的本地检出版本制作与更新：

`65fc3d6759`（2026-10-28，同步自 TencentBlueKing/bk-lite）

远端仓库：<https://github.com/TencentBlueKing/bk-lite>

同步带来的变化已反映在图中：

- 业务模块导航图：新增 `Module Link`（跨模块同步任务，CMDB→监控同步），可观测分组补入 `RUM`
- 部署拓扑图：新增 `RUM 数据面`（`deploy/rum`，Faro 上报 + NATS 控制链路）
- 上游将 `workflow_orchestration` 更名为 `workflow`，未单独成图的模块归属不受影响
