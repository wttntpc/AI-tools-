# AI 工具連接懶人包（通用版）

適用於 **Claude Code、AntiGravity、Codex（OpenAI）、OpenCode、Hermes Agent** 等支援 MCP 的 AI Agent。

把這行貼給你的 AI Agent，它會列出所有工具讓你**自由選擇**要安裝哪些：

```
這是 AI 工具連接懶人包 https://github.com/wttntpc/AI-tools-
請讀取 AI 通用懶人包.md，依步驟引導我完成設定。
```

---

## 可以連接哪些工具？

所有工具皆為**選用**，依需求自由選擇安裝。

| # | 工具 | 說明 |
|---|------|------|
| 01 | NotebookLM | Google 知識管理工具，透過 MCP 讓 AI 直接讀寫筆記本 |
| 02 | GitHub | 版本控制，透過 CLI 管理 repo、push、PR |
| 03 | Obsidian | 本地知識庫，透過 MCPVault 連接 |
| 04 | Notion | 筆記與資料庫，透過官方 MCP 讓 AI 讀寫頁面 |
| 05 | Workflow | 開工 / 收工 / 新專案初始化流程 |
| 06 | Firebase | 雲端資料庫 |
| 07 | Google Calendar | 讓 AI 讀取、建立、修改行事曆事件 |
| 08 | Gmail | 讓 AI 讀取、搜尋、起草郵件 |
| 09 | Google Drive | 讓 AI 讀取雲端 PDF、Docs、Sheets |
| 10 | Zotero | 讓 AI 直接查詢、引用你的文獻庫 |
| 11 | HackMD | 讓 AI 直接讀取、建立、編輯 HackMD 筆記與共筆 |

---

## 支援的 AI Agent

| Agent | 規則檔 | MCP 設定 |
|-------|--------|---------|
| Claude Code | `CLAUDE.md` | `claude mcp add` |
| AntiGravity | `ANTIGRAVITY.md` | `opencode.json` |
| Codex (OpenAI) | `AGENTS.md` | `opencode.json` |
| OpenCode | `OPENCODE.md` | `opencode.json` |
| Hermes Agent | `AGENTS.md` 或 `.hermes.md` | `~/.hermes/config.yaml` |

懶人包會自動偵測你使用的 Agent，套用對應的設定方式。

> 💡 **Hermes Agent** 使用 `~/.hermes/config.yaml`（YAML 格式），與其他 Agent 的 `opencode.json` 不同。

---

## 檔案說明

| 檔案 | 說明 |
|------|------|
| `AI 通用懶人包.md` | 主要懶人包，包含完整安裝流程（Step 0–11，附錄 A–B） |
| `SKILL.md` | AI Agent 自動讀取的安裝入口 |
| `.gitignore` | 排除 API key、token、個人資料等敏感檔案 |

---

## 安全原則

### 🔑 Token / API Key 保護
- **絕對不把 token 貼在 AI 對話視窗**（對話紀錄可能被存取）
- API key 與 token 只存入系統環境變數，不寫進任何檔案或 repo
- 所有 Google 服務登入走瀏覽器 OAuth，不複製 cookie 或 token

### 📁 檔案安全
- 不 commit 個人 NotebookLM 清單、筆記本 ID、研究報告
- 收工前先執行 `git diff`，確認沒有敏感資料才 push
- 只 stage 本次相關檔案，不使用 `git add .`

### ⚠️ 最小權限原則
- Google 服務只授予需要的最小 scope（如 `drive.readonly`）
- HackMD token 只授予 Read + Write，不授予 Admin 權限
- 定期到 https://myaccount.google.com/permissions 確認授權應用程式

### 🚨 Token 洩漏緊急處理
1. **立即撤銷**：到對應平台刪除洩漏的 token
   - HackMD：Settings → API → Delete token
   - Google：console.cloud.google.com → Credentials → 刪除 OAuth client
   - GitHub：Settings → Developer settings → Tokens → Revoke
2. **重新建立**新 token 並更新環境變數
3. 確認相關 repo 沒有殘留敏感資料（`git log` 搜尋確認）

---

## 更新紀錄

| 日期 | 版本 | 內容 |
|------|------|------|
| 2026-06-10 | v1.5 | 新增 HackMD（11），支援 AI 讀寫 HackMD 筆記 |
| 2026-06-08 | v1.4 | 所有 skill 改為選用，更新 SKILL.md 安裝流程 |
| 2026-06-08 | v1.3 | 新增 Zotero（10），加強 07-09 資安說明 |
| 2026-06-08 | v1.2 | 新增 Google Calendar、Gmail、Google Drive（07-09） |
| 2026-06-08 | v1.1 | 新增 Notion 連接，移除付費生圖（04-draw） |
| 2026-06-08 | v1.0 | 改為通用版，支援 Claude Code、AntiGravity、Codex、OpenCode |
