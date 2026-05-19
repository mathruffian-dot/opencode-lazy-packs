# OpenCode 懶人包 #05：連接 Firebase

> 版本：v0.1
> 更新日期：2026-05-19

---

## 這個懶人包會幫你做什麼？

讓 OpenCode 可以管理 Firebase：
- 安裝 Firebase CLI
- 登入 Google 帳號
- 建立或選擇 Firebase 專案
- 寫入 opencode.json MCP 設定
- 驗證 CRUD

---

## 先備條件

- [ ] Node.js 18+ 已安裝
- [ ] 已有 Google 帳號

---

## 請 OpenCode 幫我執行以下步驟

### 步驟一：安裝 Firebase CLI

```bash
npm install -g firebase-tools
```

確認版本：
```bash
firebase --version
```

---

### 步驟二：登入 Firebase

```bash
firebase login
```

瀏覽器會開啟 Google 登入頁面。

登入後檢查可用專案：
```bash
firebase projects:list
```

---

### 步驟三：初始化 Firebase 專案

在專案目錄執行：
```bash
firebase init
```

選擇需要的服務（Firestore、Hosting 等）。

---

### 步驟四：寫入 OpenCode MCP 設定

編輯 `~/.config/opencode/opencode.json`，加入：

```json
{
  "mcp": {
    "firebase": {
      "type": "local",
      "command": ["npx", "-y", "firebase-tools@latest", "mcp"],
      "enabled": true
    }
  }
}
```

---

### 步驟五：驗證連線

重新開啟 OpenCode，然後問它：

```
請列出我的 Firebase 專案。
```

---

## 完成回報格式

```md
## Firebase 連接完成

- firebase-tools 版本：xxx
- 登入狀態：成功 / 失敗
- 可用專案：xxx 個
- MCP 設定：已寫入 opencode.json
- 工具測試：成功 / 失敗
```

---

## 常見問題

| 問題 | 解法 |
|------|------|
| `firebase login` 瀏覽器不開 | 手動執行，或檢查防火牆 |
| opencode.json 路徑錯誤 | 確認 `~/.config/opencode/opencode.json` 存在 |

---

## 更新紀錄

| 日期 | 版本 | 更新內容 |
|------|------|---------|
| 2026-05-19 | v0.1 | 初版 |
