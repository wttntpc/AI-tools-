---
name: ai-tools-zotero
description: 連接 Zotero 本機 API — 讓 AI 直接查詢、引用你的文獻庫。適用所有 Agent（Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent）。說「連接 Zotero」「查詢文獻」時載入。
---

# 連接 Zotero（通用版）

> Zotero 使用本機 API（localhost:23119）直接連線，不需要 API Key、不需要帳號登入。
> 資料完全不離開本機，資安風險極低。

> ⚠️ 已知問題：`zotero-mcp` npm 套件目前與 Zotero 7.x 不相容，會連線失敗。
> 本指南改用「直接呼叫本機 API」方式，實測可正常讀取文獻庫。

---

## 資安原則

- 本機 API 只在 Zotero 桌面程式執行時有效，程式關閉即自動斷線
- 資料不經過網路，完全在本機運作
- AI 只能讀取，無法修改或刪除文獻
- 不把任何文獻資料 commit 到 repo

---

## 步驟

### 1. 確認 Zotero 已安裝並開啟

下載：https://www.zotero.org/download/
開啟 Zotero 桌面程式，確認正在執行。

---

### 2. 啟用本機 API（需自己操作）

> ⚠️ 此步驟需在 Zotero 桌面程式內手動完成，AI 無法代為操作。

1. 開啟 Zotero → `Edit` → `Preferences` → **進階**
2. 找到「**允許此電腦上的其他應用程式與 Zotero 通訊**」
3. 勾選啟用
4. 畫面顯示 `Available at http://localhost:23119/api/` 即表示成功

完成後告知 AI 繼續下一步。

---

### 3. 取得使用者 ID（AI 執行）

AI 執行以下指令，自動取得你的 Zotero 使用者 ID：

```powershell
# Windows
curl -s http://localhost:23119/api/users/0/items?limit=1
```

```bash
# macOS / Linux
curl -s "http://localhost:23119/api/users/0/items?limit=1"
```

回傳的 JSON 中，`library.id` 的數字即為你的使用者 ID（例如 `19177181`）。

---

### 4. 驗證連線

AI 使用以下 API 列出最近新增的 5 篇論文：

```
http://localhost:23119/api/users/{userId}/items?itemType=journalArticle&sort=dateAdded&direction=desc&limit=5
```

成功回傳論文清單即表示連線完成 ✅

---

## 常用 API 查詢範例

AI 可直接呼叫以下端點查詢文獻：

| 任務 | API 端點 |
|------|---------|
| 最近新增論文 | `/api/users/{id}/items?itemType=journalArticle&sort=dateAdded&direction=desc` |
| 搜尋關鍵字 | `/api/users/{id}/items?q=EEG&itemType=journalArticle` |
| 列出所有分類 | `/api/users/{id}/collections` |
| 取得分類內文獻 | `/api/users/{id}/collections/{collectionKey}/items` |

---

## 常用指令範例

| 任務 | 對 AI 說 |
|------|---------|
| 最近新增 | 「列出我 Zotero 最近新增的 5 篇論文」 |
| 搜尋文獻 | 「在 Zotero 找所有關於 EEG 和 exercise 的論文」 |
| 列出分類 | 「列出 Zotero 的所有分類」 |
| 引用格式 | 「這篇論文的 APA 引用格式是什麼？」 |
| 摘要整理 | 「整理 Zotero 中關於 HRV 的文獻重點」 |

---

⚠️ 注意事項：
- 使用前必須先開啟 Zotero 桌面程式
- AI 只能讀取，無法修改或刪除你的文獻
- 不把任何文獻資料 commit 到 repo
- 若日後 `zotero-mcp` 套件修復相容性，可改回 MCP 方式連線
