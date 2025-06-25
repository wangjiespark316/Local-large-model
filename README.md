# L🐳 本地部署 Ollama + Open WebUI + Qwen1.8B 中文大模型笔记（适用于 Mac）
📁 一、项目目录结构
你创建的项目目录为：
/Users/wangjie/Documents/ollama-docker
目录结构如下：
ollama-docker/
├── docker-compose.yml           # Docker 启动配置文件
├── ollama/                      # 存放大模型数据（可备份）
└── webui-data/                  # 聊天记录、用户配置等数据
📦 二、docker-compose.yml 内容
version: "3.9"
services:
open-webui:
image: ghcr.io/open-webui/open-webui:ollama


container_name: open-webui
ports:
"3000:8080"
volumes:
./ollama:/root/.ollama
./webui-data:/app/backend/data
environment:
OLLAMA_MODELS=/root/.ollama/models
OLLAMA_KEEP_ALIVE=5
OPENAI_API_KEY=${OPENAI_API_KEY}
OPENAI_API_BASE_URL=${OPENAI_API_BASE_URL}
restart: always

声明.env文件位置，docker compose会自动加载同目录的.env文件
新建 .env 文件（和 docker-compose.yml 同目录）
OPENAI_API_KEY=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
OPENAI_API_BASE_URL=https://openrouter.ai/api/v1
🚀 三、启动步骤
✅ 1. 创建项目目录并进入
mkdir -p ~/Documents/ollama-docker
cd ~/Documents/ollama-docker
✅ 2. 将上面的 docker-compose.yml 保存到此目录
✅ 3. 启动服务
docker compose up -d
启动成功后，浏览器访问：http://localhost:3000
🤖 四、下载中文大模型 Qwen:1.8B
使用 curl 命令下载模型：
curl -X POST http://localhost:11434/api/pull \
  -H "Content-Type: application/json" \
  -d '{"name": "qwen:1.8b"}'
下载成功后模型会存储在本地目录：./ollama/models/
查看已下载模型：curl http://localhost:11434/api/tags
🧠 五、在 WebUI 中使用模型
访问地址：http://localhost:3000
首次打开界面，选择模型：qwen:1.8b
即可开始中文对话！
🛠 六、常用命令
功能	命令
启动服务	docker compose up -d
停止服务	docker compose down
查看模型列表	curl http://localhost:11434/api/tags
删除模型	curl -X DELETE http://localhost:11434/api/delete -d '{"name":"qwen:1.8b"}'
查看日志	docker logs open-webui -f
💡 七、推荐中文模型（可选）
使用 curl 命令下载，替换 name 字段即可：
✅ Yi-6B-Chat（yi:6b-chat）：中文理解强、逻辑好
✅ DeepSeek Coder（deepseek-coder:1.3b）：优秀的代码生成能力
✅ Breeze 7B（breeze:7b）：全能中文任务表现好
✅ MiniCPM 2B（minicpm:2b）：轻量、速度快
✅ 八、Metal 加速说明（Mac M 系列自动启用）
可通过命令确认是否启用 Metal 加速：
docker logs open-webui | grep metal
如果日志中包含 ▶ using metal，说明加速已启用 ✅
📝 九、小贴士
👉 模型首次加载较慢（几十秒），后续基本秒开；
👉 建议一次只加载一个大模型，避免内存压力过大；
👉 所有数据模型都挂载在 ./ollama 和 ./webui-data，方便备份、迁移；
👉 Open WebUI 已支持中文提示词、界面中文、上下文管理等功能。
