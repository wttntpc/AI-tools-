---
name: ai-tools-google-drive
description: 連接 Google Drive MCP — 讓 AI 讀取雲端硬碟檔案，包含 PDF、Docs、Sheets、簡報。說「連接 Google Drive」「讀取雲端硬碟」時載入。
---

# 連接 Google Drive（通用版）

> 注意：Google Calendar、Gmail、Google Drive 共用同一組 Google Cloud OAuth 憑證。
> 若已完成 07-google-calendar 或 08-gmail 的授權，可直接跳到步驟 3。

---

## 步驟

### 1. 建立 Google Cloud OAuth 憑證（首次設定）

1. 前往 https://console.cloud.google.com → 建立新專案（例如 `AI-MCP`）
2. 左側選單 → **APIs & Services → Library**，搜尋並啟用：
   - `Google Drive API`
   - `Google Docs API`（選用，讀取 Docs 文件）
   - `Google Sheets API`（選用，讀取試算表）
3. 左側選單 → **APIs & Services → Credentials**
4. 點 **Create Credentials → OAuth client ID**
   - Application type：**Desktop app**
   - 名稱：`AI-MCP`
5. 下載 `client_secret_xxx.json`，儲存到安全位置（不要放進 repo）

> 首次使用需設定 OAuth 同意畫面：左側選單 → OAuth consent screen → External → 填入 App name 和 Email

---

### 2. 儲存憑證路徑為環境變數

```powershell
# Windows
setx GOOGLE_CLIENT_SECRET_PATH "C:\你的路徑\client_secret_xxx.json"
```

```bash
# macOS / Linux
echo 'export GOOGLE_CLIENT_SECRET_PATH="$HOME/你的路徑/client_secret_xxx.json"' >> ~/.bashrc
source ~/.bashrc
```

---

### 3. 依 Agent 類型註冊 MCP

**Claude Code：**
```bash
claude mcp add google-drive -- npx -y @googleapis/mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "google-drive": {
      "type": "local",
      "command": ["npx", "-y", "@googleapis/mcp"],
      "env": {
        "GOOGLE_CLIENT_SECRET_PATH": "${GOOGLE_CLIENT_SECRET_PATH}"
      },
      "enabled": true
    }
  }
}
```

---

### 4. 驗證

重啟 Agent 後，請 AI：「列出我 Google Drive 根目錄的檔案」

首次使用會開啟瀏覽器完成 Google 帳號授權，選正確帳號並允許存取 Drive。

---

### 常用指令範例

| 任務 | 對 AI 說 |
|------|---------|
| 列出檔案 | 「列出我 Google Drive 最近修改的 10 個檔案」 |
| 讀取 PDF | 「讀取 Drive 裡的 xxx.pdf 並摘要重點」 |
| 讀取 Docs | 「讀取我的 Google Doc『研究計畫』並整理大綱」 |
| 搜尋檔案 | 「在 Drive 裡搜尋含有『EEG』的檔案」 |

---

⚠️ 安全規則：
- `client_secret_xxx.json` 絕對不可 commit 到 repo
- 加入 `.gitignore`：`client_secret*.json` 和 `token*.json`
- AI 只能讀取已授權的檔案，不會存取未授權範圍
