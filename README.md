# OpenCode 懶人包

> 把一份 MD 檔丟給 OpenCode，它就照著幫你把設定做完。
> 寫給第一次碰 AI Agent 的老師：全程繁體中文、不用寫程式、不用先刷卡。

這個 repo 只教 **OpenCode** 一種工具。不比較、不推薦第二套，因為研習現場最貴的成本是「選擇」。

📌 **版本紀錄一律看 [CHANGELOG.md](CHANGELOG.md)**（各檔案內不再各自標版本）。

---

## 使用方式

### 方式一：直接叫 AI 幫你裝（最簡單）

把這段貼給你的 AI agent：

```
這是 OpenCode 懶人包全集 https://github.com/mathruffian-dot/opencode-lazy-packs
請讀取 repo 內容，列出所有可用的懶人包，問我要裝哪些。
```

AI 會自動：

1. 讀取 repo 根目錄的 `SKILL.md`（安裝入口）
2. 列出主線 11 包給你看
3. 問你要裝哪些（可以回「全部」，也可以只回編號）
4. 依序安裝你選的項目，每包裝完回報結果

### 方式二：一行指令自己裝

```bash
npx skills add mathruffian-dot/opencode-lazy-packs --skill <skill名> -g -y
```

`<skill名>` 就是下方清單裡的**資料夾名稱**（例如 `00-env-setup`）。

### 方式三：手動貼 MD 檔

1. 先看對應的影片，了解這一包在幹嘛
2. 打開 `skills/<名稱>/SKILL.md`，整份複製
3. 開啟 OpenCode，把內容貼給它
4. 它會照著執行，需要你動手的地方會停下來告訴你

---

## 懶人包清單

主線 **11 包**（`#00`～`#10`），依「作品階梯」編號——**編號就是建議的學習順序**。
另有 `extras/` 加碼 **2 包**（非主線）。

