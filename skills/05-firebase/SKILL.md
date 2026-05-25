---
name: opencode-firebase
description: 連接 Firebase，讓 OpenCode 管理 Firestore / Hosting / Auth。說「連接 Firebase」「設定 Firebase」時載入。
---

# 連接 Firebase

透過 `firebase-tools` 讓 OpenCode 管理 Firebase 專案。

## 步驟

### 1. 安裝 Firebase CLI
```bash
npm install -g firebase-tools
firebase --version
```

### 2. 登入
```bash
firebase login
```

### 3. 初始化專案
在專案目錄執行：
```bash
firebase init
```

### 4. 寫入 opencode.json
```json
"firebase": {
  "type": "local",
  "command": ["npx", "-y", "firebase-tools@latest", "mcp"],
  "enabled": true
}
```

### 5. 驗證
重啟 OpenCode 後：「請列出我的 Firebase 專案。」

回報格式：firebase-tools 版本、登入狀態、專案數量、MCP 寫入狀態、工具測試結果。
