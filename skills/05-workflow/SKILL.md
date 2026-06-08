---
name: ai-tools-workflow
description: 開工/收工/新專案初始化流程 — 適用 Claude Code、AntiGravity、Codex、OpenCode。說「開工」「收工」「初始化專案」時載入。
---

# 開工 / 收工 / 新專案初始化（通用版）

## 規則檔對應

| Agent | 規則檔 |
|-------|--------|
| Claude Code | `CLAUDE.md` |
| AntiGravity | `ANTIGRAVITY.md` |
| Codex | `AGENTS.md` |
| OpenCode | `OPENCODE.md` |

---

## 開工

1. 偵測當前 Agent 類型，讀取對應規則檔
2. 執行 `git status` 確認目前狀態
3. 讀取最近 3 筆 commit：`git log --oneline -3`
4. 回報狀態與建議下一步
5. 不自動 pull / commit / push

---

## 收工

1. 檢查敏感資料（API key、token、個人清單等）
2. 更新 Obsidian 專案駕駛艙（完成事項、下一步、踩坑）
3. 只在規則改變時更新對應規則檔
4. 執行 `git status` + `git diff` 確認變更
5. 只 stage 本次相關檔案（不使用 `git add .`）
6. 產生 commit message，確認後 commit + push
7. 回報同步結果

---

## 新專案初始化

先詢問使用者：
- 專案名稱、用途、工作目錄
- 是否建立 GitHub repo（公開/私有）
- 是否需要部署（GitHub Pages / Firebase 等）
- Obsidian vault 與專案駕駛艙位置

接著建立或補齊：
- 對應規則檔（CLAUDE.md / ANTIGRAVITY.md / AGENTS.md）
- `README.md`
- `.gitignore`
- Git repo 與 GitHub repo（如需要）
- Obsidian 專案駕駛艙

若資料夾已存在 → 盤點「已有 / 缺少」清單，只補缺口，不覆蓋既有設定。
