# OpenCode 懶人包

> 每份 MD 檔丟給 OpenCode 就能自動完成設定。
> OpenAI Codex 或 Claude Code 的用戶請看對應的懶人包。

---

## 使用方式

### 方式一：一行指令安裝（推薦）

```bash
npx skills add mathruffian-dot/opencode-lazy-packs --skill <skill名> -g -y
```

可用的 skill 名：

| Skill 名 | 對應懶人包 |
|----------|-----------|
| `00-env-setup` | 環境建置 |
| `01-notebooklm` | 連接 NotebookLM |
| `02-github` | 連接 GitHub |
| `03-obsidian` | 連接 Obsidian |
| `04-second-brain` | 第二大腦設定 |
| `05-firebase` | 連接 Firebase |
| `06-browser` | 瀏覽器控制 |
| `07-workflow-skills` | 開工/收工技能 |
| `08-draw` | 生圖技能 |
| `09-install-all` | 一次全部安裝 |

安裝後對 OpenCode 說該技能對應的關鍵字即可啟動。

### 方式二：手動下載 MD 檔

1. 看影片了解原理
2. 下載對應的懶人包（MD 檔）
3. 開啟終端機，在專案目錄執行 `opencode`
4. 把懶人包內容丟給 OpenCode，它會自動執行

---

## 為什麼有這份？

原本的懶人包是給 Claude Code 和 OpenAI Codex 用的。OpenCode 是第三個 AI 編碼代理工具，設定方式與前兩者不同：

| 差異 | OpenCode | Claude Code | OpenAI Codex |
|------|----------|-------------|--------------|
| 安裝 | `npm install -g opencode-ai` | `npm install -g @anthropic-ai/claude-code` | `npm install -g @openai/codex` |
| 全域設定 | `~/.config/opencode/opencode.json` | `~/.claude/settings.json` | `~/.codex/config.toml` |
| 專案指令檔 | `AGENTS.md` | `CLAUDE.md` | `AGENTS.md` |
| MCP 配置 | 編輯 opencode.json | `claude mcp add` | `codex mcp add` |
| Skill 機制 | 原生支援（SKILL.md） | 原生支援 | Desktop 支援 |
| 命令 | 有 `/` 內建命令 | 有 `/` 內建命令 | ❌ 沒有 |

---

## 最低先備條件

- [ ] Node.js 18+ 已安裝
- [ ] 電腦有網路連線

---

## 懶人包清單

| 編號 | 名稱 | 對應影片 | 狀態 | 說明 |
|------|------|---------|------|------|
| 00 | [環境建置](00-環境建置.md) | — | v0.1 | 安裝 OpenCode、Node.js、Git、GitHub CLI、uv |
| 01 | [連接 NotebookLM](01-連接-NotebookLM.md) | — | v0.1 | 安裝 NotebookLM MCP + 產生簡報與圖表 |
| 02 | [連接 GitHub](02-連接-GitHub.md) | — | v0.1 | GitHub CLI 登入 + Pages 教材上線 |
| 03 | [建立第二大腦 Obsidian](03-建立第二大腦-Obsidian.md) | — | v0.1 | Obsidian MCPVault 連接 |
| 04 | [第二大腦設定指南](04-第二大腦設定指南.md) | — | v0.1 | 三層結構 + AGENTS.md + 模板 |
| 05 | [連接 Firebase](05-連接-Firebase.md) | — | v0.1 | Firebase MCP 安裝 |
| 06 | [安裝瀏覽器控制](06-安裝瀏覽器控制.md) | — | v0.1 | Playwright MCP + open-computer-use |
| 07 | [開工/收工/初始化技能](07-開工收工初始化技能.md) | — | v0.1 | 全域三技能：startup、shutdown、project-init |
| 08 | [生圖技能](08-生圖.md) | — | v0.1 | draw skill：OpenAI gpt-image-2 生圖 |

---

## 授權

MIT License，與 Claude Code / Codex 懶人包相同。
