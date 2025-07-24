# n8n AI + Ngrok + MCP 一体化解决方案

这个项目是一个完整的自托管AI工作流和远程访问解决方案，它融合了三个强大的功能：
1. 本地AI开发环境（包含n8n、Ollama、Qdrant和PostgreSQL）
2. 通过Ngrok提供的安全隧道进行远程访问
3. 集成n8n-mcp，提供MCP（Model Context Protocol）服务器功能

## 功能特点

✅ **自托管n8n** - 低代码平台，提供400多种集成和高级AI组件

✅ **Ollama** - 跨平台LLM平台，可安装和运行最新的本地大语言模型

✅ **Qdrant** - 开源高性能向量数据库，具有全面的API

✅ **PostgreSQL** - 数据工程领域的重型数据库，能够安全处理大量数据

✅ **Ngrok** - 提供安全隧道，使您可以从任何地方访问您的n8n实例

✅ **n8n-mcp** - 集成MCP服务器，为AI应用提供标准化的工具调用接口

## 前提条件

在开始之前，请确保您已安装以下软件：
- Docker: [获取Docker](https://docs.docker.com/get-docker/)
- Docker Compose: [安装Docker Compose](https://docs.docker.com/compose/install/)

## 设置步骤

1. **克隆仓库**

   将此仓库克隆到您的本地机器：
   ```bash
   git clone https://github.com/DuckRaiser/n8n-ai-ngrok.git
   cd n8n-ai-ngrok
   ```

2. **配置环境变量**

   复制示例环境文件并进行编辑：
   ```bash
   cp .env.example .env
   ```
   
   在`.env`文件中设置以下必要的值：
   - PostgreSQL用户名、密码和数据库名
   - n8n加密密钥和JWT密钥
   - Ngrok认证令牌（从[Ngrok仪表板](https://ngrok.com/)获取）
   - Ngrok永久域名（如果您有）
   - 时区设置

3. **Ngrok配置**

   如果您有Ngrok永久域名，请确保更新`ngrok.yml`文件中的域名设置。

4. **启动服务**

   ### 对于Nvidia GPU用户：
   ```bash
   docker compose --profile gpu-nvidia up -d
   ```

   ### 对于AMD GPU用户（Linux）：
   ```bash
   docker compose --profile gpu-amd up -d
   ```

   ### 对于MacOS / Apple Silicon用户：
   如果您想在本地运行Ollama以获得更快的推理速度，请安装[Ollama](https://ollama.com/)并按照以下步骤操作：
   ```bash
   docker compose up -d
   ```
   然后修改n8n服务中的`OLLAMA_HOST`环境变量为`host.docker.internal:11434`

   ### 对于其他用户（CPU模式）：
   ```bash
   docker compose --profile cpu up -d
   ```

5. **访问n8n**

   启动服务后，您可以通过以下方式访问您的n8n实例：
   - 本地访问: http://localhost:5678
   - 远程访问: 通过您在Ngrok设置中配置的URL

## 使用方法

1. 打开n8n web界面并设置您的账户
2. 探索预装的AI工作流或创建您自己的工作流
3. 使用Ollama运行本地LLM模型
4. 利用Qdrant作为向量数据库进行高效的相似性搜索
5. 使用n8n-mcp作为MCP服务器，为AI应用提供标准化工具调用接口
6. 随时随地通过Ngrok隧道访问您的n8n实例

## MCP服务器使用指南

新增的n8n-mcp容器提供了Model Context Protocol (MCP)服务器功能，使您的AI应用能够以标准化方式调用n8n工作流和工具。

### 什么是MCP？
MCP（Model Context Protocol）是一个开放协议，它标准化了应用程序如何向大型语言模型提供上下文和工具。通过MCP，AI助手可以安全地访问和操作本地工具、数据源和工作流。

### 如何使用n8n-mcp

1. **启动MCP服务器**：当您运行`docker compose up -d`时，n8n-mcp容器会自动启动
2. **配置MCP客户端**：在支持MCP的AI应用中配置连接信息：
   - 服务器地址：`http://n8n-mcp:3000`（容器内部）或 `http://localhost:3000`（宿主机）
   - 协议：MCP over HTTP

3. **创建MCP工具**：在n8n中创建可以被MCP调用的工作流，这些工作流将作为工具提供给AI应用

### 示例应用场景
- **智能客服**：AI助手通过MCP调用n8n工作流来处理客户查询
- **数据分析**：AI应用通过MCP触发n8n数据管道进行复杂分析
- **自动化任务**：AI助手通过MCP执行n8n工作流完成各种自动化任务

## 访问PostgreSQL数据库

本项目的PostgreSQL数据库已经配置为可以通过本地端口5432访问。您可以使用以下几种方式连接和管理数据库：

### 1. 使用pgAdmin

[pgAdmin](https://www.pgadmin.org/) 是PostgreSQL的流行图形界面管理工具：

1. 下载并安装 [pgAdmin](https://www.pgadmin.org/download/)
2. 启动pgAdmin并添加新服务器
3. 填写连接信息：
   - 主机: localhost
   - 端口: 5432
   - 数据库: n8n (或您在.env文件中设置的POSTGRES_DB值)
   - 用户名: root (或您在.env文件中设置的POSTGRES_USER值)
   - 密码: password (或您在.env文件中设置的POSTGRES_PASSWORD值)

### 2. 使用DBeaver

[DBeaver](https://dbeaver.io/) 是一个通用的数据库管理工具，支持PostgreSQL：

1. 下载并安装 [DBeaver Community Edition](https://dbeaver.io/download/)
2. 启动DBeaver并创建新连接
3. 选择PostgreSQL并填写连接信息(与pgAdmin相同)

### 3. 使用命令行工具

如果您更喜欢命令行界面，可以使用psql：

```bash
# 安装PostgreSQL客户端工具
# 对于Windows，需要安装完整的PostgreSQL或仅客户端工具
# 对于Linux: sudo apt-get install postgresql-client
# 对于MacOS: brew install libpq

# 连接到数据库
psql -h localhost -p 5432 -U root -d n8n
# 根据您的.env配置相应调整用户名和数据库名
```

### 4. 使用Docker直接连接

您也可以通过Docker容器直接连接到PostgreSQL：

```bash
docker exec -it postgres psql -U root -d n8n
# 根据您的.env配置相应调整用户名和数据库名
```

## 停止服务

要停止所有服务，请运行：
```bash
docker compose down
```
## Upgrading

* ### For Nvidia GPU setups:

```bash
docker compose --profile gpu-nvidia pull
docker compose create && docker compose --profile gpu-nvidia up
```

* ### For Mac / Apple Silicon users

```
docker compose pull
docker compose create && docker compose up
```

* ### For Non-GPU setups:

```bash
docker compose --profile cpu pull
docker compose create && docker compose --profile cpu up
```

## 👓 Recommended reading

n8n is full of useful content for getting started quickly with its AI concepts
and nodes. If you run into an issue, go to [support](#support).

- [AI agents for developers: from theory to practice with n8n](https://blog.n8n.io/ai-agents/)
- [Tutorial: Build an AI workflow in n8n](https://docs.n8n.io/advanced-ai/intro-tutorial/)
- [Langchain Concepts in n8n](https://docs.n8n.io/advanced-ai/langchain/langchain-n8n/)
- [Demonstration of key differences between agents and chains](https://docs.n8n.io/advanced-ai/examples/agent-chain-comparison/)
- [What are vector databases?](https://docs.n8n.io/advanced-ai/examples/understand-vector-databases/)


## 数据持久化

所有数据都通过Docker卷进行持久化存储：
- n8n_storage: 存储n8n工作流和配置
- postgres_storage: 存储PostgreSQL数据
- ollama_storage: 存储Ollama模型
- qdrant_storage: 存储Qdrant向量数据

## 服务架构

本项目包含以下服务：

| 服务名称 | 功能描述 | 端口 |
|---------|----------|------|
| **n8n** | 低代码AI工作流平台 | 5678 |
| **postgres** | PostgreSQL数据库 | 5432 |
| **qdrant** | 向量数据库 | 6333 |
| **ollama** | 本地LLM平台 | 11434 |
| **ngrok** | 安全隧道服务 | - |
| **n8n-mcp** | MCP服务器 | 3000 |

## 故障排除

### 常见问题

1. **容器启动失败**
   - 检查端口是否被占用：`lsof -i :5678`
   - 检查环境变量是否正确配置

2. **MCP连接问题**
   - 确保n8n-mcp容器已启动：`docker compose ps | grep n8n-mcp`
   - 检查容器日志：`docker compose logs n8n-mcp`

3. **数据库连接失败**
   - 确认PostgreSQL容器健康状态：`docker compose ps postgres`
   - 检查数据库凭据是否正确

### 查看日志

```bash
# 查看所有服务日志
docker compose logs

# 查看特定服务日志
docker compose logs n8n
docker compose logs n8n-mcp
docker compose logs postgres
```

## 贡献

欢迎贡献！请随时提交问题或拉取请求。

## 许可证

请参阅原始项目的许可证：
- [n8n-ngrok](https://github.com/joffcom/n8n-ngrok)
- [self-hosted-ai-starter-kit](https://github.com/n8n-io/self-hosted-ai-starter-kit)
