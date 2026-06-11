---
name: ai-tools-gmail
description: 連接 Gmail MCP — 讓 AI 讀取、搜尋、起草、發送郵件。適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 Gmail」「設定郵件」時載入。
---

# 連接 Gmail（通用版）v2.0

> Google Calendar、Gmail、Google Drive 共用同一組 Google Cloud OAuth 憑證。
> 若已完成 07-google-calendar 或 09-google-drive 的授權，可直接跳到步驟 2。

## AI 操作守則（強制）

1. 發信前必須確認：完整顯示草稿，等使用者說「確認發送」才執行
2. 禁止自動修改：不得自動封存、標記、移動任何郵件
3. 讀取後不留副作用：不改變已讀/未讀狀態

## 步驟

### 1. 使用者在 Google Cloud Console 建立憑證（一次性）

AI 引導使用者：

1. 前往 https://console.cloud.google.com → 同上專案
2. APIs & Services → Library → 啟用 Gmail API
3. OAuth consent screen → External → 填入 App name、Email、Test users（加自己 Gmail）
4. Credentials → Create Credentials → OAuth client ID → Desktop app → 下載憑證

AI 問：「請告訴我憑證檔案的完整路徑。」

### 2. AI 直接複製憑證並設定環境變數

**Windows：**
```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.gmail-mcp"
Copy-Item "使用者提供的路徑" "$env:USERPROFILE\.gmail-mcp\gcp-oauth.keys.json"
setx GOOGLE_GMAIL_CREDENTIALS "使用者提供的路徑"
```

**macOS / Linux：**
```bash
mkdir -p ~/.gmail-mcp
cp "使用者提供的路徑" ~/.gmail-mcp/gcp-oauth.keys.json
echo 'export GOOGLE_GMAIL_CREDENTIALS="使用者提供的路徑"' >> ~/.zshrc
source ~/.zshrc
```

### 3. AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add --env GOOGLE_GMAIL_CREDENTIALS="使用者提供的路徑" gmail -- npx -y @gongrzhe/server-gmail-autoauth-mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "gmail": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@gongrzhe/server-gmail-autoauth-mcp"],
      "environment": {
        "GOOGLE_GMAIL_CREDENTIALS": "${GOOGLE_GMAIL_CREDENTIALS}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent (~/.hermes/config.yaml)：**
```yaml
mcp_servers:
  gmail:
    command: npx
    args: ["-y", "@gongrzhe/server-gmail-autoauth-mcp"]
    env:
      GOOGLE_GMAIL_CREDENTIALS: "${GOOGLE_GMAIL_CREDENTIALS}"
    enabled: true
```

### 4. 首次授權（瀏覽器 OAuth）

AI 告知使用者：「請重啟 Agent，首次使用時瀏覽器會自動開啟，請選正確帳號並允許存取 Gmail，完成後告訴我。」

### 5. 驗證

AI 讀取最新 5 封郵件標題確認連線成功。
