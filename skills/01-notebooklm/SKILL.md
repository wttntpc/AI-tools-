---
name: ai-tools-notebooklm
description: 連接 NotebookLM MCP — 適用 Claude Code、AntiGravity、Codex、OpenCode。說「連接 NotebookLM」「設定 NotebookLM」時載入。
---

# 連接 NotebookLM（通用版）

## 步驟

### 1. 安裝
```powershell
# Windows / macOS / Linux
uv tool install notebooklm-mcp-cli
nlm --version
```

### 2. 登入（瀏覽器 OAuth）
```powershell
nlm login
```
瀏覽器開啟後，請選正確的 Google 帳號完成授權。

若登入到錯誤帳號：
```powershell
nlm logout
nlm login
```

### 3. 驗證
```powershell
nlm doctor
nlm list
```
若 Windows 顯示 CP950 編碼錯誤：
```powershell
$env:PYTHONIOENCODING = "utf-8"
nlm doctor
```

### 4. 依 Agent 類型註冊 MCP

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
      "command": ["nlm", "mcp"],
      "enabled": true
    }
  }
}
```

### 5. 驗證連線
重啟 Agent 後，請 AI：「列出我的 NotebookLM 筆記本」

⚠️ 不要複製 cookie/token，不把筆記本清單 commit 到 repo。
