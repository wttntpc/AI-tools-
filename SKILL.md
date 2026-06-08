---
name: ai-tools-lazy-packs
description: 通用 AI 工具連接懶人包 — 適用 Claude Code、AntiGravity、Codex、OpenCode。說「AI 工具懶人包」「安裝通用設定」時載入。
---

# 通用 AI 工具懶人包 — AI Agent 自動安裝入口

當使用者給你這個 repo 網址並說要安裝時，請依以下流程執行。

## 步驟一：認識可用懶人包

| 編號 | Skill 名稱 | 說明 |
|------|-----------|------|
| 01 | `01-notebooklm` | 連接 NotebookLM MCP |
| 02 | `02-github` | 連接 GitHub CLI |
| 03 | `03-firebase` | 連接 Firebase MCP |
| 04 | `04-notion` | 連接 Notion MCP |
| 05 | `05-workflow` | 開工 / 收工 / 新專案初始化 |
| 06 | `06-obsidian` | 連接 Obsidian MCP (MCPVault) |
| 00 | `00-install-all` | 一次安裝全部 |

## 步驟二：讓使用者選擇

列出上表給使用者看，然後問：「你要安裝哪些？輸入全部或編號組合（例如 01, 02, 03）。」

## 步驟三：依序安裝

對選取的每個 skill：
```bash
npx skills add mathruffian-dot/antigravity-lazy-pack --skill <skill名稱> -g -y
```

若 `npx skills add` 無法使用，改為手動：讀取 `skills/<名稱>/SKILL.md` 內容並執行。

## 步驟四：回報結果

每項回報安裝狀態（✅/⚠️/❌），最後列總表。
