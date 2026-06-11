---
name: ai-tools-hackmd
description: 連接 HackMD MCP — 讓 AI 直接讀取、建立、編輯 HackMD 筆記與共筆。適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 HackMD」「讀取 HackMD 筆記」時載入。
---

# 連接 HackMD（通用版）v2.0

## 步驟

### 1. 使用者取得 API Token（一次性）

AI 引導使用者：

> 「請前往 HackMD → 右上角頭像 → Settings → API → Create API Token → 名稱填 AI-MCP，勾選 Read + Write → 複製 token（只出現一次），貼給我。」

### 2. AI 取得 Token 後直接設定環境變數

**Windows：**
```powershell
setx HACKMD_API_TOKEN "使用者提供的token"
```

**macOS：**
```bash
echo 'export HACKMD_API_TOKEN="使用者提供的token"' >> ~/.zshrc
source ~/.zshrc
```

**Linux：**
```bash
echo 'export HACKMD_API_TOKEN="使用者提供的token"' >> ~/.bashrc
source ~/.bashrc
```

### 3. AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add -e HACKMD_API_TOKEN="使用者提供的token" hackmd -- npx -y hackmd-mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "hackmd": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "hackmd-mcp"],
      "environment": {
        "HACKMD_API_TOKEN": "${HACKMD_API_TOKEN}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent (~/.hermes/config.yaml)：**
```yaml
mcp_servers:
  hackmd:
    command: npx
    args: ["-y", "hackmd-mcp"]
    env:
      HACKMD_API_TOKEN: "${HACKMD_API_TOKEN}"
    enabled: true
```

### 4. 驗證

AI 告知使用者：「請重啟 Agent，完成後告訴我，我列出你的 HackMD 筆記確認連線。」

## 資安

- API token 由 AI 存入環境變數，不寫進 repo
- Token 洩漏：立即到 HackMD Settings → API 撤銷，重新建立後貼給 AI
