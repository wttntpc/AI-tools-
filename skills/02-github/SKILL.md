---
name: ai-tools-github
description: 連接 GitHub CLI — 適用 Claude Code、AntiGravity、Codex、OpenCode、Hermes Agent。說「連接 GitHub」「設定 GitHub」時載入。
---

# 連接 GitHub CLI（通用版）

> GitHub CLI（`gh`）讓 AI 直接管理 repo、建立 PR、push 程式碼。
> 登入走瀏覽器 OAuth，**不需要手動複製 token**，資安風險極低。

---

## 🔒 資安原則（必讀）

| 規則 | 說明 |
|------|------|
| Token 不入 repo | 不把 GitHub token、PAT 寫進任何檔案或 repo |
| 不用無差別 add | 禁止使用 `git add .` 或 `git add -A`，改用 `git add 指定檔案` |
| commit 前先檢查 | 執行 `git diff --staged` 確認沒有敏感資料再 commit |
| .gitignore 優先 | 新專案一定先建立 `.gitignore`，再進行任何 commit |
| 敏感檔案清單 | `.env`、`*.json`（憑證）、`token*`、`*secret*` 都不 commit |

---

## 步驟

### 1. 檢查 Git 是否已安裝

```powershell
git --version
```

**若顯示錯誤「找不到指令」，請安裝 Git：**

- **Windows**：開啟瀏覽器前往 👉 https://git-scm.com/download/win → 下載並安裝
- 或用指令安裝：
  ```powershell
  winget install --id Git.Git --accept-source-agreements --accept-package-agreements
  ```
- **macOS**：
  ```bash
  xcode-select --install
  ```

安裝完成後**重新開啟終端機**，再次執行 `git --version` 確認。

---

### 2. 檢查 GitHub CLI 是否已安裝

```powershell
gh --version
```

**若顯示錯誤「找不到指令」，請安裝 GitHub CLI：**

- **Windows**：
  ```powershell
  winget install --id GitHub.cli --accept-source-agreements --accept-package-agreements
  ```
  > ⚠️ 安裝後必須**重新開啟終端機**，`gh` 指令才會生效。
  > 若 winget 回報「已安裝」（exit code 43），不是錯誤，直接繼續。

- **macOS**：
  ```bash
  brew install gh
  ```

- **若 `gh` 仍找不到**，用完整路徑確認是否已安裝：
  ```powershell
  # Windows — 搜尋 gh.exe 位置
  where.exe gh
  # 若找不到，嘗試：
  & "C:\Program Files\GitHub CLI\gh.exe" --version
  ```
  若能執行，將路徑加入 PATH 環境變數，或每次用完整路徑呼叫。

---

### 3. 登入 GitHub（瀏覽器 OAuth）

> ⚠️ 此步驟需要在瀏覽器完成，AI 無法代為操作。

先確認登入狀態：
```powershell
gh auth status
```

若顯示「**not logged in**」或錯誤，執行登入：
```powershell
gh auth login --web --git-protocol https
```

**登入流程：**
1. 終端機顯示一組 **8 位數驗證碼**（例如 `ABCD-1234`）
2. 瀏覽器自動開啟 GitHub 授權頁面
3. 在網頁上輸入驗證碼 → 點「**Authorize GitHub CLI**」
4. 授權完成後回到終端機，顯示「Logged in as 你的帳號」即成功

> 💡 若瀏覽器沒有自動開啟，複製終端機顯示的網址手動貼到瀏覽器。

---

### 4. 設定 Git 使用者資訊

```powershell
git config --global user.name "你的姓名"
git config --global user.email "你的GitHub信箱"
```

> 這裡填的 email 要與 GitHub 帳號的 email 一致，commit 記錄才會正確連結到你的帳號。

---

### 5. 驗證所有設定

```powershell
gh auth status
git config --global user.name
git config --global user.email
```

**正確結果範例：**
```
✓ Logged in to github.com as 你的帳號 (oauth_token)
你的姓名
你的信箱@email.com
```

---

## 🛡️ 安全操作習慣

### commit 前必做的檢查
```powershell
# 查看目前有哪些變更
git status

# 查看詳細變更內容（確認沒有敏感資料）
git diff

# 只 stage 指定檔案（不要用 git add .）
git add 檔案名稱.py
git add src/component.tsx

# 確認 staged 內容
git diff --staged
```

### .gitignore 範本（新專案必備）

```gitignore
# 環境變數與金鑰
.env
.env.*
*.key
*secret*
*token*

# Google / API 憑證
client_secret*.json
token*.json
gcp-oauth.keys.json
*.credentials.json

# 個人資料
notebooklm_notebooks.json
zotero_export.*

# 系統檔案
.DS_Store
Thumbs.db
```

---

## 常用指令

| 任務 | 指令 |
|------|------|
| 查看登入狀態 | `gh auth status` |
| 列出自己的 repo | `gh repo list` |
| 複製 repo | `gh repo clone 帳號/repo名稱` |
| 建立新 repo | `gh repo create` |
| 建立 PR | `gh pr create` |
| 查看 PR 狀態 | `gh pr status` |
| 登出 | `gh auth logout` |

---

## 常見問題排解

| 問題 | 原因 | 解法 |
|------|------|------|
| `gh: command not found` | 安裝後沒重開終端機 | 關閉並重新開啟終端機 |
| winget 回報 exit code 43 | 已安裝，不是錯誤 | 直接繼續下一步 |
| 瀏覽器沒開啟授權頁 | 系統預設瀏覽器問題 | 手動複製終端機的網址貼到瀏覽器 |
| push 被拒絕（rejected） | 遠端有新 commit | 先執行 `git pull --rebase` 再 push |
| commit 後想撤銷 | 誤 commit 了不該 commit 的檔案 | `git reset HEAD~1`（本機撤銷，未 push 前有效） |
| 不小心 push 了 token | 嚴重資安事件 | 立即到 GitHub → Settings → Developer Settings → 撤銷該 token |
