---
name: opencode-browser
description: 安裝瀏覽器控制工具（Playwright MCP + open-computer-use）。說「裝瀏覽器控制」「瀏覽器自動化」時載入。
---

# 瀏覽器控制

讓 OpenCode 控制瀏覽器和桌面應用。

## 步驟

### 1. Playwright MCP
在 `~/.config/opencode/opencode.json` 加入：
```json
"playwright": {
  "type": "local",
  "command": ["npx", "-y", "@playwright/mcp"],
  "enabled": true
}
```

### 2. open-computer-use
```bash
npm install -g open-computer-use
```
> ⚠️ Windows 不能跑 `open-computer-use install-opencode-mcp`（需要 POSIX shell），手動編輯設定檔。

加入 opencode.json：
```json
"open-computer-use": {
  "type": "local",
  "command": ["open-computer-use", "mcp"],
  "enabled": true
}
```

### 3. 驗證
- Playwright: 「開啟 https://example.com 並告訴我標題」
- open-computer-use: 「截圖我的桌面」

### 輕量替代：CLI-Anything Browser CLI
若只需簡單網頁操作（不需完整自動化）：
```bash
pip install cli-anything-hub && cli-hub install browser
cli-anything-browser navigate https://example.com
```

回報格式：Playwright 狀態、open-computer-use 狀態、兩個工具的啟動測試結果。
