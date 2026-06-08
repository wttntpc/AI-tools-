---
name: ai-tools-google-calendar
description: 連接 Google Calendar MCP — 讓 AI 讀取、建立、修改行事曆事件。說「連接 Google Calendar」「設定行事曆」時載入。
---

# 連接 Google Calendar（通用版）

> 注意：Google Calendar、Gmail、Google Drive 共用同一組 Google Cloud OAuth 憑證。
> 若已完成 08-gmail 或 09-google-drive 的授權，可直接跳到步驟 3。

---

## 步驟

### 1. 建立 Google Cloud OAuth 憑證（首次設定）

1. 前往 https://console.cloud.google.com → 建立新專案（例如 `AI-MCP`）
2. 左側選單 → **APIs & Services → Library**，搜尋並啟用：
   - `Google Calendar API`
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
claude mcp add google-calendar -- npx -y @anthropic-labs/mcp-google-calendar
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "google-calendar": {
      "type": "local",
      "command": ["npx", "-y", "@anthropic-labs/mcp-google-calendar"],
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

重啟 Agent 後，請 AI：「列出我本週的 Google Calendar 行事曆事件」

首次使用會開啟瀏覽器完成 Google 帳號授權，選正確帳號並允許存取 Calendar。

---

⚠️ 安全規則：
- `client_secret_xxx.json` 絕對不可 commit 到 repo
- token 快取檔（`token.json`）也不可公開
- 加入 `.gitignore`：`client_secret*.json` 和 `token*.json`
