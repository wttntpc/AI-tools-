---
name: ai-tools-google-calendar
description: 連接 Google Calendar MCP — 讓 AI 讀取、建立、修改行事曆事件。說「連接 Google Calendar」「設定行事曆」時載入。
---

# 連接 Google Calendar（通用版）

> Google Calendar、Gmail、Google Drive 共用同一組 Google Cloud OAuth 憑證。
> 若已完成 08-gmail 或 09-google-drive 的授權，可直接跳到步驟 3。

---

## 資安原則（必讀）

| 規則 | 說明 |
|------|------|
| 憑證不入 repo | `client_secret*.json`、`token*.json` 絕對不 commit |
| 最小權限 | OAuth 同意畫面只勾選 Calendar 所需範圍，不授予多餘權限 |
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
2. **APIs & Services → Library** → 搜尋並啟用 `Google Calendar API`
3. **APIs & Services → OAuth consent screen**
   - User Type：**External**（個人帳號選此項）
   - 填入 App name（例如 `AI-MCP`）和你的 Email
   - Scopes：只加 `calendar.readonly`（唯讀）或 `calendar`（可寫入）
   - Test users：加入你自己的 Gmail
4. **APIs & Services → Credentials → Create Credentials → OAuth client ID**
   - Application type：**Desktop app**
   - 名稱：`AI-MCP-Calendar`
5. 下載 `client_secret_xxx.json`，存到安全位置（建議 `C:\Users\你\.google\`）

### 2. 儲存憑證路徑為環境變數

```powershell
# Windows
setx GOOGLE_CALENDAR_CREDENTIALS "C:\Users\你\.google\client_secret_xxx.json"
```

```bash
# macOS / Linux
echo 'export GOOGLE_CALENDAR_CREDENTIALS="$HOME/.google/client_secret_xxx.json"' >> ~/.bashrc
source ~/.bashrc
```

### 3. 依 Agent 類型註冊 MCP

**Claude Code：**
```bash
claude mcp add google-calendar -e GOOGLE_CALENDAR_CREDENTIALS="$GOOGLE_CALENDAR_CREDENTIALS" -- npx -y @anthropic-labs/mcp-google-calendar
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "google-calendar": {
      "type": "local",
      "command": ["npx", "-y", "@anthropic-labs/mcp-google-calendar"],
      "env": {
        "GOOGLE_CALENDAR_CREDENTIALS": "${GOOGLE_CALENDAR_CREDENTIALS}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  google-calendar:
    command: npx
    args: ["-y", "@anthropic-labs/mcp-google-calendar"]
    env:
      GOOGLE_CALENDAR_CREDENTIALS: "${GOOGLE_CALENDAR_CREDENTIALS}"
    enabled: true
```

### 4. 驗證

重啟 Agent 後，請 AI：「列出我本週的 Google Calendar 事件」

首次使用會開啟瀏覽器授權，選正確 Google 帳號並允許存取 Calendar。

---

### 常用指令範例

| 任務 | 對 AI 說 |
|------|---------|
| 查看行程 | 「列出我本週的行程」 |
| 建立事件 | 「幫我在週三下午 2 點建立實驗室會議，持續 1 小時」 |
| 查詢空檔 | 「我這週哪些時段沒有行程？」 |
| 提醒研究時程 | 「列出所有標記為研究的行事曆事件」 |
