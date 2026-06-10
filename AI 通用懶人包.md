# 通用 AI 工具連接懶人包 v1.5

> 適用：Claude Code、AntiGravity、Codex（OpenAI）、OpenCode、Hermes Agent
> 更新日期：2026-06-10
> 語系偏好：繁體中文（Taiwan）

這份懶人包讓任何支援 MCP 或工具呼叫的 AI Agent，都能用同一份流程完成 NotebookLM、GitHub、Gemini、Obsidian、Notion、Firebase、Google Calendar、Gmail、Google Drive、Zotero、HackMD 的連接與開工收工工作流程設定。

---

## Step 0：偵測 AI Agent 類型 + 環境檢查

### 偵測 Agent 類型

AI 請先自動判斷目前的執行環境，並告知使用者：

| Agent | 規則檔 | MCP 設定方式 |
|-------|--------|------------|
| Claude Code | `CLAUDE.md` | `claude mcp add` 指令 |
| AntiGravity | `ANTIGRAVITY.md` | `opencode.json` 或 UI |
| Codex (OpenAI) | `AGENTS.md` | `opencode.json` 或 CLI flag |
| OpenCode | `OPENCODE.md` 或 `CLAUDE.md` | `opencode.json` |
| Hermes Agent | `AGENTS.md`（或 `.hermes.md`） | `~/.hermes/config.yaml` |

> 如果無法自動偵測，請問使用者：「你現在用的是哪個 AI Agent？」

> 💡 **Hermes 規則檔載入順序**：`.hermes.md` → `AGENTS.md` → `CLAUDE.md` → `.cursorrules`（第一個找到的為準）

### 環境檢查

不管哪個 Agent，都先執行以下檢查：

```powershell
# Windows
git --version
gh --version
uv --version
python --version
node --version
npm.cmd --version
```

```bash
# macOS / Linux
git --version
gh --version
uv --version
python3 --version
node --version
npm --version
```

**缺少工具時的安裝指引：**

| 工具 | Windows | macOS | Linux |
|------|---------|-------|-------|
| Git | `winget install Git.Git` | `xcode-select --install` | `sudo apt install git` |
| GitHub CLI | `winget install GitHub.cli` | `brew install gh` | 見 cli.github.com |
| uv | `powershell -c "irm https://astral.sh/uv/install.ps1 \| iex"` | `curl -LsSf https://astral.sh/uv/install.sh \| sh` | 同左 |
| Node.js | winget 或 nvm | brew install node | sudo apt install nodejs |

---

## Step 1：連接 NotebookLM（所有 Agent 通用）

### 安裝 MCP CLI

```powershell
uv tool install notebooklm-mcp-cli
nlm --version
```

### 登入 Google 帳號（瀏覽器 OAuth）

```powershell
nlm login
nlm doctor
```

> 如登入到錯誤帳號：`nlm logout` → `nlm login` → 在瀏覽器選正確帳號

### 依 Agent 類型註冊 MCP

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

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  notebooklm:
    command: nlm
    args: ["mcp"]
    enabled: true
```

重啟 Agent 後，請 AI 列出 NotebookLM 筆記本確認連線成功。

---

## Step 2：連接 GitHub（所有 Agent 通用）

```powershell
gh auth status
gh auth login --web --git-protocol https
git config --global user.name "你的名字"
git config --global user.email "your-email@example.com"
```

驗證：

```powershell
gh auth status
git config --global user.name
git config --global user.email
```

> 安全規則：不把 GitHub token 寫進任何 Markdown 或 repo
> Hermes Agent 使用 `AGENTS.md` 作為規則檔（與 Codex 相同）

---

## Step 3：設定 Gemini 免費 API（所有 Agent 通用）

1. 前往 https://aistudio.google.com/apikey 取得免費 API Key（不需信用卡）
2. 儲存為環境變數：

```powershell
# Windows
setx GEMINI_API_KEY "你的-API-KEY"
```

```bash
# macOS
echo 'export GEMINI_API_KEY="你的-API-KEY"' >> ~/.zshrc
source ~/.zshrc
# Linux
echo 'export GEMINI_API_KEY="你的-API-KEY"' >> ~/.bashrc
source ~/.bashrc
```

3. 驗證（Windows PowerShell）：

```powershell
$env:GEMINI_API_KEY = "你的-API-KEY"
Invoke-RestMethod -Uri "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=$env:GEMINI_API_KEY" `
  -Method Post -ContentType "application/json" `
  -Body '{"contents":[{"parts":[{"text":"1+1等於多少？"}]}]}'
