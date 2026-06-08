---
name: ai-tools-zotero
description: 連接 Zotero MCP — 讓 AI 直接查詢、引用你的文獻庫。說「連接 Zotero」「查詢文獻」時載入。
---

# 連接 Zotero（通用版）

> Zotero MCP 使用本機 API（localhost:23119），只在 Zotero 桌面程式執行時有效。
> 憑證不會離開本機，資安風險極低。

---

## 資安原則

- Zotero API key 只存入環境變數，不寫進任何 Markdown 或 repo
- `.gitignore` 加入 `zotero.env`
- Zotero 未執行時 MCP 自動無法連線，不會有資料外洩風險

---

## 步驟

### 1. 確認 Zotero 已安裝並開啟

下載：https://www.zotero.org/download/
開啟 Zotero 桌面程式，確認正在執行。

---

### 2. 啟用本機 API 並取得 Key（需自己操作）

> ⚠️ 此步驟需在 Zotero 桌面程式內手動完成，AI 無法代為操作。

1. 開啟 Zotero → `Edit` → `Preferences` → `Advanced`
2. 找到 **Local API** 區塊
3. 勾選 **Enable local API**
4. 複製畫面上自動產生的 **API Key**

> 若找不到 Local API 選項，請確認 Zotero 版本為 7.x（`Help → About Zotero`）。
> 舊版本需先到 https://www.zotero.org/settings/keys 以 Zotero 帳號登入後建立 key。

完成後將 API Key 提供給 AI 繼續下一步。

---

### 3. 儲存 API Key 為環境變數（AI 執行）

```powershell
# Windows
setx ZOTERO_API_KEY "你的-api-key"
```

```bash
# macOS / Linux
echo 'export ZOTERO_API_KEY="你的-api-key"' >> ~/.bashrc
source ~/.bashrc
```

---

### 4. 依 Agent 類型註冊 MCP（AI 執行）

**Claude Code：**
```bash
claude mcp add zotero -- npx -y zotero-mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "zotero": {
      "type": "local",
      "command": ["npx", "-y", "zotero-mcp"],
      "env": {
        "ZOTERO_API_KEY": "${ZOTERO_API_KEY}",
        "ZOTERO_LOCAL_PORT": "23119"
      },
      "enabled": true
    }
  }
}
```

---

### 5. 驗證

先確認 Zotero 桌面程式正在執行，再重啟 Agent，然後請 AI：
「列出我 Zotero 文獻庫中最近新增的 5 篇論文」

---

## 常用指令範例

| 任務 | 對 AI 說 |
|------|---------|
| 搜尋文獻 | 「在 Zotero 找所有關於 EEG 和 exercise 的論文」 |
| 列出分類 | 「列出 Zotero 的所有分類」 |
| 引用格式 | 「這篇論文的 APA 引用格式是什麼？」 |
| 摘要整理 | 「整理 Zotero 中關於 HRV 的文獻重點」 |

---

⚠️ 注意事項：
- 使用前必須先開啟 Zotero 桌面程式
- 只設定 Read Only 權限，AI 無法修改或刪除你的文獻
- 不把 API key 或任何文獻資料 commit 到 repo
