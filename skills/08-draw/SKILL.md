---
name: opencode-draw
description: 安裝 AI 生圖技能（OpenAI gpt-image-2）。說「安裝生圖技能」「設定畫圖」時載入。
---

# 生圖技能 (draw)

安裝全域 draw 技能，之後在任何專案說「畫一張 XX」就能 AI 生圖。

## 前置需求
- [ ] `~/.openai.env` 已含 `OPENAI_API_KEY`
- [ ] Python `openai` 套件已安裝：`pip install openai`

## 步驟

### 1. 複製技能檔案
```bash
# 從本 repo 下載 draw.py
curl -o ~/.config/opencode/skills/draw/SKILL.md https://raw.githubusercontent.com/mathruffian-dot/opencode-lazy-packs/main/skills/08-draw/SKILL.md
```

### 2. 確認 draw.py 位置
確保 `~/.claude/skills/draw/draw.py` 已存在（若無則從 Claude Code 技能目錄複製）。

### 3. 設定 opencode.json 權限
```json
"draw": "allow"
```

### 4. 測試
```bash
python ~/.claude/skills/draw/draw.py "一隻橘貓坐在窗邊，水彩風格" --name test --quality low
```

### 使用方式
安裝後在任何對話中直接說「畫一張 XXX」，agent 會自動載入技能並呼叫 `draw.py` 生圖。
輸出目錄：當前專案 `slides/generated/`。

回報格式：API key 狀態、`draw: allow` 權限設定、測試生圖結果。
