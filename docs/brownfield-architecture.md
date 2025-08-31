# Brownfield Architecture Document: 多使用者部落格 (HPB Blog Project)

## 介紹

本文件旨在捕捉專案的**當前狀態**，並概述新功能「**多使用者部落格**」的架構。由於專案目前缺少應用程式程式碼，本文件將作為基礎架構規劃，並提供給參與實施的 AI 代理作為參考。

### 文件範圍

專注於與「**建立多使用者部落格平台**」相關的領域。

### 變更日誌

| 日期       | 版本 | 描述                 | 作者      |
| ---------- | ---- | -------------------- | --------- |
| 2025-08-29 | 1.0  | 初始 brownfield 分析 | Architect |

## 快速參考 - 關鍵檔案與入口點 (依新結構)

由於我們是從頭開始，這些是**建議的**關鍵檔案。

### 理解系統的關鍵檔案

- **後端主入口**: `hbp-blog-backend/main.go`
- **前端主入口**: `hpb-blog-frontend/app.vue`
- **專案配置**: `.env` (用於環境變數), `hbp-blog-backend/config/config.yaml` (後端內部配置，待建立)
- **Docker 編排**: `docker-compose.yml`
- **後端 Dockerfile**: `hbp-blog-backend/Dockerfile`
- **前端 Dockerfile**: `hpb-blog-frontend/Dockerfile`

## 高階架構

系統將是一個經典的三層應用程式，並根據要求完全容器化。

1.  **前端 (Frontend):** 一個 Nuxt.js 應用程式，用於提供使用者介面。
2.  **後端 (Backend):** 一個 Go (Gin) 應用程式，提供 RESTful API 供前端使用。
3.  **資料庫 (Database):** 一個 PostgreSQL 資料庫，用於資料持久化。

所有三個服務都將使用 Docker Compose 進行編排。

### 實際技術棧 (建議)

| 類別   | 技術       | 版本/框架      | 備註                             |
| ------ | ---------- | -------------- | -------------------------------- |
| 前端   | JavaScript | Nuxt.js        | 用於伺服器端渲染和靜態站點生成。 |
| 後端   | Go         | Gin            | 用於建構高效能的 REST API。      |
| 資料庫 | SQL        | PostgreSQL     | 可靠且強大的關聯式資料庫。       |
| 容器化 | Docker     | Docker Compose | 用於管理多容器應用程式。         |

### 程式碼倉庫結構現況 (依新結構)

- **類型**: Monorepo (一個主倉庫，包含前端和後端各自的 Git 子模組或獨立子倉庫)。
- **套件管理器**: 後端使用 Go Modules，前端使用 `npm` 或 `yarn`。
- **值得注意**: 程式碼將組織成 `hbp-blog-frontend` 目錄用於 Nuxt.js 前端，`hbp-blog-backend` 目錄用於 Go 後端。每個子專案都有自己的 Git 倉庫。

## 原始碼樹與模組組織 (依新結構)

### 專案結構 (建議)

```text
hpb-blog-project/
├── .git/                     # 主專案(AI協作)的 Git Repo
├── .gitignore                # 主專案的 Git 忽略清單
├── docs/                     # 存放 AI 產生的文件
│   ├── prd/                  # 產品需求文件 (Product Requirement Document)
│   └── conversations/        # 與 AI 的對話紀錄
│
├── hbp-blog-backend/         # 後端專案 (Go + Gin)
│   ├── .git/                 # "後端" 自己的 Git Repo
│   ├── .gitignore
│   ├── main.go               # Go 程式碼主入口 (整合路由、服務、資料庫連接等)
│   ├── go.mod
│   ├── go.sum
│   ├── Dockerfile            # 告訴 Docker 如何打包後端專案
│   └── (其他後端目錄，例如 internal/, pkg/, config/ 等，待建立)
│
├── hpb-blog-frontend/        # 前端專案 (Nuxt.js)
│   ├── .git/                 # "前端" 自己的 Git Repo
│   ├── .gitignore
│   ├── app.vue               # Nuxt 專案主入口 (根元件)
│   ├── package.json
│   ├── package-lock.json / yarn.lock
│   ├── nuxt.config.ts        # Nuxt 配置檔案
│   ├── pages/                # Nuxt 頁面 (路由)
│   │   ├── index.vue         # 首頁
│   │   ├── admin/            # 管理員頁面 (登入、新文章、編輯)
│   │   └── posts/            # 文章列表和詳細頁面
│   ├── components/           # 可重用 Vue 元件
│   ├── assets/               # 靜態資源 (圖片、字體等)
│   ├── public/               # 公共靜態檔案
│   ├── plugins/              # Nuxt 插件
│   ├── composables/          # 可組合函數 (類似 React Hooks)
│   └── Dockerfile            # 告訴 Docker 如何打包前端專案
│
├── docker-compose.yml        # 整個專案的啟動總指揮
└── .env                      # 存放環境變數與密碼
```

### 關鍵模組及其目的 (依新結構及 Nuxt 慣例)

