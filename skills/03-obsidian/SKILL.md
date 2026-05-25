---
name: opencode-obsidian
description: 連接 Obsidian，讓 OpenCode 讀寫第二大腦筆記。說「連接 Obsidian」「設定 Obsidian vault」時載入。
---

# 連接 Obsidian

透過 `@bitbonsai/mcpvault` 讓 OpenCode 讀寫 Obsidian vault。

## 步驟

### 1. 找到 Obsidian vault 路徑
搜尋含 `.obsidian` 子資料夾的目錄：
```powershell
Get-ChildItem -Path "$env:USERPROFILE\OneDrive" -Recurse -Directory |
  Where-Object { Test-Path (Join-Path $_.FullName ".obsidian") }
```
確認使用者這是主要 vault。

### 2. 安裝 mcpvault
```bash
npm install -g @bitbonsai/mcpvault
```

### 3. 寫入 opencode.json
```json
"obsidian": {
  "type": "local",
  "command": ["npx", "@bitbonsai/mcpvault", "<VAULT_PATH>"],
  "enabled": true
}
```

### 4. 驗證
重啟 OpenCode 後：「列出 Obsidian vault 根目錄」→ 再建立測試筆記。

### 進階：CLI-Anything Obsidian CLI
若需全文檢索、metadata 操作等進階功能：
1. 在 Obsidian 安裝 Local REST API plugin
2. `pip install cli-anything-hub && cli-hub install obsidian`

回報格式：vault 路徑、mcpvault 版本、讀取/寫入測試結果。
