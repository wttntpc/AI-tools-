---
name: ai-tools-hackmd
description: 連接 HackMD MCP — 讓 AI 直接讀取、建立、編輯 HackMD 筆記與共筆。適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 HackMD」「讀取 HackMD 筆記」時載入。
---

# 連接 HackMD（通用版）

> HackMD 是 Markdown 協作筆記平台，透過官方 API 讓 AI 直接讀寫你的筆記。

---

## 資安原則

| 規則 | 說明 |
|------|------|
| API token 不入 repo | `HACKMD_API_TOKEN` 只存入系統環境變數 |
| 最小權限 | 只授予需要的讀寫範圍 |
| 不 commit 筆記內容 | 個人筆記、研究內容不上傳 repo |

`.gitignore` 已包含：
```
hackmd.env
```

---

## 步驟

### 1. 取得 HackMD API Token（需自己操作）

> ⚠️ 此步驟需在瀏覽器手動完成，AI 無法代為操作。

1. 登入 [HackMD](https://hackmd.io)
2. 點右上角頭像 → **Settings**
3. 左側選 **API** → **Create API Token**
4. 填入名稱（例如 `AI-MCP`），選擇權限：
   - ✅ `Read` — 讀取筆記
   - ✅ `Write` — 建立 / 編輯筆記
5. 複製產生的 token（只出現一次）

完成後告知 AI 繼續。

---

### 2. 儲存 Token 為環境變數

```powershell
# Windows
setx HACKMD_API_TOKEN "你的token"
```

```bash
# macOS
echo 'export HACKMD_API_TOKEN="你的token"' >> ~/.zshrc
source ~/.zshrc

# Linux
echo 'export HACKMD_API_TOKEN="你的token"' >> ~/.bashrc
source ~/.bashrc
```

---

### 3. 依 Agent 類型註冊 MCP

**Claude Code：**
```bash
claude mcp add --env HACKMD_API_TOKEN="$HACKMD_API_TOKEN" hackmd -- npx -y hackmd-mcp
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

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  hackmd:
    command: npx  # Windows 請改為 npx.cmd
    args: ["-y", "hackmd-mcp"]
    env:
      HACKMD_API_TOKEN: "${HACKMD_API_TOKEN}"
    enabled: true
```

---

### 4. 驗證

重啟 Agent 後，請 AI：「列出我 HackMD 最近的筆記」

---

## 常用指令範例

| 任務 | 對 AI 說 |
|------|---------|
| 列出筆記 | 「列出我 HackMD 最近的筆記」 |
| 讀取筆記 | 「讀取 HackMD 筆記『研究計畫』並整理大綱」 |
| 建立筆記 | 「在 HackMD 建立一篇新筆記，標題為『EEG 分析結果』」 |
| 編輯筆記 | 「更新 HackMD 筆記，加入今天的分析結果」 |
| 共筆連結 | 「產生這篇 HackMD 筆記的分享連結」 |
