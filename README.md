# 🐳 本地部署 Ollama + Open WebUI + Qwen1.8B 中文大模型笔记（适用于 Mac）

## ✅ 自动生成目录说明
只要在 `docker-compose.yml` 中定义好路径，以下两个目录将**自动生成**：

- `ollama/`：存放大模型数据（可备份）
- `webui-data/`：聊天记录、用户配置等数据

------

## 📁 一、项目目录结构
你创建的项目目录为：
`/Users/wangjie/Documents/ollama-docker`

结构如下：

```
ollama-docker/
├── docker-compose.yml         # Docker 启动配置文件
├── ollama/                    # 存放大模型数据（可备份）
└── webui-data/                # 聊天记录、用户配置等数据
```

------

## 📦 二、docker-compose.yml 内容

```yaml
version: "3.9"
services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:ollama
    container_name: open-webui
    ports:
      - "3000:8080"
    volumes:
      - ./ollama:/root/.ollama
      - ./webui-data:/app/backend/data
    environment:
      OLLAMA_MODELS: /root/.ollama/models
      OLLAMA_KEEP_ALIVE: 5
      OPENAI_API_KEY: ${OPENAI_API_KEY}
      OPENAI_API_BASE_URL: ${OPENAI_API_BASE_URL}
    restart: always
```

### 声明 `.env` 文件位置

Docker Compose 会自动加载**同目录**的 `.env` 文件。

创建 `.env` 文件（与 `docker-compose.yml` 同目录）：

```
OPENAI_API_KEY=sk-xxx（此处省略真实密钥）
OPENAI_API_BASE_URL=https://openrouter.ai/api/v1
```

------

## 🚀 三、启动步骤

✅ **1. 创建项目目录并进入**

```bash
mkdir -p ~/Documents/ollama-docker
cd ~/Documents/ollama-docker
```

✅ **2. 保存 `docker-compose.yml` 到该目录**  
✅ **3. 启动服务**

```bash
docker compose up -d
```

浏览器访问：[http://localhost:3000](http://localhost:3000)

------

## 🤖 四、下载中文大模型 Qwen:1.8B

使用 curl 命令下载模型：

```bash
curl -X POST http://localhost:11434/api/pull \
  -H "Content-Type: application/json" \
  -d '{"name": "qwen:1.8b"}'
```

- 模型存储路径：`./ollama/models/`

查看已下载模型：

```bash
curl http://localhost:11434/api/tags
```

------

## 🧠 五、在 WebUI 中使用模型

- 访问：[http://localhost:3000](http://localhost:3000)
- 首次进入页面，选择模型 `qwen:1.8b` 即可开始中文对话！

------

## 🛠 六、常用命令

| 功能         | 命令 |
|--------------|------|
| 启动服务     | `docker compose up -d` |
| 停止服务     | `docker compose down` |
| 查看模型列表 | `curl http://localhost:11434/api/tags` |
| 删除模型     | `curl -X DELETE http://localhost:11434/api/delete -d '{"name":"qwen:1.8b"}'` |
| 查看日志     | `docker logs open-webui -f` |

------

## 💡 七、推荐中文模型（可选）

使用 `curl` 下载时替换 `name` 字段即可：

- ✅ **Yi-6B-Chat（yi:6b-chat）**：中文理解强、逻辑好  
- ✅ **DeepSeek Coder（deepseek-coder:1.3b）**：优秀的代码生成能力  
- ✅ **Breeze 7B（breeze:7b）**：全能中文任务表现好  
- ✅ **MiniCPM 2B（minicpm:2b）**：轻量、速度快  

------

## ✅ 八、Metal 加速说明（Mac M 系列自动启用）

确认是否启用了 Metal 加速：

```bash
docker logs open-webui | grep metal
```

日志中包含：

```
▶ using metal
```

说明加速已启用 ✅

------

## 📝 九、小贴士

- 👉 **首次加载模型较慢**（可能需等待几十秒），后续基本秒开；
- 👉 **建议一次只加载一个大模型**，避免占用大量内存；
- 👉 所有数据与模型都挂载在 `./ollama` 和 `./webui-data`，**方便备份与迁移**；
- 👉 Open WebUI 已支持：
  - 中文提示词输入
  - 中文界面显示
  - 上下文管理
