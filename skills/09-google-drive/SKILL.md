---
name: ai-tools-google-drive
description: 連接 Google Drive MCP — 讓 AI 讀取雲端硬碟檔案。適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 Google Drive」「讀取雲端硬碟」時載入。
---

# 連接 Google Drive（通用版）v2.0

> Google Calendar、Gmail、Google Drive 共用同一組 Google Cloud OAuth 憑證。
> 若已完成 07-google-calendar 或 08-gmail 的授權，可直接跳到步驟 2。
> 只授予 drive.readonly，AI 只能讀取不能修改或刪除。

## 步驟

### 1. 使用者在 Google Cloud Console 建立憑證（一次性）

AI 引導使用者：

1. 前往 https://console.cloud.google.com → 同上專案
2. APIs & Services → Library → 啟用 Google Drive API
3. OAuth consent screen → External → Scopes 只加 drive.readonly，Test users 加入自己 Gmail
4. Credentials → Create Credentials → OAuth client ID → Desktop app → 下載憑證

AI 問：「請告訴我憑證檔案的完整路徑。」

### 2. AI 直接設定環境變數

**Windows：**
```powershell
setx GOOGLE_DRIVE_CREDENTIALS "使用者提供的路徑"
```

**macOS：**
```bash
echo 'export GOOGLE_DRIVE_CREDENTIALS="使用者提供的路徑"' >> ~/.zshrc
source ~/.zshrc
```

### 3. AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add --env GOOGLE_DRIVE_CREDENTIALS="使用者提供的路徑" google-drive -- npx -y @modelcontextprotocol/server-gdrive
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "google-drive": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-gdrive"],
      "environment": {
        "GOOGLE_DRIVE_CREDENTIALS": "${GOOGLE_DRIVE_CREDENTIALS}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent (~/.hermes/config.yaml)：**
```yaml
mcp_servers:
  google-drive:
    command: npx
    args: ["-y", "@modelcontextprotocol/server-gdrive"]
    env:
      GOOGLE_DRIVE_CREDENTIALS: "${GOOGLE_DRIVE_CREDENTIALS}"
    enabled: true
```

### 4. 首次授權（瀏覽器 OAuth）

AI 告知使用者：「請重啟 Agent，首次使用時瀏覽器會自動開啟，請選正確帳號並允許存取 Drive（唯讀），完成後告訴我。」

### 5. 驗證

AI 列出 Drive 最近修改的 5 個檔案確認連線成功。

## 常用指令範例

| 任務 | 對 AI 說 |
|------|---------|
| 列出檔案 | 「列出 Drive 最近修改的 10 個檔案」 |
| 讀取 PDF | 「讀取 Drive 裡的 xxx.pdf 並摘要研究方法」 |
| 搜尋論文 | 「在 Drive 搜尋含有 EEG 的 PDF 檔案」 |
