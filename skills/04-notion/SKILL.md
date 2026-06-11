---
name: notion-mcp
description: 連接 Notion MCP — 讓 AI 直接讀寫 Notion 頁面與資料庫。適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 Notion」「Notion MCP」時載入。
---

# 連接 Notion MCP（v2.0）

## 步驟

### 1. 使用者取得 Integration Token（一次性）

AI 引導使用者：

> 「請前往 https://www.notion.so/profile/integrations → New integration → 填入名稱（例如 AI-MCP）→ 選擇工作區 → Submit → 複製 Internal Integration Token（ntn_ 開頭），貼給我。」

### 2. AI 取得 Token 後直接設定環境變數

**Windows：**
```powershell
setx NOTION_API_KEY "使用者提供的token"
```

**macOS：**
```bash
echo 'export NOTION_API_KEY="使用者提供的token"' >> ~/.zshrc
source ~/.zshrc
```

**Linux：**
```bash
echo 'export NOTION_API_KEY="使用者提供的token"' >> ~/.bashrc
source ~/.bashrc
```

### 3. AI 依 Agent 類型執行 MCP 註冊

**Claude Code：**
```bash
claude mcp add --env NOTION_API_KEY="使用者提供的token" notion -- npx -y @notionhq/notion-mcp-server
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "notion": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@notionhq/notion-mcp-server"],
      "environment": {
        "NOTION_API_KEY": "${NOTION_API_KEY}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent (~/.hermes/config.yaml)：**
```yaml
mcp_servers:
  notion:
    command: npx
    args: ["-y", "@notionhq/notion-mcp-server"]
    env:
      NOTION_API_KEY: "${NOTION_API_KEY}"
    enabled: true
```

### 4. 驗證

AI 告知使用者：「請重啟 Agent，並在 Notion 頁面右上角 Share → 把 Integration 加入，AI 才能存取該頁面。重啟後告訴我。」

## 常見問題

| 問題 | 解法 |
|------|------|
| AI 連線成功但讀不到頁面 | 在 Notion 頁面右上角 Share → 把 Integration 加入 |
| Token 格式錯誤 | 確認開頭為 ntn_，不要包含引號 |
