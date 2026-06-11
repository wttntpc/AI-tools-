---
name: ai-tools-notebooklm
description: 連接 NotebookLM MCP — 適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 NotebookLM」「設定 NotebookLM」時載入。
---

# 連接 NotebookLM（通用版）v2.0

## 步驟

### 1. AI 直接執行安裝

```bash
uv tool install notebooklm-mcp-cli
nlm --version
```

### 2. 瀏覽器 OAuth（使用者操作）

AI 執行後告知使用者：「瀏覽器會自動開啟，請選擇正確的 Google 帳號完成授權，完成後告訴我。」

```bash
nlm login
```

授權完成後 AI 執行驗證：
```bash
nlm doctor
```

若 Windows 顯示 CP950 編碼錯誤，AI 先執行：
```powershell
$env:PYTHONIOENCODING = "utf-8"
nlm doctor
```

若登入到錯誤帳號，AI 執行：
```bash
nlm logout
nlm login
```

### 3. AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add notebooklm -- nlm mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "notebooklm": {
      "type": "local",
      "command": "nlm",
      "args": ["mcp"],
      "enabled": true
    }
  }
}
```

**Hermes Agent (~/.hermes/config.yaml)：**
```yaml
mcp_servers:
  notebooklm:
    command: nlm
    args: ["mcp"]
    enabled: true
```

### 4. 驗證

AI 告知使用者：「請重啟 Agent，完成後告訴我。」重啟後 AI 列出 NotebookLM 筆記本確認連線成功。
