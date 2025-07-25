# MMC Backend

MMC Backend 是基于 **NestJS** 构建的云原生后端，主要服务于英语学习平台。项目采用 AWS Lambda + API Gateway 的无服务器架构，并集成 OpenAI、OpenRouter 及 Upstash Vector 等服务，提供 AI 故事生成、RAG 知识库、音频与图像管理以及用户认证等功能。

## 目录结构概览
```
src/
├── main.ts                # 本地开发入口
├── lambda.ts              # 部署到 Lambda 的入口
├── modules/               # 业务模块
│   ├── ai/                # AI 故事与聊天接口
│   ├── auth/              # Cognito 认证
│   ├── audio/             # 音频管理
│   ├── image/             # 图像管理
│   ├── rag/               # RAG 服务
│   └── health/            # 健康检查
infrastructure/            # AWS CDK 基础设施代码
```

## 开发流程
1. **安装依赖**
   ```bash
   yarn install
   ```
2. **配置环境变量**
   ```bash
   cp environment-example.txt .env.development
   # 根据需要创建 .env.test 或 .env.production
   ```
   编辑 `.env.*` 文件，填入 `OPENROUTER_API_KEY`、`OPENAI_API_KEY` 等密钥。
3. **部署开发资源并启动本地服务**
   ```bash
   yarn run deploy:dev:script      # 构建 Lambda Layer 和函数并部署到 dev
   yarn start:dev                  # 使用 main.ts 启动本地服务器
   ```

## 部署到测试环境
测试环境可使用与开发环境相同的脚本，但需将 `NODE_ENV` 设为 `test` 并准备 `.env.test`：
```bash
cp environment-example.txt .env.test
NODE_ENV=test yarn run deploy:dev    # 使用默认脚本时请先执行 `yarn build:layer`
# 或直接运行下列脚本自动完成构建和部署
# NODE_ENV=test yarn run deploy:dev:script
```
部署完成后即可在测试环境验证功能。

## 部署到生产环境
生产环境使用独立的配置文件和脚本：
```bash
cp environment-example.txt .env.production
yarn run deploy:prod:script
```
该脚本会自动构建 Lambda 层和函数，并通过 AWS CDK 部署所有资源。

## 管理员账户设置
部署完成后，可通过 AWS CLI 将用户加入管理员组：
```bash
aws cognito-idp admin-add-user-to-group \
  --user-pool-id YOUR_USER_POOL_ID \
  --username USER_EMAIL \
  --group-name admin
```

更多环境变量说明参见 [docs/environment-setup.md](docs/environment-setup.md)。
