---
name: opencode-workflow-skills
description: 安裝開工/收工/初始化三大技能。說「裝工作流程技能」「安裝 startup shutdown skills」時載入。
---

# 開工/收工/初始化技能

安裝三個全域 OpenCode 技能：startup、shutdown、project-init。

## 步驟

### 1. 建立技能目錄
```bash
mkdir -p ~/.config/opencode/skills/startup
mkdir -p ~/.config/opencode/skills/shutdown
mkdir -p ~/.config/opencode/skills/project-init
```

### 2. 建立三個 SKILL.md
- **startup**：說「開工」→ 讀 Obsidian 工作筆記 + 檢查 Git
- **shutdown**：說「收工」→ Git commit/push + Obsidian 同步 + chezmoi
- **project-init**：說「初始化專案」→ AGENTS.md + Git + GitHub + Obsidian

（完整內容見原懶人包 #07）

### 3. 設定 opencode.json 權限
```json
"permission": {
  "skill": {
    "startup": "allow",
    "shutdown": "allow",
    "project-init": "allow",
    "*": "ask"
  }
}
```

### 4. 驗證
重啟 OpenCode 後說「開工」。

回報格式：三個技能路徑、opencode.json 權限設定、驗證結果。
