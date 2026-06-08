---
name: ai-tools-gmail
description: 連接 Gmail MCP — 讓 AI 讀取、搜尋、起草、發送郵件。說「連接 Gmail」「設定郵件」時載入。
---

# 連接 Gmail（通用版）

> Google Calendar、Gmail、Google Drive 共用同一組 Google Cloud OAuth 憑證。
> 若已完成 07-google-calendar 或 09-google-drive 的授權，可直接跳到步驟 3。

---

## 資安原則（必讀）

| 規則 | 說明 |
|------|------|
| 憑證不入 repo | `client_secret*.json`、`token*.json` 絕對不 commit |
| 最小權限 | 建議只授予 `gmail.readonly` + `gmail.send`，不授予 `gmail.modify`（避免 AI 誤刪郵件） |
| 發信前確認 | AI 起草後必須由你確認，才能真正發送 |
| 環境變數 | 憑證路徑存入系統環境變數，不寫進 Markdown |
| 定期檢查 | 每季到 https://myaccount.google.com/permissions 確認授權應用程式 |
| 撤銷方式 | 如懷疑洩漏，立即到 Google Cloud Console 刪除 OAuth client |

`.gitignore` 請加入：
```
client_secret*.json
token*.json
*.credentials.json
```

---

## 步驟

### 1. 建立 Google Cloud OAuth 憑證（首次設定）

1. 前往 https://console.cloud.google.com → 建立或選擇專案（例如 `AI-MCP`）
2. **APIs & Services → Library** → 搜尋並啟用 `Gmail API`
3. **APIs & Services → OAuth consent screen**
   - User Type：**External**
   - 填入 App name（例如 `AI-MCP`）和你的 Email
   - Scopes：只加 `gmail.readonly` 和 `gmail.send`
   - Test users：加入你自己的 Gmail
4. **APIs & Services → Credentials → Create Credentials → OAuth client ID**
   - Application type：**Desktop app**
   - 名稱：`AI-MCP-Gmail`
5. 下載 `client_secret_xxx.json`，存到安全位置（建議 `C:\Users\你\.google\`）

### 2. 儲存憑證路徑為環境變數

```powershell
# Windows
setx GOOGLE_GMAIL_CREDENTIALS "C:\Users\你\.google\client_secret_xxx.json"
```

```bash
# macOS / Linux
echo 'export GOOGLE_GMAIL_CREDENTIALS="$HOME/.google/client_secret_xxx.json"' >> ~/.bashrc
source ~/.bashrc
```

### 3. 依 Agent 類型註冊 MCP

**Claude Code：**
```bash
claude mcp add gmail -e GOOGLE_GMAIL_CREDENTIALS="$GOOGLE_GMAIL_CREDENTIALS" -- npx -y @gongrzhe/server-gmail-autoauth-mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "gmail": {
      "type": "local",
      "command": ["npx", "-y", "@gongrzhe/server-gmail-autoauth-mcp"],
      "env": {
        "GOOGLE_GMAIL_CREDENTIALS": "${GOOGLE_GMAIL_CREDENTIALS}"
      },
      "enabled": true
    }
  }
}
```

### 4. 驗證

重啟 Agent 後，請 AI：「讀取我最新的 5 封 Gmail 郵件標題」

首次使用會開啟瀏覽器授權，選正確 Google 帳號並允許存取 Gmail。

---

### 常用指令範例

| 任務 | 對 AI 說 |
|------|---------|
| 讀取最新郵件 | 「讀取我最新 10 封信的標題和寄件人」 |
| 搜尋郵件 | 「找出所有來自 xxx@gmail.com 的郵件」 |
| 整理未讀 | 「列出所有未讀郵件並摘要重點」 |
| 起草回覆 | 「幫我起草一封回覆，語氣正式，說明下週無法出席」 |

⚠️ AI 起草郵件後，請你仔細確認內容再發送。AI 不應在未確認的情況下自動寄出郵件。