影片頻道：**[三師爸 Sense Bar](https://www.youtube.com/@sensebar)**

| 編號 | 名稱（skill 名） | 階梯 | 對應影片 | 狀態 | 說明 |
|------|-----------------|------|---------|------|------|
| 00 | [環境建置](skills/00-env-setup/SKILL.md)<br>`00-env-setup` | 地基 | AI Agent 基本功 EP01、EP02 | ✅ | 裝 OpenCode 桌面版／CLI、Git、GitHub CLI、Node.js、uv |
| 01 | [連接免費模型](skills/01-connect-model/SKILL.md)<br>`01-connect-model` | 地基 | AI Agent 基本功 EP01、EP02<br>OpenCode 基本功 EP06 | ✅ | 用 OpenCode Zen 接上免費模型，讓它真的能對話、能動手 |
| 02 | [內部工具包](skills/02-file-toolkit/SKILL.md)<br>`02-file-toolkit` | 內部工具 | AI Agent 基本功 EP03 | ✅ | Python 文件處理（Word／Excel／PPT／PDF／QR）＋ yt-dlp／FFmpeg ＋ Edge-TTS |
| 03 | [連接 NotebookLM](skills/03-notebooklm/SKILL.md)<br>`03-notebooklm` | 外部工具 | AI Agent 基本功 EP04 | ✅ | NotebookLM MCP：自動產簡報、資訊圖表、音訊概覽 |
| 04 | [連接 Obsidian](skills/04-obsidian/SKILL.md)<br>`04-obsidian` | 外部工具 | AI Agent 基本功 EP06 | ✅ | 第二大腦：讓 OpenCode 讀寫你的筆記庫 |
| 05 | [Google Sheets ＋ Apps Script](skills/05-sheets-gas/SKILL.md)<br>`05-sheets-gas` | 資料庫 | AI Agent 基本功 EP05 | ✅ | 用試算表當後端。**瀏覽器直接貼程式碼部署，不用 clasp** |
| 06 | [Supabase 即時資料庫](skills/06-supabase/SKILL.md)<br>`06-supabase` | 資料庫 | AI Agent 基本功 EP05 | ✅ | 多人同時寫入：文字雲、線上對戰、即時排行榜 |
| 07 | [連接 GitHub](skills/07-github/SKILL.md)<br>`07-github` | 跨電腦 | AI Agent 基本功 EP01（clone）<br>AI Agent 基本功 EP06（跨電腦）| ✅ | 三階段：clone 拿別人的 → push 存自己的 → 換電腦 pull 驗收 |
| 08 | [開工／收工／初始化技能](skills/08-workflow-skills/SKILL.md)<br>`08-workflow-skills` | 跨電腦 | AI Agent 基本功 EP05<br>AI Agent 基本功 EP06 | ✅ | 三個全域技能：`startup`、`shutdown`、`project-init` |
| 09 | [Groq API](skills/09-groq-api/SKILL.md)<br>`09-groq-api` | API | AI Agent 基本功 EP04 | ✅ | 語音轉字幕 ＋ 文字清洗。**免費、不用綁信用卡** |
| 10 | [Netlify 部署與後端](skills/10-netlify/SKILL.md)<br>`10-netlify` | API | AI Agent 基本功 EP04 | ✅ | 網頁上線 ＋ 用 Functions 把金鑰藏在後端 |

**影片對照補充**

| 影片 | 標題 |
|------|------|
| AI Agent 基本功 EP01 | 用 Agent 來學習 Agent |
| AI Agent 基本功 EP02 | 核心觀念與初始化設定 |
| AI Agent 基本功 EP03 | 一句話讓 AI 幫你讀檔、寫程式、上網、做出成品 |
| AI Agent 基本功 EP04 | 連接外部工具，MCP 與連接器一張地圖講清楚 |
| AI Agent 基本功 EP05 | 三層一次講清楚：技能／全域／專案 |
| AI Agent 基本功 EP06 | 跨 Agent、跨電腦協作同一個專案 |
| OpenCode 基本功 EP06 | 各種模型正確用法全解析 |

> #00 與 #01 同屬「地基」，都對應 EP01、EP02；模型怎麼選、免費的怎麼用，細節在 **OpenCode 基本功 EP06**（與 AI Agent 基本功 EP06 是不同系列，別看錯）。

---

## 建議順序

```
地基        #00 環境建置  →  #01 連接模型      ← 沒做完這兩包，後面全部跑不動
內部工具    #02 內部工具包                     ← 讓 agent 長出手腳
外部工具    #03 NotebookLM   #04 Obsidian      ← 把外面的工具接進來
資料庫      #05 Sheets ＋ GAS                  ← 讓作品有地方存資料
資料庫       #05 Apps Script ／ #06 Supabase      ← 讓成品記得住
API         #09 Groq API                        ← 呼叫外部服務
跨電腦      #07 GitHub   #08 開工／收工技能     ← 換一台電腦也接得回來
```

> ⚠️ **#00 裝完，OpenCode 還不會回答你**——那是正常的，它還沒接上模型。
> 一定要接著做 **#01**，它才會開始工作。這是 v1 最多人卡住的地方。

---

## `extras/`：加碼路線（非主線）

| 路徑 | 內容 | 說明 |
|------|------|------|
| [`extras/firebase/SKILL.md`](extras/firebase/SKILL.md) | 連接 Firebase | 即時資料庫的**替代方案**；主線走 `06-supabase` |
| [`extras/browser/SKILL.md`](extras/browser/SKILL.md) | 瀏覽器控制 | 讓 agent 自己操作瀏覽器 |

`skills/` 與 `extras/` 的差別：

- **`skills/`**：三天研習的主線，內容為 v2 重新查證或重寫過。
- **`extras/`**：沿用 v1 內容、未重寫，也不在研習流程裡。有需要再自己跑。

> `extras/` 不在 `skills/` 目錄下，`npx skills add` 不一定抓得到。
> 最保險的用法是打開檔案、整份複製，直接貼給你的 agent。

---

## v1 使用者請看這裡

如果你之前 fork 或照著 v1 做過，v2 的結構動得不小：

- **內容集中到 `skills/<名稱>/SKILL.md`**。根目錄那些 `00-環境建置.md`、`01-連接-NotebookLM.md` 已經移除——它們原本和 `skills/` 底下的短版是雙胞胎，兩邊會漂移。
- **重新編號**，改依「地基 → 內部工具 → 外部工具 → 資料庫 → API → 跨電腦」。
- **新增兩包**：連接模型（#01）、內部工具包（#02）。
- **Firebase／瀏覽器控制移到 `extras/`；生圖包移除**。
- **版本紀錄集中到 [CHANGELOG.md](CHANGELOG.md)**，各檔案內不再標版本。

### 舊名稱 → 新位置對照

| v1 | v2 |
|----|----|
| `00-env-setup` | `00-env-setup`（內容重寫） |
| `01-notebooklm` | `03-notebooklm` |
| `02-github` | `07-github`（改成三階段，內容重寫） |
| `03-obsidian` ＋ `04-second-brain` | `04-obsidian`（兩包合併） |
| `05-firebase` | `extras/firebase` |
| `06-browser` | `extras/browser` |
| `07-workflow-skills` | `08-workflow-skills`（改為完整版） |
| `08-draw` | 已移除（見下方「生圖怎麼辦」） |
| `09-install-all` | 已移除，改由根目錄 `SKILL.md` 互動式安裝 |
| — | 新增 `01-connect-model`、`02-file-toolkit` |

### 想要舊版？還在

v1 的完整內容保存在 `v1` 標籤，隨時可以取回：

<https://github.com/mathruffian-dot/opencode-lazy-packs/tree/v1>

已經照 v1 裝好、東西都能跑的人，**不必急著重做**。真正值得回來補的是 **#01 連接模型**（v1 完全沒教）和 **#00 的 Git／gh 安裝**（v1 兩包互踢皮球，實際上沒裝到）。

---

## 這裡不教什麼

寫清楚邊界，是為了讓你知道「這份東西做不到的事，不是你的問題」。

**1. push 以外的 git 操作**
只教「拿下來（clone）→ 存上去（push）→ 換電腦拿回來（pull）」。分支、合併、rebase、Pull Request、衝突處理一律不教——那是團隊協作才需要的，一個人備課用不到，先學反而會勸退。

**2. 需要自費的 API**
所有主線內容都走「不用綁信用卡就能開始」的路線。需要付費才能用的服務不列入主線，避免研習現場有人卡在刷卡。免費方案的額度與規則各家隨時會調整，本 repo 一律不寫死數字，請以服務商當下的官方說明為準。

**3. 任何「借用他人訂閱配額」的路線**
用 OAuth 登入把某人的個人訂閱額度接給 agent 使用——這類做法可能違反服務條款，也會讓照做的老師在不知情的狀況下踩線。**這個 repo 曾因此移除整個 Gemini 包，往後也不收。**

---

## 生圖怎麼辦？

v1 的生圖包需要自費的 OpenAI API 金鑰，已在 v2 移除。

想讓 agent 生圖，請改用同帳號的免金鑰版本：

**<https://github.com/mathruffian-dot/opencode-draw-free>**（走 Pollinations，不用申請金鑰）

---

## 授權

MIT License。

---

## 相關 repo

- [opencode-draw-free](https://github.com/mathruffian-dot/opencode-draw-free) — 免金鑰生圖技能（Pollinations）
- [agent-speak-skill](https://github.com/mathruffian-dot/agent-speak-skill) — 讓 AI Agent「用語音回答你」：免費 Edge-TTS 串流語音回覆，零金鑰、無視窗（Claude Code／Codex／OpenCode／AntiGravity 通用）
- [image-vision-sidecar](https://github.com/mathruffian-dot/image-vision-sidecar) — 讓純文字模型（DeepSeek／GLM）間接讀圖的免費 Vision Sidecar：Groq API 把 PDF／PPTX／DOCX／圖片轉成繁中 Markdown
- [ai-agent-ep03](https://github.com/mathruffian-dot/ai-agent-ep03) — #02 內部工具包的安裝腳本與範例（預設分支 `master`）
