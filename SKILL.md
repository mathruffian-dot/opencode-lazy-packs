---
name: opencode-lazy-packs
description: OpenCode 懶人包全集 — 環境建置、MCP 串接、技能安裝，說「安裝懶人包」「opencode 懶人包」時載入
---

# OpenCode 懶人包 — AI Agent 自動安裝入口

當使用者給你這個 repo 網址並說要安裝時，請依以下流程執行：

## 步驟一：認識可用懶人包

這個 repo 的 `skills/` 目錄下有 10 個技能。全部支援 `npx skills add` 一鍵安裝。

| 編號 | Skill 名稱 | 說明 | 前置需求 |
|------|-----------|------|---------|
| 00 | `00-env-setup` | 安裝 Node.js、OpenCode、Git、GitHub CLI、uv | 無 |
| 01 | `01-notebooklm` | 連接 NotebookLM MCP | #00 |
| 02 | `02-github` | 連接 GitHub CLI + 驗證 push | #00 |
| 03 | `03-obsidian` | 連接 Obsidian MCPVault | 有 Obsidian vault |
| 04 | `04-second-brain` | 建立第二大腦三層結構 | #03 |
| 05 | `05-firebase` | 連接 Firebase MCP | #00 |
| 06 | `06-browser` | 安裝 Playwright + open-computer-use | #00 |
| 07 | `07-workflow-skills` | 安裝開工/收工/初始化技能 | #03 |
| 08 | `08-draw` | 安裝 draw 生圖技能（gpt-image-2）| OpenAI API Key |
| 09 | `09-install-all` | 一次安裝全部（依序執行以上 9 個）| — |

## 步驟二：讓使用者選擇

列出上表給使用者看，然後問：

```
以上是這份懶人包的所有項目。

你可以：
- 輸入「全部」→ 一次安裝全部（用 09-install-all）
- 輸入編號組合 → 例如「00, 01, 03」只裝這三個
- 若有已安裝的項目 → 告訴我跳過

你要安裝哪些？
```

## 步驟三：依序安裝（使用 skill 機制）

對使用者選取的每個 skill，執行：

```bash
npx skills add mathruffian-dot/opencode-lazy-packs --skill <skill名稱> -g -y
```

> ⚠️ 如果 `npx skills add` 無法使用（例如環境不支援），改為手動安裝：讀取 `skills/<名稱>/SKILL.md` 的內容，照裡面的步驟執行。

## 步驟四：安裝後驗證

每安裝完一個，依照該 skill 內的「完成回報格式」回報結果。全部完成後列出總表。

## 補充說明

- `00-env-setup` 幾乎所有 pack 的前置，建議優先安裝
- 已安裝的工具（如 Node.js、Git 等）安裝時會自動跳過
- 全部 skill 內容都在 `skills/<名稱>/SKILL.md`，需要詳細步驟時直接讀取
