---
name: ai-tools-google-calendar
description: 連接 Google Calendar MCP — 讓 AI 讀取、建立、修改行事曆事件。適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 Google Calendar」「設定行事曆」時載入。
---

# 連接 Google Calendar（通用版）v2.0

> Google Calendar、Gmail、Google Drive 共用同一組 Google Cloud OAuth 憑證。
> 若已完成 08-gmail 或 09-google-drive 的授權，可直接跳到步驟 2。

## 步驟

### 1. 使用者在 Google Cloud Console 建立憑證（一次性）

AI 引導使用者：

1. 前往 https://console.cloud.google.com → 建立或選擇專案（例如 AI-MCP）
2. APIs & Services → Library → 啟用 Google Calendar API
3. OAuth consent screen → External → 填入 App name、Email → Scopes 只加 calendar.readonly，Test users 加入自己 Gmail
4. Credentials → Create Credentials → OAuth client ID → Desktop app → 下載憑證檔

AI 問：「請告訴我你把憑證檔案存在哪裡（完整路徑），我幫你完成後續設定。」

### 2. AI 直接設定環境變數

**Windows：**
```powershell
setx GOOGLE_CALENDAR_CREDENTIALS "使用者提供的路徑"
```

**macOS：**
```bash
echo 'export GOOGLE_CALENDAR_CREDENTIALS="使用者提供的路徑"' >> ~/.zshrc
source ~/.zshrc
```

### 3. AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add --env GOOGLE_CALENDAR_CREDENTIALS="使用者提供的路徑" google-calendar -- npx -y @modelcontextprotocol/server-google-calendar
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "google-calendar": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-google-calendar"],
      "environment": {
        "GOOGLE_CALENDAR_CREDENTIALS": "${GOOGLE_CALENDAR_CREDENTIALS}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent (~/.hermes/config.yaml)：**
```yaml
mcp_servers:
  google-calendar:
    command: npx
    args: ["-y", "@modelcontextprotocol/server-google-calendar"]
    env:
      GOOGLE_CALENDAR_CREDENTIALS: "${GOOGLE_CALENDAR_CREDENTIALS}"
    enabled: true
```

AI 同時在 .gitignore 加入：client_secret*.json、token*.json

### 4. 首次授權（瀏覽器 OAuth）

AI 告知使用者：「請重啟 Agent，首次使用時瀏覽器會自動開啟，請選正確帳號並允許存取 Calendar，完成後告訴我。」

### 5. 驗證

AI 列出本週 Google Calendar 事件確認連線成功。
