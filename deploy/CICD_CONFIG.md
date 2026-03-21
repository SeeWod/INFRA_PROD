# INFRA_PROD CI/CD 变量与密钥说明

## 目标
集中管理基础设施（Nginx 网关、Mongo、Redis）部署所需的变量与密钥，采用“技术_02”规范：
- deploy/ 目录内存放 docker-compose.yml 与 CICD_CONFIG.md
- 运行时挂载 ../nginx 与 ../static 作为 Nginx 配置与静态根目录

## 变量分类（vars 或 secrets）

基础设施（vars）
- INFRA_NETWORK_NAME=apps
- NGINX_HTTP_PORT=80
- NGINX_HTTPS_PORT=443
- MONGO_PORT=27017
- REDIS_PORT=6379

镜像（vars 或 secrets）
- MONGO_IMAGE (vars 或 secrets) 示例：mongo:7

远程登录（secrets，若使用 CI 直接部署）
- PROD_SSH_HOST=your.server.example.com
- PROD_SSH_USER=deployer
- PROD_SSH_KEY=PEM私钥内容
- INFRA_DEPLOY_PATH=/srv
- INFRA_NGINX_RELOAD_CMD="sudo nginx -t && sudo nginx -s reload"
 
应用工作流联动（vars 或 secrets）
- ENABLE_INFRA_RESTART=true

## 目录挂载约定
- ../nginx/nginx.conf → /etc/nginx/nginx.conf
- ../nginx/conf.d → /etc/nginx/conf.d
- ../static → /srv/static
- ../data/mongo → Mongo 数据目录
- ../data/redis → Redis 数据目录

## 说明
- 该目录可在后续迁出至独立仓库维护；迁出后需要在 CD 中调整 INFRA_DEPLOY_PATH 指向新路径
- Nginx conf.d 中的应用规则文件由应用仓库提供（deploy/nginx/<app>.conf），CD 阶段拷贝到此处并重载 Nginx
