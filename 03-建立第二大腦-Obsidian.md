# OpenCode 懶人包 #03：建立第二大腦 Obsidian

> 版本：v0.2
> 更新日期：2026-05-25

---

## 這個懶人包會幫你做什麼？

讓 OpenCode 可以讀寫你的 Obsidian 筆記本：
- 找到 Obsidian vault 位置
- 安裝 MCPVault（@bitbonsai/mcpvault）
- 寫入 opencode.json MCP 設定
- 驗證讀寫

---

## 先備條件

- [ ] Node.js 18+ 已安裝
- [ ] 已有 Obsidian vault

---

## 請 OpenCode 幫我執行以下步驟

### 步驟一：找到使用者的 Obsidian vault

先問使用者筆記本位置。常見位置：

| 同步方式 | 常見路徑 |
|----------|----------|
| OneDrive | `C:\Users\<你>\OneDrive\文件\<vault名稱>` |
| Google Drive | `G:\我的雲端硬碟\<vault名稱>` |
| Documents | `C:\Users\<你>\Documents\<vault名稱>` |

如果不知道，可搜尋：
```bash
# Windows（PowerShell）
Get-ChildItem -Path "$env:USERPROFILE\OneDrive" -Recurse -Directory -Force |
  Where-Object { Test-Path (Join-Path $_.FullName ".obsidian") }
```

確認條件：
- 資料夾存在
- 裡面有 `.obsidian` 子資料夾
- 使用者確認這是主要筆記本

---

### 步驟二：安裝 mcpvault

```bash
npm install -g @bitbonsai/mcpvault
```

確認安裝位置：
```bash
# Windows
where.exe mcpvault

# macOS / Linux
which mcpvault
```

---

### 步驟三：寫入 OpenCode MCP 設定

編輯 `~/.config/opencode/opencode.json`，加入：

```json
{
  "mcp": {
    "obsidian": {
      "type": "local",
      "command": ["npx", "@bitbonsai/mcpvault", "<VAULT_PATH>"],
      "enabled": true
    }
  }
}
```

Windows 範例：
```json
{
  "mcp": {
    "obsidian": {
      "type": "local",
      "command": ["npx", "@bitbonsai/mcpvault", "C:\\Users\\mathr\\OneDrive\\文件\\Secondbrain"],
      "enabled": true
    }
  }
}
```

---

### 步驟四：驗證連線

重新開啟 OpenCode，然後問它：

```
請列出我的 Obsidian vault 根目錄的資料夾。
```

再測試寫入：

```
請在我的 Obsidian 建立一則測試筆記，內容寫「OpenCode 已成功連接 Obsidian」。
```

---

## 完成回報格式

```md
## Obsidian 連接完成

- Vault 路徑：<VAULT_PATH>
- mcpvault：已安裝 / 未安裝
- MCP 設定：已寫入 opencode.json
- 讀取測試：成功 / 失敗
- 寫入測試：成功 / 失敗
```

---

## 進階：CLI-Anything Obsidian CLI

若需要全文檢索、metadata 操作、tag 管理、筆記分析等進階功能，可安裝 CLI-Anything 的 Obsidian CLI：

### 前置需求
1. 在 Obsidian 內安裝 [Local REST API](https://github.com/coddingtonbear/obsidian-local-rest-api) 社群插件
2. 插件設定中啟用 HTTPS（非必要）並記下 API key

### 安裝
```bash
pip install cli-anything-hub
cli-hub install obsidian
```

### 使用範例
```bash
cli-anything-obsidian search "SKILL.md"
cli-anything-obsidian tag list
cli-anything-obsidian note get "專案工作流程.md"
```

> 💡 mcpvault（本懶人包預設方案）適合基本讀寫；Obsidian CLI 適合需要查詢、分析、批量操作的情境。兩者可並存。

---

## 常見問題

| 問題 | 解法 |
|------|------|
| `npm install -g` 出現 EPERM | Windows 以系統管理員身分執行 |
| 找不到 vault | 搜尋 `.obsidian` 資料夾位置 |
| opencode.json 格式錯誤 | JSON 最後一項不能有逗號，路徑雙引號 |

---

## 更新紀錄

| 日期 | 版本 | 更新內容 |
|------|------|---------|
| 2026-05-25 | v0.2 | 補充 CLI-Anything Obsidian CLI 進階方案 |
| 2026-05-19 | v0.1 | 初版 |
