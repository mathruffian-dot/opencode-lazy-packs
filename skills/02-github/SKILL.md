---
name: opencode-github
description: 連接 GitHub，讓 OpenCode 管理 repo、commit、push。說「連接 GitHub」「設定 GitHub」時載入。
---

# 連接 GitHub

透過 GitHub CLI (`gh`) 讓 OpenCode 管理 GitHub。

## 步驟

### 1. 確認已安裝
```bash
git --version && gh --version
```

### 2. 登入 GitHub CLI
```bash
gh auth status
```
若未登入：
```bash
gh auth login --web --git-protocol https
```

### 3. 設定 Git 使用者資訊
檢查並補設：
```bash
git config --global user.name "你的姓名"
git config --global user.email "你的email"
```

### 4. 驗證 commit/push
建立測試 repo → commit → push → 確認成功後詢問保留或刪除。

回報格式：Git/GitHub CLI 版本、登入狀態、使用者資訊、commit/push 測試結果。
