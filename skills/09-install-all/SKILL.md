---
name: opencode-install-all
description: 一次安裝所有 OpenCode 懶人包技能（00-08 全部）。說「全部安裝」「裝完所有懶人包」時載入。
---

# 一次安裝全部技能

依序載入並執行所有 9 個懶人包技能：

1. **00-env-setup** — 安裝 Node.js、OpenCode、Git、gh、uv
2. **01-notebooklm** — 連接 NotebookLM MCP
3. **02-github** — 連接 GitHub CLI
4. **03-obsidian** — 連接 Obsidian MCPVault
5. **04-second-brain** — 建立第二大腦三層結構
6. **05-firebase** — 連接 Firebase MCP
7. **06-browser** — 安裝 Playwright + open-computer-use
8. **07-workflow-skills** — 安裝 startup/shutdown/project-init
9. **08-draw** — 安裝 draw 生圖技能

## 執行方式

依序載入每個子技能並執行。每完成一個報告進度。
跳過已安裝的工具（檢查版本確認）。
最終回報總表：9 項各別的成功/失敗/已跳過狀態。

> 💡 也可用 `npx skills add` 個別安裝：
> ```bash
> npx skills add mathruffian-dot/opencode-lazy-packs --skill 01-notebooklm -g -y
> ```
