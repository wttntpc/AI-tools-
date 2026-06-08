---
name: ai-tools-google-drive
description: 連接 Google Drive MCP — 讓 AI 讀取雲端硬碟檔案，包含 PDF、Docs、Sheets、簡報。說「連接 Google Drive」「讀取雲端硬碟」時載入。
---

# 連接 Google Drive（通用版）

> Google Calendar、Gmail、Google Drive 共用同一組 Google Cloud OAuth 憑證。
> 若已完成 07-google-calendar 或 08-gmail 的授權，可直接跳到步驟 3。

---

## 資安原則（必讀）

| 規則 | 說明 |
|------|------|
| 憑證不入 repo | `client_secret*.json`、`token*.json` 絕對不 commit |
| 最小權限 | 只授予 `drive.readonly`，AI 只能讀取不能修改或刪除檔案 |
| 環境變數 | 憑證路徑存入系統環境變數，不寫進 Markdown |
| 共享檔案注意 | AI 可存取你 Drive 內的所有已授權檔案，包含他人共享給你的，請留意敏感內容 |
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
2. **APIs & Services → Library** → 搜尋並啟用：
   - `Google Drive API`
   - `Google Docs API`（選用）
   - `Google Sheets API`（選用）
3. **APIs & Services → OAuth consent screen**
   - User Type：**External**
   - 填入 App name（例如 `AI-MCP`）和你的 Email
   - Scopes：只加 `drive.readonly`
   - Test users：加入你自己的 Gmail
4. **APIs & Services → Credentials → Create Credentials → OAuth client ID**
   - Application type：**Desktop app**
   - 名稱：`AI-MCP-Drive`
5. 下載 `client_secret_xxx.json`，存到安全位置（建議 `C:\Users\你\.google\`）

### 2. 儲存憑證路徑為環境變數

```powershell
# Windows
setx GOOGLE_DRIVE_CREDENTIALS "C:\Users\你\.google\client_secret_xxx.json"
```

```bash
# macOS / Linux
echo 'export GOOGLE_DRIVE_CREDENTIALS="$HOME/.google/client_secret_xxx.json"' >> ~/.bashrc
source ~/.bashrc
```

### 3. 依 Agent 類型註冊 MCP

**Claude Code：**
```bash
claude mcp add google-drive -e GOOGLE_DRIVE_CREDENTIALS="$GOOGLE_DRIVE_CREDENTIALS" -- npx -y @modelcontextprotocol/server-gdrive
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "google-drive": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-gdrive"],
      "env": {
        "GOOGLE_DRIVE_CREDENTIALS": "${GOOGLE_DRIVE_CREDENTIALS}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  google-drive:
    command: npx
    args: ["-y", "@modelcontextprotocol/server-gdrive"]
    env:
      GOOGLE_DRIVE_CREDENTIALS: "${GOOGLE_DRIVE_CREDENTIALS}"
    enabled: true
```

### 4. 驗證

重啟 Agent 後，請 AI：「列出我 Google Drive 最近修改的 5 個檔案」

首次使用會開啟瀏覽器授權，選正確 Google 帳號並允許存取 Drive。

---

### 常用指令範例

| 任務 | 對 AI 說 |
|------|---------|
| 列出檔案 | 「列出 Drive 最近修改的 10 個檔案」 |
| 讀取 PDF 論文 | 「讀取 Drive 裡的 xxx.pdf 並摘要研究方法」 |
| 讀取 Docs | 「讀取 Google Doc『研究計畫』並整理大綱」 |
| 搜尋論文 | 「在 Drive 搜尋含有 EEG 的 PDF 檔案」 |
