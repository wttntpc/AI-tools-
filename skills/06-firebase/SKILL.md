---
name: ai-tools-firebase
description: 連接 Firebase MCP — 適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 Firebase」「設定 Firebase」時載入。
---

# 連接 Firebase（通用版）v2.0

## 步驟

### 1. AI 先執行版本檢查

**Windows：**
```powershell
npx.cmd -y firebase-tools@latest --version
```

**macOS / Linux：**
```bash
npx -y firebase-tools@latest --version
```

### 2. 瀏覽器 OAuth（使用者操作）

AI 告知使用者：「我要執行 Firebase 登入，瀏覽器會自動開啟，請完成 Google 授權後告訴我。」

**Windows：**
```powershell
npx.cmd -y firebase-tools@latest login
```

**macOS / Linux：**
```bash
npx -y firebase-tools@latest login
```

### 3. AI 驗證登入

**Windows：**
```powershell
npx.cmd -y firebase-tools@latest projects:list
```

**macOS / Linux：**
```bash
npx -y firebase-tools@latest projects:list
```

### 4. AI 依 Agent 類型執行 MCP 註冊

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
      "command": "npx",
      "args": ["-y", "firebase-tools@latest", "mcp"],
      "enabled": true
    }
  }
}
```

**Hermes Agent (~/.hermes/config.yaml)：**
```yaml
mcp_servers:
  firebase:
    command: npx
    args: ["-y", "firebase-tools@latest", "mcp"]
    enabled: true
```

### 5. 驗證

AI 告知使用者：「請重啟 Agent，完成後告訴我，我列出你的 Firebase 專案確認連線。」
