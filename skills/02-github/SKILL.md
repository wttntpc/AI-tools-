---
name: ai-tools-github
description: 連接 GitHub CLI — 適用 Claude Code、AntiGravity、Codex、OpenCode。說「連接 GitHub」「設定 GitHub」時載入。
---

# 連接 GitHub（通用版）

## 步驟

### 1. 檢查安裝狀態
```powershell
git --version
gh --version
```

若 gh 未安裝：
```powershell
# Windows
winget install --id GitHub.cli --accept-source-agreements --accept-package-agreements
# macOS
brew install gh
```

### 2. 登入 GitHub（瀏覽器 OAuth）
```powershell
gh auth status
gh auth login --web --git-protocol https
```

### 3. 設定 Git 使用者
```powershell
git config --global user.name "你的名字"
git config --global user.email "your-email@example.com"
```

### 4. 驗證
```powershell
gh auth status
git config --global user.name
git config --global user.email
```

⚠️ 安全規則：不把 GitHub token 寫進任何 Markdown 或 repo。commit 前先檢查 diff，不使用無差別 `git add .`。
