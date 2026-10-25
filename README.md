# Medical Agent

基于 FastAPI、LangChain / LangGraph 的医疗 Agent 后端实现，包含 Redis 会话检查点、Milvus 长期记忆工具、PostgreSQL 医疗数据模型及 Neo4j 知识图谱初始化脚本。

## 项目结构

- `src/agents/`：Agent 构建、对话调用与记忆工具。
- `src/core/`、`src/infra/`：配置、日志、数据库及存储客户端。
- `src/main.py`：FastAPI 应用、生命周期和健康检查接口。
- `src/modules/medical/`：医疗数据模型。
- `alembic/`：数据库迁移。
- `scripts/`：PostgreSQL 数据导入和 Neo4j 图谱初始化。
- `data/raw/medical.json`：上游附带的 JSONL 医疗知识数据，供导入脚本使用。
- `test/`：Agent 集成测试代码，依赖外部服务与模型。
- `docker-compose.yml`：基础设施服务定义。

## 配置与启动

依赖清单和平台兼容问题见下方“当前实现状态”。以下是项目使用的配置与启动方式，不代表已经完成全链路部署验证。

```bash
conda env create -f environment.yml
conda activate tiangong-medical
cp .env.example .env
```

在本地 `.env` 中填写模型密钥及各服务连接配置。Compose 沿用上游开发环境配置，使用 PostgreSQL、Redis Stack、MinIO、Milvus 和 Neo4j；启动前检查本机端口与已有服务是否冲突，并自行替换开发用默认密码。应用配置与 Compose 配置需要保持一致。

```bash
docker compose up -d
alembic upgrade head
uvicorn src.main:app --host 127.0.0.1 --port 8080
```

- API 文档：`http://127.0.0.1:8080/docs`
- 健康接口：`GET /health`，仅返回应用状态。
- 依赖检查：`GET /health/deps`，检查各存储依赖。

按需执行 PostgreSQL 数据导入：

```bash
python scripts/init_postgres.py
```

`python scripts/init_neo4j.py` 会先删除目标 Neo4j 库中的全部节点，再导入图谱，只能对明确可重建的目标库执行。




