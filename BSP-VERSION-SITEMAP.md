# BSP v3 — 版本 Sitemap

> 記錄所有已完成嘅 BSP v3 Cloudflare Pages 部署版本
> 最後更新：2026-04-29

---

## 📋 版本列表（由舊到新）

### 2026-04-19 — SEO 優化版本

| 版本 | URL | 內容 |
|------|-----|------|
| b0299e72 | https://b0299e72.bsp-v3.pages.dev | SEO 優化：Meta Tags、OG Image、JSON-LD、sitemap.xml、robots.txt |
| d8399411 | https://d8399411.bsp-v3.pages.dev/BSP/users/school | School Users 管理頁面 |
| e542d595 | https://e542d595.bsp-v3.pages.dev/BSP/users/personal | Personal Users 管理頁面（Edit/Suspend/Delete 按鈕修復） |

### 2026-04-20 — Payment + Dashboard

| 版本 | URL | 內容 |
|------|-----|------|
| 3dae3b2b | https://3dae3b2b.bsp-v3.pages.dev/payment | Payment Page 修復（移除雙 header、全部英文） |
| efaf159e | https://efaf159e.bsp-v3.pages.dev/BSP/dashboard | BSP Dashboard 新版面 |
| 87e88ed2 | https://87e88ed2.bsp-v3.pages.dev/payment | 舊版 Payment page（已廢） |

### 2026-04-21 — Staff + Agent 頁面

| 版本 | URL | 內容 |
|------|-----|------|
| 162c2f01 | https://162c2f01.bsp-v3.pages.dev/BSP/users/bspstaff | BSP Staff Users 管理頁面（overflow 修復） |
| f9afcdf5 | https://f9afcdf5.bsp-v3.pages.dev/BSP/users/agent | Agent Users 管理頁面 |

### 2026-04-22

| 版本 | URL | 內容 |
|------|-----|------|
| f45690f8 | https://f45690f8.bsp-v3.pages.dev | 最新版本 |

### 2026-04-28 — Schools 目錄 + Split View

| 版本 | URL | 內容 |
|------|-----|------|
| 36fd103f | https://36fd103f.bsp-v3.pages.dev | Schools 目錄頁面 |
| 60224647 | https://60224647.bsp-v3.pages.dev | Schools 目錄（3個版本：Grid / Map / Split）<br>Build: 81 routes |

### 2026-04-28 — Hour 30

| 版本 | URL | 狀態 |
|------|-----|------|
| **3fa9b5a9** ⬅️ | https://3fa9b5a9.bsp-v3.pages.dev | **目前 rollback 版本** |

---

## 🔄 當前狀態

- **目前運作版本：** `3fa9b5a9.bsp-v3.pages.dev`（已 rollback）
- **最新代碼版本：** `60224647.bsp-v3.pages.dev`（Schools Split View）
- **Workspace 程式碼：** `/home/ubuntu/.openclaw/workspace/data/projects/bsp-v3`

---

## 📊 待做工作（從 `development.md`）

- [ ] /faq 頁面
- [ ] /terms 頁面
- [ ] /privacy 頁面
- [ ] BSP Dashboard 審核申請頁面 (`/BSP/applications`)
- [ ] User categories 頁面
- [ ] Annual fee settings 頁面
- [ ] Stripe API Key 對接
- [ ] Email notification 系統

---

## 📌 已知問題

- `/school/harrow-international` 及其他 school profile 頁面未被 pre-render
  - 原因：CSR 動態 rendering，crawler 搵唔到 links
  - 解決：需要在 `nuxt.config.ts` 加入 route rules 強制 SSR

---

*此檔案由 SecrexAI 自動維護*
