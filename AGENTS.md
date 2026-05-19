# AGENTS.md — opencode-lazy-packs

## 這個資料夾是什麼

OpenCode 專用的懶人包倉庫，對應 repo：
**mathruffian-dot/opencode-lazy-packs**

與 `claude-code-lazy-packs/`、`codex-lazy-packs/` 平行：
同一套教學流程，分別給三個 AI 編碼代理使用。

## 主要差異

| 項目 | opencode | claude-code | codex |
|------|----------|-------------|-------|
| 設定檔 | `opencode.json` | `settings.json` | `config.toml` |
| 專案檔 | `AGENTS.md` | `CLAUDE.md` | `AGENTS.md` |
| MCP | 編輯 JSON | `claude mcp add` | `codex mcp add` |
| Skills dir | `~/.config/opencode/skills/` | `~/.claude/skills/` | `~/.codex/skills/` |

## 雙倉同步規則

- **不要**把 Claude Code 或 Codex 版直接複製過來；MCP 指令、設定檔格式都不同
- 修改主流程時三邊都要更新
- Obsidian 閱讀版：`OpenCode 懶人包/`
- GitHub 版：`opencode-lazy-packs/`

## 提醒

- 使用者說「更新 OpenCode 懶人包」→ 只動本資料夾
- 使用者說「三邊都更新」→ 三個資料夾都改
