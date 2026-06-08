---
name: ai-tools-firebase
description: 連接 Firebase MCP — 適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 Firebase」「設定 Firebase」時載入。
---

# 連接 Firebase（通用版）

## 步驟

### 1. 安裝與登入
```powershell
# Windows（使用 npx.cmd 避免 PowerShell 執行原則問題）
npx.cmd -y firebase-tools@latest --version
npx.cmd -y firebase-tools@latest login
npx.cmd -y firebase-tools@latest projects:list
```
```bash
# macOS / Linux
npx -y firebase-tools@latest --version
npx -y firebase-tools@latest login
npx -y firebase-tools@latest projects:list
```

> `firebase login` 需要瀏覽器互動授權。若在 AI 對話中卡住，請開獨立終端機視窗手動執行登入，再回來讓 AI 驗證。

### 2. 依 Agent 類型註冊 MCP

**Claude Code：**
```bash
claude mcp add firebase -- npx -y firebase-tools@latest mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "firebase": {
      "type": "local",
      "command": ["npx.cmd", "-y", "firebase-tools@latest", "mcp"],
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  firebase:
    command: npx        # Windows 請改為 npx.cmd
    args: ["-y", "firebase-tools@latest", "mcp"]
    enabled: true
```

### 3. 驗證
重啟 Agent 後，請 AI：「列出我的 Firebase 專案」

⚠️ 安全規則：
- Firebase Admin SDK 憑證絕對不可公開
- `.firebaserc` 若含私人專案 ID，公開前請確認是否適合
