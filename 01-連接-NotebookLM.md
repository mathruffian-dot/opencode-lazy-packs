# OpenCode 懶人包 #01：連接 NotebookLM

> 版本：v0.1
> 更新日期：2026-05-19

---

## 這個懶人包會幫你做什麼？

讓 OpenCode 可以讀寫你的 NotebookLM 筆記本：
- 安裝 `notebooklm-mcp-cli`
- 登入 Google 帳號
- 寫入 opencode.json MCP 設定
- 驗證連線

---

## 先備條件

- [ ] Node.js 18+ 已安裝
- [ ] uv 已安裝
- [ ] 已有 Google 帳號
- [ ] 電腦有網路連線

---

## 請 OpenCode 幫我執行以下步驟

### 步驟一：安裝 notebooklm-mcp-cli

```bash
pip install notebooklm-mcp-cli
```

確認版本：
```bash
nlm --version
```

---

### 步驟二：登入 NotebookLM

```bash
nlm login
```

> 瀏覽器會開啟 Google 登入頁面，請選擇你的 Google 帳號完成授權。

完成後檢查：
```bash
nlm list
```

應顯示你的 NotebookLM 筆記本清單。

---

### 步驟三：找到 nlm 執行檔路徑

**Windows**（PowerShell）：
```bash
where.exe nlm
```

**macOS / Linux**：
```bash
which nlm
```

記下這個完整路徑，例如：
- Windows：`C:\Users\<你>\AppData\Local\Programs\Python\Python313\Scripts\nlm.exe`
- macOS：`/Users/<你>/.local/bin/nlm`

---

### 步驟四：寫入 OpenCode MCP 設定

編輯 `~/.config/opencode/opencode.json`，加入：

```json
{
  "mcp": {
    "notebooklm": {
      "type": "local",
      "command": ["<nlm完整路徑>", "--transport", "stdio"],
      "enabled": true
    }
  }
}
```

Windows 範例：
```json
{
  "mcp": {
    "notebooklm": {
      "type": "local",
      "command": ["C:\\Users\\mathr\\.local\\bin\\notebooklm-mcp.EXE", "--transport", "stdio"],
      "enabled": true
    }
  }
}
```

---

### 步驟五：驗證連線

重新開啟 OpenCode，然後問它：

```
請列出我所有的 NotebookLM 筆記本。
```

---

## 完成回報格式

```md
## NotebookLM 連接完成

- nlm 版本：xxx
- 登入狀態：成功 / 失敗
- MCP 設定：已寫入 ~/.config/opencode/opencode.json
- 工具清單：已載入 xxx 個工具
- 筆記本讀取測試：成功 / 失敗
```

---

## 常見問題

| 問題 | 解法 |
|------|------|
| `nlm login` 瀏覽器不自動開啟 | 手動開啟 https://notebooklm.google.com/ 確認已登入 |
| nlm 指令找不到 | 檢查 Python 的 Scripts 目錄是否在 PATH 中 |
| opencode.json 格式錯誤 | JSON 最後一項不能有逗號 |

---

## 更新紀錄

| 日期 | 版本 | 更新內容 |
|------|------|---------|
| 2026-05-19 | v0.1 | 初版 |