- **`hbp-blog-backend/main.go`**: 初始化並啟動 Gin Web 伺服器，負責註冊所有路由、服務和資料庫連接。
- **`hbp-blog-backend/(internal/transport/http)` (待建立)**: 定義 API 路由，處理 HTTP 請求，並管理中間件（如日誌和認證）。
- **`hbp-blog-backend/(pkg/user)` (待建立)**: 處理使用者註冊、登入和認證邏輯。
- **`hbp-blog-backend/(pkg/post)` (待建立)**: 管理部落格文章的建立、讀取、更新和刪除。
- **`hpb-blog-frontend/app.vue`**: Nuxt 應用程式的根元件，負責整體佈局和頁面渲染。
- **`hpb-blog-frontend/pages`**: 定義部落格的路由，包括首頁、文章詳細資訊和管理員部分。
- **`hpb-blog-frontend/components`**: 包含可重用的 UI 元素，如表單、按鈕和佈局元件。
- **`docker-compose.yml`**: 編排後端、前端和資料庫服務的啟動、停止和網路連接。
- **`.env`**: 儲存專案的環境變數，例如資料庫連接字串、API 金鑰等。

### 使用者介面路由與畫面 (Nuxt.js)

- **公開頁面**:
  - `/` (首頁): 顯示所有已發布文章的列表。
  - `/posts/{id}`: 顯示單篇文章的詳細內容。
- **管理頁面 (需認證)**:
  - `/admin/login`: 管理員/作者登入頁面。
  - `/admin/dashboard`: 顯示所有文章列表，並提供編輯/刪除選項。
  - `/admin/posts/new`: 創建新文章的表單。
  - `/admin/posts/edit/{id}`: 編輯現有文章的表單。

## 資料模型與 API

### 資料模型 (建議)

- **使用者模型**: `users` 表 (id, username, password_hash, created_at)
- **文章模型**: `posts` 表 (id, user_id, title, content, created_at, updated_at, status (enum: published, hidden, draft))

這些將在後端 `hbp-blog-backend` 中定義為 Go struct，並可能在前端 `hpb-blog-frontend` 中定義為 TypeScript 介面。

### API 規格 (建議的 REST 端點)

- `POST /api/login`: 驗證使用者並返回 token。
- `GET /api/posts`: 獲取文章列表 (未認證使用者獲取所有已發布文章；已認證作者獲取自己的文章列表)。
- `GET /api/posts/{id}`: 根據 ID 獲取單篇文章。
- `POST /api/posts`: 建立新文章 (需要認證)。
- `PUT /api/posts/{id}`: 更新現有文章 (需要認證)。
- `DELETE /api/posts/{id}`: 刪除文章 (永久刪除，需要認證)。
- `PUT /api/posts/{id}/status`: 更新文章狀態 (例如隱藏/顯示) (需要認證)。

## 開發與部署

### 本地開發設置

1.  在 `hpb-blog-project/` 根目錄下建立 `docker-compose.yml`。
2.  在 `hbp-blog-backend/` 和 `hpb-blog-frontend/` 目錄中分別建立 `Dockerfile`。
3.  在 `hpb-blog-project/` 根目錄下建立 `.env` 檔案，用於存放環境變數。
4.  運行 `docker-compose up --build` 以啟動所有服務。

### 建構與部署流程

- **建構指令**: `docker-compose up --build` 命令將會建構容器映像檔。
- **部署**: 部署流程將涉及將映像檔推送到容器註冊表，並將其部署到雲端環境。具體細節待定。
- **環境**: `development`、`testing` 和 `production` 的配置將透過不同的配置檔案或傳遞給 Docker 的環境變數來管理。

## 測試現況

### 當前測試覆蓋率

- **單元測試**: 0%
- **整合測試**: 0%
- **端到端測試**: 0%

### 運行測試 (建議)

```bash
# 後端 (在 hbp-blog-backend/ 目錄中執行)
go test ./...

# 前端 (在 hpb-blog-frontend/ 目錄中執行)
npm test  # 或 yarn test
```

## 風險評估與緩解

### 技術風險

- **主要風險**: 所提議架構的錯誤實施，因為這是一個從零開始的新構建。

### 緩解策略

- **開發方法**: 將開發分解為小的、連續的任務。
- **驗證**: 每個任務將包括設置和基本實施，以驗證每個步驟的架構。

## 增強影響分析

由於這是一個從零開始的新功能，整個建議的結構就是其影響。

### 需要建立的檔案

- 所有在建議的專案結構中列出的檔案都需要被建立。

### 需要新增的檔案/模組

- 所有模組（`user`、`post`、`http transport` 等）以及 Nuxt.js 和 Gin 的核心檔案都是新的。

### 整合考量

- 主要的整合是 Nuxt.js 前端和 Go 後端之間的 REST API 通訊。
- 安全的通訊和狀態管理（認證 token）將是關鍵。
- 需要考慮後端資料庫連接和前端 API 請求的配置。