```

---

## Step 4：連接 Obsidian（選用）

### 安裝 MCPVault

```powershell
# Windows
npm.cmd install -g @bitbonsai/mcpvault
where.exe mcpvault
```
```bash
# macOS / Linux
npm install -g @bitbonsai/mcpvault
which mcpvault
```

**先設定 vault 路徑為環境變數：**

```powershell
# Windows
setx OBSIDIAN_VAULT_PATH "C:\Users\你\OneDrive\文件\Secondbrain"
```
```bash
# macOS
echo 'export OBSIDIAN_VAULT_PATH="$HOME/Documents/Secondbrain"' >> ~/.zshrc
source ~/.zshrc
# Linux
echo 'export OBSIDIAN_VAULT_PATH="$HOME/Documents/Secondbrain"' >> ~/.bashrc
source ~/.bashrc
```

### 依 Agent 類型註冊 MCP

**Claude Code：**
```bash
claude mcp add obsidian -e OBSIDIAN_VAULT_PATH="$OBSIDIAN_VAULT_PATH" -- mcpvault "$OBSIDIAN_VAULT_PATH"
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "obsidian": {
      "type": "local",
      "command": ["mcpvault", "${OBSIDIAN_VAULT_PATH}"],
      "env": {
        "OBSIDIAN_VAULT_PATH": "${OBSIDIAN_VAULT_PATH}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  obsidian:
    command: mcpvault
    args: ["${OBSIDIAN_VAULT_PATH}"]
    env:
      OBSIDIAN_VAULT_PATH: "${OBSIDIAN_VAULT_PATH}"
    enabled: true
```

---

## Step 5：連接 Notion（選用）

### 事前準備

1. 前往 https://www.notion.so/profile/integrations → **New integration**
2. 填入名稱（例如 `AI-MCP`），選擇工作區，點 Submit
3. 複製 **Internal Integration Token**（`ntn_...` 開頭），妥善保存

> 安全規則：token 只存入環境變數，不寫進 repo 或 Markdown

### 儲存 Token 為環境變數

```powershell
# Windows
setx NOTION_API_KEY "ntn_你的token"
```

```bash
# macOS
echo 'export NOTION_API_KEY="ntn_你的token"' >> ~/.zshrc
source ~/.zshrc
# Linux
echo 'export NOTION_API_KEY="ntn_你的token"' >> ~/.bashrc
source ~/.bashrc
```

### 依 Agent 類型註冊 MCP

**Claude Code：**
```bash
claude mcp add notion -e NOTION_API_KEY="$NOTION_API_KEY" -- npx -y @notionhq/notion-mcp-server
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

> 重啟 Agent 後，請 AI 列出 Notion 資料庫確認連線成功。
> 記得在 Notion 頁面右上角「Share」→ 把 Integration 加進去，AI 才能存取該頁面。

---

## Step 6：連接 Firebase（選用）

```powershell
# Windows（使用 npx.cmd 避免執行原則問題）
npx.cmd -y firebase-tools@latest --version
npx.cmd -y firebase-tools@latest login
npx.cmd -y firebase-tools@latest projects:list
```
```bash
# macOS / Linux
npx -y firebase-tools@latest --version
npx -y firebase-tools@latest login
npx -y firebase-tools@latest projects:list
```

### 依 Agent 類型註冊 MCP

**Claude Code：**
```bash
claude mcp add firebase -- npx -y firebase-tools@latest mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "firebase": {
      "type": "local",
      "command": ["npx.cmd", "-y", "firebase-tools@latest", "mcp"],
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  firebase:
    command: npx
    args: ["-y", "firebase-tools@latest", "mcp"]
    enabled: true
```

---

## Step 7：連接 Google Calendar（選用）

> ⚠️ 資安：憑證不入 repo，只授予 `calendar.readonly` 權限，AI 無法刪除事件。

1. 前往 https://console.cloud.google.com → 建立或選擇專案（例如 `AI-MCP`）
2. **APIs & Services → Library** → 啟用 `Google Calendar API`
3. **OAuth consent screen** → External → 填入 App name 和 Email → Scopes 只加 `calendar.readonly`
4. **Credentials → Create Credentials → OAuth client ID** → Desktop app → 下載 `client_secret_xxx.json`，存到 `C:\Users\你\.google\`

```powershell
# Windows
setx GOOGLE_CALENDAR_CREDENTIALS "C:\Users\你\.google\client_secret_xxx.json"
```
```bash
# macOS
echo 'export GOOGLE_CALENDAR_CREDENTIALS="$HOME/.google/client_secret_xxx.json"' >> ~/.zshrc
source ~/.zshrc
# Linux
echo 'export GOOGLE_CALENDAR_CREDENTIALS="$HOME/.google/client_secret_xxx.json"' >> ~/.bashrc
source ~/.bashrc
```

**Claude Code：**
```bash
claude mcp add google-calendar -e GOOGLE_CALENDAR_CREDENTIALS="$GOOGLE_CALENDAR_CREDENTIALS" -- npx -y @modelcontextprotocol/server-google-calendar
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "google-calendar": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-google-calendar"],
      "env": {
        "GOOGLE_CALENDAR_CREDENTIALS": "${GOOGLE_CALENDAR_CREDENTIALS}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  google-calendar:
    command: npx
    args: ["-y", "@modelcontextprotocol/server-google-calendar"]
    env:
      GOOGLE_CALENDAR_CREDENTIALS: "${GOOGLE_CALENDAR_CREDENTIALS}"
    enabled: true
```

`.gitignore` 加入：`client_secret*.json`、`token*.json`

---

## Step 8：連接 Gmail（選用）

> ⚠️ 資安：`@gongrzhe/server-gmail-autoauth-mcp` 實際授予 `gmail.modify` 權限（可讀取、撰寫、封存，但不能永久刪除）。
> AI 起草郵件後必須由你確認才能發送，詳見 `skills/08-gmail/SKILL.md`。

1. 前往 https://console.cloud.google.com → 同上專案
2. **APIs & Services → Library** → 啟用 `Gmail API`
3. **OAuth consent screen** → External → 填入 App name、Email、Test users（加入你的 Gmail）
4. **Credentials → Create Credentials → OAuth client ID** → Desktop app → 下載憑證，存到 `C:\Users\你\.google\`

```powershell
# Windows — 複製憑證到 Gmail MCP 指定位置
New-Item -ItemType Directory -Force "$env:USERPROFILE\.gmail-mcp"
Copy-Item "C:\Users\你\.google\client_secret_xxx.json" "$env:USERPROFILE\.gmail-mcp\gcp-oauth.keys.json"
setx GOOGLE_GMAIL_CREDENTIALS "C:\Users\你\.google\client_secret_xxx.json"
```
```bash
# macOS / Linux — 複製憑證到 Gmail MCP 指定位置
mkdir -p ~/.gmail-mcp
cp ~/.google/client_secret_xxx.json ~/.gmail-mcp/gcp-oauth.keys.json
# macOS
echo 'export GOOGLE_GMAIL_CREDENTIALS="$HOME/.google/client_secret_xxx.json"' >> ~/.zshrc && source ~/.zshrc
# Linux
echo 'export GOOGLE_GMAIL_CREDENTIALS="$HOME/.google/client_secret_xxx.json"' >> ~/.bashrc && source ~/.bashrc
```

**Claude Code：**
```bash
claude mcp add gmail -e GOOGLE_GMAIL_CREDENTIALS="$GOOGLE_GMAIL_CREDENTIALS" -- npx -y @gongrzhe/server-gmail-autoauth-mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "gmail": {
      "type": "local",
      "command": ["npx", "-y", "@gongrzhe/server-gmail-autoauth-mcp"],
      "env": {
        "GOOGLE_GMAIL_CREDENTIALS": "${GOOGLE_GMAIL_CREDENTIALS}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  gmail:
    command: npx
    args: ["-y", "@gongrzhe/server-gmail-autoauth-mcp"]
    env:
      GOOGLE_GMAIL_CREDENTIALS: "${GOOGLE_GMAIL_CREDENTIALS}"
    enabled: true
```

---

## Step 9：連接 Google Drive（選用）

> ⚠️ 資安：只授予 `drive.readonly`，AI 只能讀取不能修改或刪除檔案。

1. 前往 https://console.cloud.google.com → 同上專案
2. **APIs & Services → Library** → 啟用 `Google Drive API`
3. **OAuth consent screen** → Scopes 只加 `drive.readonly`
4. **Credentials → Create Credentials → OAuth client ID** → Desktop app → 下載憑證

```powershell
# Windows
setx GOOGLE_DRIVE_CREDENTIALS "C:\Users\你\.google\client_secret_xxx.json"
```
```bash
# macOS
echo 'export GOOGLE_DRIVE_CREDENTIALS="$HOME/.google/client_secret_xxx.json"' >> ~/.zshrc
source ~/.zshrc
# Linux
echo 'export GOOGLE_DRIVE_CREDENTIALS="$HOME/.google/client_secret_xxx.json"' >> ~/.bashrc
source ~/.bashrc
```

**Claude Code：**
```bash
claude mcp add google-drive -e GOOGLE_DRIVE_CREDENTIALS="$GOOGLE_DRIVE_CREDENTIALS" -- npx -y @modelcontextprotocol/server-gdrive
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "google-drive": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-gdrive"],
      "env": {
        "GOOGLE_DRIVE_CREDENTIALS": "${GOOGLE_DRIVE_CREDENTIALS}"
      },
      "enabled": true
    }
  }
}
```

**Hermes Agent（~/.hermes/config.yaml）：**
```yaml
mcp_servers:
  google-drive:
    command: npx
    args: ["-y", "@modelcontextprotocol/server-gdrive"]
    env:
      GOOGLE_DRIVE_CREDENTIALS: "${GOOGLE_DRIVE_CREDENTIALS}"
    enabled: true
```

---

## Step 11：連接 HackMD（選用）

> HackMD 是 Markdown 協作筆記平台，透過官方 API 讓 AI 直接讀寫你的筆記。
> ⚠️ 資安：API token 只存入系統環境變數，不貼在對話中、不寫進 repo。

### 1. 取得 HackMD API Token（需自己操作）

1. 登入 [HackMD](https://hackmd.io) → 右上角頭像 → **Settings**
2. 左側選 **API** → **Create API Token**
3. 名稱填 `AI-MCP`，勾選 ✅ Read + ✅ Write
4. 複製 token（**只出現一次，請妥善保存**）

> ⚠️ 安全提醒：不要把 token 貼在 AI 對話視窗，直接自己在終端機輸入。

### 2. 儲存 Token 為環境變數（自己在終端機輸入）

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

### 3. 依 Agent 類型註冊 MCP

**Claude Code：**
```bash
claude mcp add hackmd -e HACKMD_API_TOKEN="%HACKMD_API_TOKEN%" -- npx -y hackmd-mcp
```

**AntiGravity / Codex / OpenCode（opencode.json）：**
```json
{
  "mcp": {
    "hackmd": {
      "type": "local",
      "command": ["npx", "-y", "hackmd-mcp"],
      "env": {
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

### 4. 驗證

重啟 Agent 後，請 AI：「列出我 HackMD 最近的筆記」

`.gitignore` 已包含：`hackmd.env`

---

## Step 10：連接 Zotero（選用）

> 使用本機 API（localhost:23119），不需要 API Key，資料完全不離開本機。
> ⚠️ 已知問題：`zotero-mcp` npm 套件與 Zotero 7.x 不相容，改用直接 API 呼叫。
> 此方式適用所有 Agent（Claude Code、AntiGravity、Codex、OpenCode、Hermes）。

1. 開啟 Zotero → `Edit` → `Preferences` → **進階**
2. 勾選「**允許此電腦上的其他應用程式與 Zotero 通訊**」
3. 畫面顯示 `Available at http://localhost:23119/api/` 即成功

AI 驗證：
```powershell
curl -s "http://localhost:23119/api/users/0/items?itemType=journalArticle&limit=3"
```

成功回傳論文清單即完成 ✅

---

## 附錄 A：建立 Agent 規則檔

根據偵測到的 Agent，在專案根目錄建立對應規則檔：

| Agent | 規則檔 | 備註 |
|-------|--------|------|
| Claude Code | `CLAUDE.md` | |
| AntiGravity | `ANTIGRAVITY.md` | |
| Codex | `AGENTS.md` | |
| OpenCode | `OPENCODE.md` | |
| Hermes Agent | `AGENTS.md` 或 `.hermes.md` | 優先讀取 `.hermes.md` |

### 通用規則檔範本

```markdown
# <專案名稱> - AI 工作規則

## 專案資訊
專案名稱：
用途：
工作目錄：
GitHub repo：

## 連接的工具
- NotebookLM：已連接 / 未設定
- GitHub：已連接 / 未設定
- Gemini API：已設定 / 未設定
- Notion：已連接 / 未使用
- Firebase：已連接 / 未使用
- Obsidian vault：已連接 / 未使用
- Google Calendar：已連接 / 未使用
- Gmail：已連接 / 未使用
- Google Drive：已連接 / 未使用
- Zotero：已連接 / 未使用
- HackMD：已連接 / 未使用

## 工作規則
- 回應使用繁體中文
- 涉及檔案操作時回報完整路徑
- Windows 環境使用 PowerShell 語法
- 開工時讀本檔並回報目前 Git 狀態
- 收工時更新 Obsidian 駕駛艙、檢查 diff、只提交相關檔案

## 禁止事項
- 不 commit API key、token、密碼
- 不 commit NotebookLM 個人清單或筆記本 ID
- 不使用無差別 git add .
- Gmail：發信前必須顯示完整草稿，等待使用者確認才發送
```

---

## 附錄 B：開工 / 收工 / 新專案初始化

### 開工（對任何 Agent 說「開工」）

AI 應執行：
1. 讀取對應規則檔（CLAUDE.md / ANTIGRAVITY.md / AGENTS.md / .hermes.md）
2. 執行 `git status` 確認目前狀態
3. 讀取最近 3 筆 commit：`git log --oneline -3`
4. 回報狀態與建議下一步

### 收工（對任何 Agent 說「收工」）

AI 應執行：
1. 確認無敏感資料（API key、token、個人清單）
2. 更新 Obsidian 專案駕駛艙（完成項目、下一步、踩坑）
3. 執行 `git status` 與 `git diff`
4. 只 stage 本次相關檔案
5. 產生 commit message 並確認後 push

---

## 完成回報格式

AI 完成安裝後請用以下格式回報：

```
## 通用懶人包設定完成

Agent 類型：Claude Code / AntiGravity / Codex / OpenCode / Hermes Agent
作業系統：Windows / macOS / Linux

- NotebookLM MCP：✅ 已連接 / ⚠️ 待 OAuth / ❌ 失敗
- GitHub CLI：✅ 已登入 (帳號: xxx) / ❌ 未登入
- Gemini API：✅ 已設定 / ⚠️ 待取得 Key / ❌ 未設定
- Notion MCP：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- Firebase MCP：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- Obsidian MCP：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- Google Calendar：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- Gmail：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- Google Drive：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- Zotero：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- HackMD：✅ 已連接 / ⏭️ 跳過 / ❌ 失敗
- 規則檔：✅ 已建立 (CLAUDE.md) / ⚠️ 待建立

下一步：
```

---

## 常見問題

| 問題 | 解法 |
|------|------|
| NotebookLM 登入到錯帳號 | `nlm logout` 後重新 `nlm login` |
| `nlm doctor` 未認證 | 重跑 OAuth，不要手動貼 cookie |
| Windows 顯示 CP950 編碼錯誤 | 設定 `$env:PYTHONIOENCODING = "utf-8"` |
| GitHub CLI 找不到 gh 指令 | 確認安裝路徑，或重新安裝後重開終端機 |
| Firebase login 在 AI 對話卡住 | 開獨立 PowerShell 視窗手動登入 |
| PowerShell 擋 npx.ps1 | 改用 `npx.cmd` |
| MCP 設定後 Agent 沒看到工具 | 重啟 Agent |
| Notion 連線成功但讀不到頁面 | 在 Notion 頁面右上角 Share → 把 Integration 加入 |
| Notion token 格式錯誤 | 確認開頭為 `ntn_`，不要包含引號 |
| Obsidian vault 路徑找不到 | 搜尋含 `.obsidian` 的資料夾 |
| Gmail OAuth access_denied | 到 Google Cloud Console → OAuth consent screen → 目標對象 → 加入你的 Gmail 為測試使用者 |
| Gmail MCP 連線失敗 | 確認憑證已複製到 `~/.gmail-mcp/gcp-oauth.keys.json` |
| Zotero 連線失敗 | 確認 Zotero 桌面程式已開啟，且已勾選本機 API 選項 |
| Hermes MCP 工具沒出現 | 確認 `~/.hermes/config.yaml` 格式正確（YAML 縮排敏感），重啟 Hermes |
| Hermes 規則檔沒被讀取 | 優先建立 `.hermes.md`，或確認 `AGENTS.md` 在專案根目錄 |
| HackMD MCP 連線失敗 | 確認 `HACKMD_API_TOKEN` 環境變數已設定，重開終端機後再試 |
| HackMD token 無效 | 到 HackMD Settings → API 重新產生 token，勾選 Read + Write 權限 |
| HackMD token 洩漏 | 立即到 HackMD Settings → API 撤銷舊 token，重新建立新 token |

---

## 資安維護（每季執行）

前往 👉 https://myaccount.google.com/permissions

確認「有權存取帳戶的任何 Google 服務」清單中：
- ✅ 只有你認識的應用程式
- ❌ 不認識的應用程式 → 立即點擊「刪除連結」撤銷授權

---

## 更新紀錄

| 日期 | 版本 | 內容 |
|------|------|------|
| 2026-06-10 | v1.5 | 新增 HackMD（Step 11），含資安說明、三種 Agent 設定、常見問題 |
| 2026-06-08 | v1.4 | 新增 Hermes Agent 支援（Step 0 偵測、每個 Step 的 YAML 設定、附錄規則檔說明） |
| 2026-06-08 | v1.3 | 步驟重新編號為 Step 0-10，Step 0 合併偵測+環境，規則檔和開工收工改為附錄，修正 Gmail scope 說明，新增資安維護章節 |
| 2026-06-08 | v1.2 | 新增 Google Calendar、Gmail、Google Drive、Zotero，步驟重新編號 |
| 2026-06-08 | v1.1 | 新增 Notion MCP 連接，移除付費生圖步驟，步驟重新編號 |
| 2026-06-08 | v1.0 | 初版：整合 Claude Code、AntiGravity、Codex、OpenCode 通用流程 |
