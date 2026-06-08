---
name: notion-mcp
description: 連接 Notion MCP — 讓 AI 直接讀寫 Notion 頁面與資料庫。說「連接 Notion」「Notion MCP」時載入。
---

# 連接 Notion MCP

## 事前準備

1. 前往 https://www.notion.so/profile/integrations → New integration
2. 填入名稱（例如 `AI-MCP`），選擇工作區，點 Submit
3. 複製 **Internal Integration Token**（`ntn_...` 開頭）

> 安全規則：token 只存入環境變數，不寫進 repo 或 Markdown

## 儲存 Token

```powershell
# Windows
setx NOTION_API_KEY "ntn_你的token"
```

```bash
# macOS / Linux
echo 'export NOTION_API_KEY="ntn_你的token"' >> ~/.bashrc
source ~/.bashrc
```

## 依 Agent 類型註冊 MCP

**Claude Code：**
```bash
claude mcp add notion -- npx -y @notionhq/notion-mcp-server
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "notion": {
      "type": "local",
      "command": ["npx", "-y", "@notionhq/notion-mcp-server"],
      "env": {
        "NOTION_API_KEY": "${NOTION_API_KEY}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  notion:
    command: npx
    args: ["-y", "@notionhq/notion-mcp-server"]
    env:
      NOTION_API_KEY: "${NOTION_API_KEY}"
    enabled: true
```

## 驗證

重啟 Agent 後，請 AI：「列出我的 Notion 資料庫」

## 常見問題

| 問題 | 解法 |
|------|------|
| AI 連線成功但讀不到頁面 | 在 Notion 頁面右上角 Share → 把 Integration 加入 |
| Token 格式錯誤 | 確認開頭為 `ntn_`，不要包含引號 |
