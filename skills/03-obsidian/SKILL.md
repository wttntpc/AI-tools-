---
name: ai-tools-obsidian
description: 連接 Obsidian MCP (MCPVault) — 適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 Obsidian」「設定 Obsidian」時載入。
---

# 連接 Obsidian（通用版）v2.0

## 步驟

### 1. AI 詢問 vault 路徑

AI 問：「請提供你的 Obsidian vault 路徑（含 .obsidian 資料夾的那層），我幫你設定。」

若使用者不確定，AI 執行搜尋（Windows）：
```powershell
Get-ChildItem C:\Users -Recurse -Filter ".obsidian" -Directory 2>$null | Select-Object -First 5 FullName
```

### 2. AI 直接安裝並設定環境變數

**Windows：**
```powershell
npm.cmd install -g @bitbonsai/mcpvault
setx OBSIDIAN_VAULT_PATH "使用者提供的路徑"
```

**macOS / Linux：**
```bash
npm install -g @bitbonsai/mcpvault
echo 'export OBSIDIAN_VAULT_PATH="使用者提供的路徑"' >> ~/.zshrc
source ~/.zshrc
```

### 3. AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add --env OBSIDIAN_VAULT_PATH="使用者提供的路徑" obsidian -- mcpvault "使用者提供的路徑"
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "obsidian": {
      "type": "local",
      "command": "mcpvault",
      "args": ["${OBSIDIAN_VAULT_PATH}"],
      "environment": {
        "OBSIDIAN_VAULT_PATH": "${OBSIDIAN_VAULT_PATH}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent (~/.hermes/config.yaml)：**
```yaml
mcp_servers:
  obsidian:
    command: mcpvault
    args: ["${OBSIDIAN_VAULT_PATH}"]
    env:
      OBSIDIAN_VAULT_PATH: "${OBSIDIAN_VAULT_PATH}"
    enabled: true
```

### 4. 驗證

AI 告知使用者：「請重啟 Agent，完成後告訴我，我來驗證連線。」
