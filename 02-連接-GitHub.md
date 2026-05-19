# OpenCode 懶人包 #02：連接 GitHub

> 版本：v0.1
> 更新日期：2026-05-19

---

## 這個懶人包會幫你做什麼？

讓 OpenCode 可以 commit、push、建立 repo、啟用 GitHub Pages：
- 檢查 Git / GitHub CLI
- 用網頁端登入 GitHub CLI
- 設定 Git 使用者資訊
- 建立測試 repo 驗證 commit / push
- 建立 GitHub Pages 示範

---

## 先備條件

- [ ] Git 已安裝
- [ ] GitHub CLI（gh）已安裝
- [ ] 已有 GitHub 帳號
- [ ] 電腦有網路

---

## 請 OpenCode 幫我執行以下步驟

### 步驟一：檢查 Git 與 GitHub CLI

```bash
git --version
gh --version
```

如果 Git 未安裝，先跑 **懶人包 #00：環境建置**。

---

### 步驟二：登入 GitHub CLI

檢查登入狀態：
```bash
gh auth status
```

如果尚未登入：
```bash
gh auth login --web --git-protocol https
```

流程：
1. 終端機顯示一次性驗證碼
2. 瀏覽器開啟 https://github.com/login/device
3. 輸入驗證碼並授權
4. 回到終端機確認：`gh auth status`

成功時應看到：
```
Logged in to github.com account <你的帳號>
Git operations protocol: https
Token scopes: ... repo ...
```

---

### 步驟三：設定 Git 使用者資訊

檢查：
```bash
git config --global user.name
git config --global user.email
```

如果未設定，請輸入：
```bash
git config --global user.name "你的姓名"
git config --global user.email "你的email@example.com"
```

---

### 步驟四：建立測試 repo 驗證 commit / push

建立測試資料夾：
```bash
mkdir ~/Documents/opencode-github-test
cd ~/Documents/opencode-github-test
```

建立測試檔：
```bash
echo "# OpenCode GitHub 測試" > README.md
```

初始化並提交：
```bash
git init
git add README.md
git commit -m "建立 OpenCode GitHub 測試"
```

建立 GitHub repo 並 push：
```bash
gh repo create opencode-github-test --private --source=. --push
```

驗證：
```bash
gh repo view opencode-github-test --web
```

---

### 步驟五：測試 repo 要保留還是刪除

測試成功後，詢問使用者是否刪除。

若要刪除 GitHub repo：
```bash
gh repo delete opencode-github-test --yes
```

若要刪除本機測試資料夾：
```bash
rm -rf ~/Documents/opencode-github-test
```

---

## 完成回報格式

```md
## GitHub 連接完成

- Git：已安裝 / 已補裝
- GitHub CLI：已安裝 / 已補裝
- gh 登入：成功 / 失敗
- Git 使用者資訊：已設定 / 待設定
- commit / push 測試：成功 / 未執行
- 測試 repo：保留 / 已刪除
```

---

## 常見問題

| 問題 | 解法 |
|------|------|
| `gh auth login` 等逾時 | 開啟可互動的 PowerShell 視窗手動執行 |
| `gh auth status` 未登入 | `gh auth login --web --git-protocol https` |
| Git 全域姓名未設定 | `git config --global user.name` / `user.email` 補上 |

---

## 更新紀錄

| 日期 | 版本 | 更新內容 |
|------|------|---------|
| 2026-05-19 | v0.1 | 初版 |
