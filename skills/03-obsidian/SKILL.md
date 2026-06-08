---
name: ai-tools-obsidian
description: 連接 Obsidian MCP (MCPVault) — 適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 Obsidian」「設定 Obsidian」時載入。
---

# 連接 Obsidian（通用版）

## 步驟

### 1. 確認 vault 路徑並設為環境變數

請先找到 Obsidian vault 的實體路徑（內含 `.obsidian` 資料夾）。常見位置：
- `C:\Users\<你>\OneDrive\文件\Secondbrain`
- `C:\Users\<你>\Documents\<vault 名稱>`
- `G:\我的雲端硬碟\<vault 名稱>`

**存為環境變數（跨平台通用）：**
```powershell
# Windows
setx OBSIDIAN_VAULT_PATH "C:\Users\你\OneDrive\文件\Secondbrain"
```
```bash
# macOS / Linux
echo 'export OBSIDIAN_VAULT_PATH="$HOME/Documents/Secondbrain"' >> ~/.zshrc
source ~/.zshrc
```

> ⚠️ 請勿將 vault 實體路徑或敏感筆記內容 commit 到公開 repo。

### 2. 安裝 MCPVault
```powershell
# Windows
npm.cmd install -g @bitbonsai/mcpvault
where.exe mcpvault
```
```bash
# macOS / Linux
npm install -g @bitbonsai/mcpvault
which mcpvault
```
常見路徑（Windows）：`C:\Users\<你>\AppData\Roaming\npm\mcpvault.cmd`

### 3. 依 Agent 類型註冊 MCP

**Claude Code：**
```bash
claude mcp add obsidian -e OBSIDIAN_VAULT_PATH="$OBSIDIAN_VAULT_PATH" -- mcpvault "$OBSIDIAN_VAULT_PATH"
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "obsidian": {
      "type": "local",
      "command": ["mcpvault", "${OBSIDIAN_VAULT_PATH}"],
      "env": {
        "OBSIDIAN_VAULT_PATH": "${OBSIDIAN_VAULT_PATH}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
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
重啟 Agent 後，請 AI：「讀取 Obsidian vault 根目錄」，再測試建立並讀回一篇測試筆記。
