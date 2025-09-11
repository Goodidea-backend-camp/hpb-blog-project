# hpb-blog-project

## 開發環境啟動方式

> ⚠️ 請注意僅限在開發環境使用

### 首次使用
1. Clone 以下專案到本地
    - hpb-blog-project
    - hpb-blog-frontend
    - hpb-blog-backend

2. 將 `hpb-blog-backend/` 和 `hpb-blog-frontend/` 放在 `hpb-blog-project/` 底下
3. 切換當前目錄到 `hpb-blog-project/` 
4. 執行 `cp .env.dev .env`
5. 執行 `docker compose -f docker-compose.yml -f docker-compose.dev.yml up --build`

### 日常啟用

如果 Dockerfile 沒有變更，只是要啟動環境，可以省略 `--build`：
```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml up
```

### 停止並移除容器
```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml down -v
```
