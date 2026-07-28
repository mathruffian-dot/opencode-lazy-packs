---
name: opencode-notebooklm
description: 連接 Gemini Notebook（原 NotebookLM），讓 OpenCode 讀寫筆記本、產生簡報與音訊概覽。說「連接 Gemini Notebook」「連接 Gemini Notebook」「裝 Gemini Notebook」「Gemini 筆記本」時載入。
---

# OpenCode 懶人包 #03：連接 Gemini Notebook（原 NotebookLM）

> 🔴 **產品在 2026-07-16 改名了。**
> Google 把 **Gemini Notebook** 更名為 **Gemini Notebook**，介面換成藍紫色的 Gemini 漸層 logo。
> **同一個產品、同一批筆記本，不用搬家、不用重建。**
>
> 對本包的實際影響：
>
> | 項目 | 有沒有變 |
> |---|---|
> | 產品名稱與 logo | ✅ **改了**——使用者畫面上看到的是「Gemini Notebook」 |
> | PyPI 套件名 `notebooklm-mcp-cli` | ❌ **沒變** |
> | CLI 指令 `nlm`、MCP 執行檔 `notebooklm-mcp` | ❌ **沒變** |
> | GitHub repo | ⚠️ 搬到 `jacob-bd/gemini-notebook-mcp-cli`（舊網址會轉址） |
>
> **所以底下的安裝指令全部照舊可用。** 只有跟使用者講話時要說「Gemini Notebook」，
> 否則他打開畫面會找不到你說的那個名字。


> 📌 **本懶人包可獨立執行**：會自動檢查並安裝所需工具。

---

## 這個懶人包會幫你做什麼？

讓 OpenCode 能夠操作 Gemini Notebook：
- 建立 notebook、上傳資料來源
- 產生教學簡報（Slide Deck）、資訊圖表（Infographic）
- 產生音訊概覽、影片概覽、心智圖、測驗等
- 所有成品自動下載到電腦裡的指定資料夾

---

## 原理說明

```
OpenCode ←(MCP 協定)→ notebooklm-mcp（翻譯官）←(Google 登入)→ Gemini Notebook
```

因為 Gemini Notebook 沒有官方 API，中間需要一個「翻譯官」模擬瀏覽器操作。

**裝一次，會得到兩個指令**（同一個套件提供，不要以為裝錯了）：

| 指令 | 誰在用 | 做什麼 |
|------|--------|--------|
| `nlm` | **你**，在終端機打 | 登入、診斷、手動操作筆記本 |
| `notebooklm-mcp` | **OpenCode**，自動在背後啟動 | MCP server 本體 |

> ⚠️ 這兩個不能互換。`opencode.json` 裡要填的是 **`notebooklm-mcp`**，不是 `nlm`（詳見步驟五）。

---

## ⚠️ 開始之前：最容易裝錯的一件事

|  | ✅ 正確 | ❌ 錯誤 |
|--|---------|---------|
| 套件在哪 | **PyPI**（Python 的套件庫） | npm（Node 的套件庫） |
| 套件名稱 | `notebooklm-mcp-cli` | `nlm` |
| 安裝指令 | `uv tool install notebooklm-mcp-cli` | ~~`npm install nlm`~~ |

> 🚨 **npm 上真的有一個叫 `nlm` 的套件，但它跟 Gemini Notebook 一點關係都沒有**（是 2021 年停更的另一個專案）。
> 打 `npm install nlm` **不會報錯**，會裝得好好的——然後你會拿著一個完全無關的工具，花一小時找不到問題出在哪。
> **這一包只用 PyPI 的 `notebooklm-mcp-cli`。**

---

## 先備條件

- [ ] **uv 已安裝**（由 `00-env-setup` 負責）——本包主要靠它
- [ ] Python 3.11 以上（走 pip 路線才需要自己確認；走 uv 路線 uv 會自己處理）
- [ ] OpenCode 已安裝，且已完成 `01-connect-model`
- [ ] 有 Google 帳號
- [ ] 電腦有網路連線

> 📌 **本包不需要 Node.js**。`notebooklm-mcp` 是 Python 工具，不透過 `npx` 啟動。

---

## 安裝方式二選一

**方式 A：uv（獨立環境，推薦）**
```bash
uv tool install notebooklm-mcp-cli
```

**方式 B：pip（全域安裝）**
```bash
pip install notebooklm-mcp-cli
```

> 兩種**只選一種**。混裝會讓電腦上出現兩份、升級時互相打架。研習現場一律走 A。

---

## 請 OpenCode 幫我執行以下步驟

> ⚠️ 把這份檔案的內容貼給 OpenCode，它會自動執行。遇到需要手動操作的地方會暫停指示你。

---

### 步驟零：環境檢查

1. **確認作業系統**：Windows / macOS / Linux
2. **確認網路連線正常**
3. **檢查 uv**：`uv --version`。沒有的話，步驟一補裝
4. **確認 OpenCode 叫得動**：`opencode --version`（沒有請回 `00-env-setup`）

---

### 步驟一：安裝 uv（如果沒裝）

> 📌 **uv 安裝的權威版本在 `00-env-setup` 步驟 7**。這裡只是讓本包可以獨立跑完，兩邊有出入以 `00-env-setup` 為準。

**Windows（PowerShell）**：
```powershell
winget install --id astral-sh.uv --exact --accept-source-agreements --accept-package-agreements
```

**macOS**：
```bash
brew install uv
```

**Linux / 通用備案**：
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

確認：`uv --version` → 印出版本號。

---

### 步驟二：安裝 Gemini Notebook 工具

```bash
uv tool install notebooklm-mcp-cli
```

（用 pip 的話：`pip install notebooklm-mcp-cli`）

**驗證兩個指令都在**：

```bash
nlm --version
notebooklm-mcp --help
```

**預期輸出**：
- `nlm --version` → 印出一行 `nlm version …`
- `notebooklm-mcp --help` → 印出一段 `usage:` 說明，裡面看得到 `--transport {stdio,http,sse}`

**失敗長相**：`command not found` / `無法辨識…` → **不要重裝**，先做步驟三。

---

### 步驟三：確認安裝位置與 PATH

**✅ 安裝完，執行檔會出現在這裡，這是正常且正確的位置：**

| 系統 | 位置 |
|------|------|
| Windows | `%USERPROFILE%\.local\bin\`（即 `C:\Users\<你的帳號>\.local\bin\`） |
| macOS / Linux | `~/.local/bin/` |

`uv tool install` 本來就裝在這裡。**不要刪、不要搬、不要「換成別的路徑」。**

> 🚨 **如果你看過任何教學叫你「避開 `.local\bin` 底下的執行檔」——那是錯的。**
> 照做會把唯一正確的安裝刪掉，然後怎麼修都修不好。

**查實際完整路徑**：

```powershell
# Windows（PowerShell）
where.exe nlm; where.exe notebooklm-mcp
```

```bash
# macOS / Linux
which nlm; which notebooklm-mcp
```

**如果指令找不到，不是裝壞了，是 PATH 還沒生效。** 依序試：

1. **關掉終端機，開一個新的**，再試一次 —— 九成的狀況這樣就好了。
2. 不想關視窗的話，Windows 可以就地刷新 PATH：
   ```powershell
   $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
   ```
   macOS / Linux：`source ~/.zshrc`（或 `source ~/.bashrc`）
3. 還是找不到 → 讓 uv 把自己的 bin 目錄正式寫進 PATH，然後**重開終端機**：
   ```bash
   uv tool update-shell
   ```

---

### 步驟四：登入 Google 帳號

```bash
nlm login
```

> 🖐️ **這一步需要你本人操作**：瀏覽器會開啟 Google 登入頁面，選擇你的 Google 帳號完成授權。Agent 請停下來等使用者回報完成。

登入後驗證：
```bash
nlm doctor
```

**預期輸出**：診斷結果，認證狀態顯示正常。顯示未認證就重跑 `nlm login`。

> 📌 **指令會隨版本演進，本文件刻意不釘死版本號。**
> `nlm` 目前的完整指令清單，**一律以 `nlm --help` 的輸出為準**；子指令的選項用 `nlm <指令> --help` 查。
> 如果下面寫的某個指令在你的版本上不存在，先跑 `nlm --help` 看它改叫什麼，不要硬套本文件。

---

### 步驟五：設定 MCP 連接

#### 方式 A：讓工具自己寫（推薦，最省事）

```bash
nlm setup add opencode
```

它會自動找到 `~/.config/opencode/opencode.json`，把設定用**正確格式**寫進去，連逾時設定都一併處理好。

確認寫入結果：
```bash
nlm setup list
```

> 這個指令也支援其他 AI 工具（`claude-code`、`gemini`、`cursor` 等）。用 `nlm setup add --help` 看清單。

#### 方式 B：手動寫設定檔

編輯 `~/.config/opencode/opencode.json`，在 `mcp` 區塊加入：

```json
{
  "mcp": {
    "notebooklm": {
      "type": "local",
      "command": ["notebooklm-mcp"],
      "enabled": true,
      "timeout": 300000
    }
  }
}
```

**三個一定要對的地方：**

| 重點 | 說明 |
|------|------|
| `command` 填 **`notebooklm-mcp`** | **不是 `nlm`**。`nlm` 是給人打的 CLI，它沒有 `--transport` 這個選項；寫成 `["nlm", "--transport", "stdio"]` 會直接啟動失敗 |
| `command` 是**陣列** | OpenCode 的 `command` 只吃陣列，而且**沒有 `args` 欄位**（照抄 Claude Code 的寫法會壞，見 `00-env-setup` 的 MCP 通用守則） |
| **一定要加 `timeout`** | OpenCode 的 MCP 逾時預設只有 5000 毫秒（5 秒）。Gemini Notebook 的操作（產簡報、產影片、查詢）動輒數十秒到數分鐘，不加必逾時。`300000` = 5 分鐘 |

**如果加了 `timeout` 還是逾時**，再補一個全域設定（跟 `mcp` 同一層）：

```json
{
  "experimental": {
    "mcp_timeout": 300000
  }
}
```

**如果 PATH 有問題**（步驟三試過還是叫不動），可以在 `command` 直接填步驟三查到的**完整路徑**：

```json
"command": ["C:\\Users\\<你的帳號>\\.local\\bin\\notebooklm-mcp.exe"]
```

> Windows 路徑的反斜線在 JSON 裡要寫成兩條 `\\`。

---

### 步驟六：建立本地資料夾

在文件資料夾下建立以下目錄結構：

```
Documents/
  └── Gemini Notebook/
      ├── slides/          ← 簡報（Slide Deck）
      ├── infographics/    ← 資訊圖表
      ├── audio/           ← 音訊概覽
      ├── video/           ← 影片概覽
      ├── docs/            ← 報告文件
      ├── sheets/          ← 試算表
      ├── mindmaps/        ← 心智圖
      └── quizzes/         ← 測驗與閃卡
```

> 建立完成後，告知使用者資料夾的完整路徑。

---

### 步驟七：重啟並驗證連接

> 🖐️ **完全關閉 OpenCode，然後重新開啟**（改完設定檔沒生效時，先重開再說）。

**驗證指令（這是最可靠的方式，不要用「問問看 agent」猜）**：

```bash
opencode mcp list
```

**預期輸出**：清單裡有 `notebooklm` 這一列，狀態顯示 **`✓ connected`**。

連不上的話看細節：
```bash
opencode mcp debug notebooklm
```

看到 `✓ connected` 之後，再對 OpenCode 說：

```
請列出我所有的 Gemini Notebook 筆記本。
```

能列出（即使是空的）代表整條路都通了。

---

### 步驟八：功能測試

1. 建立一個名為「測試筆記本」的 notebook
2. 確認建立成功
3. 刪除這個測試筆記本
4. ✅ 「全部完成！OpenCode 已成功連接 Gemini Notebook。」

---

## 🤖 機器可判讀的成功訊號

**執行完請完整輸出下面這個區塊**，讓後續的 agent（或「一次全裝」那一包）可以直接判讀，不必讀懂中文說明。

```
=== OPENCODE_NOTEBOOKLM ===
result=PASS                    # PASS | PARTIAL | FAIL
os=windows                     # windows | macos | linux
uv=ok                          # ok | missing
nlm=<nlm --version 的輸出>       # 版本字串 | missing
mcp_server_bin=<完整路徑>        # notebooklm-mcp 的實際路徑 | missing
login=ok                       # ok | failed（依 nlm doctor）
mcp_config=ok                  # ok | failed（opencode.json 的 mcp.notebooklm 已寫入）
mcp_timeout=300000             # 實際寫入的毫秒數 | missing
mcp_connected=yes              # yes | no（opencode mcp list 顯示 ✓ connected）
notebook_list=ok               # ok | failed
local_folders=ok               # ok | skipped
=== END ===
```

**判定規則（agent 請照這個判斷，不要自行認定）：**

| 結果 | 條件 |
|------|------|
| `PASS` | `mcp_connected=yes` **且** `login=ok` **且** `notebook_list=ok` |
| `PARTIAL` | 工具裝好了（`nlm` 有版本號），但登入或連線至少缺一項 |
| `FAIL` | `nlm=missing`——工具根本沒裝起來 |

> ⚠️ `mcp_timeout=missing` 即使其他全綠，也請降為 `PARTIAL` 並提醒使用者補上——現場很可能在第一次跑大工作時才炸。

---

## 完成回報格式

```md
## Gemini Notebook 連接完成

| 項目 | 狀態 |
|------|------|
| uv | ✅ / ❌ |
| notebooklm-mcp-cli（`nlm` + `notebooklm-mcp`） | ✅ / ❌ |
| PATH 生效（兩個指令都叫得動） | ✅ / ❌ |
| Google 登入（`nlm doctor`） | ✅ / ❌ |
| MCP 設定寫入 opencode.json | ✅ / ❌ |
| `timeout` 已設定 | ✅ / ❌ |
| `opencode mcp list` 顯示 ✓ connected | ✅ / ❌ |
| 筆記本讀取測試 | ✅ / ❌ |
| 本地資料夾 | ✅ / ⏭️ 略過 |

- 安裝路徑：<notebooklm-mcp 的完整路徑>
- 需要你手動處理的事：（沒有 / 條列）
```

---

## 如果安裝失敗，如何重來

對 OpenCode 說：「上次 Gemini Notebook 懶人包執行失敗了，幫我清除設定，重新跑一次。」

復原步驟：

1. 從 `opencode.json` 的 `mcp` 區塊移除 `notebooklm` 項目
   （或用 `nlm setup remove opencode`）
2. 移除工具：`uv tool uninstall notebooklm-mcp-cli`（pip 路線用 `pip uninstall notebooklm-mcp-cli`）
3. **清除登入**：
   > ⚠️ **沒有 `nlm logout` 這個指令**，打了只會得到 `No such command`。
   - 想換 Google 帳號重登：`nlm login --clear`
   - 想整個清乾淨：刪掉設定資料夾
     - Windows：`%USERPROFILE%\.notebooklm-mcp-cli\`
     - macOS / Linux：`~/.notebooklm-mcp-cli/`
4. 從步驟零重新開始

---

## 常見問題

| 問題 | 解法 |
|------|------|
| `nlm: command not found` | **不是裝壞了，是 PATH 沒生效。** 關掉終端機重開；仍不行看步驟三第 2、3 點 |
| 我在 `.local\bin` 看到 `notebooklm-mcp.exe`，是不是裝錯地方？ | **沒有錯，那就是正確位置。** `uv tool install` 本來就裝在那裡，不要刪 |
| `uv: command not found` | Windows 重開 PowerShell；macOS/Linux `source ~/.zshrc` 或 `source ~/.bashrc` |
| 我照網路教學打了 `npm install nlm` | 裝到的是完全無關的套件。`npm uninstall -g nlm` 移掉，改用 `uv tool install notebooklm-mcp-cli` |
| 登入後 `nlm doctor` 顯示未認證 | 重新執行 `nlm login`，確認瀏覽器確實登入成功 |
| 瀏覽器沒有自動開啟 | 手動開 <https://notebooklm.google.com/> 確認已登入，或試 `nlm login --manual`（用 `nlm login --help` 確認你的版本支援哪些選項） |
| 想換另一個 Google 帳號 | `nlm login --clear` 重新登入；多帳號可用 `nlm login switch <profile>` |
| `opencode mcp list` 沒有 `notebooklm` 這一列 | 設定沒寫進去或寫錯層級。確認 `mcp` 在**頂層**，不要巢狀在別的項目裡；或直接跑 `nlm setup add opencode` 讓它幫你寫 |
| `opencode mcp list` 顯示連線失敗 / 逾時 | ①`command` 是不是誤填成 `nlm`？要填 `notebooklm-mcp` ②有沒有加 `"timeout": 300000`？③跑 `opencode mcp debug notebooklm` 看細節 |
| 設定檔改了但沒反應 | 完全關閉 OpenCode 再重開；仍不行用 `opencode mcp list` 確認它到底讀到什麼 |
| 大工作跑到一半被中斷（產影片、產簡報） | 逾時太短。把 `timeout` 調大，並補上全域的 `experimental.mcp_timeout`（見步驟五） |
| `ModuleNotFoundError: No module named 'notebooklm_tools'` | 安裝被弄壞了（多半是手動搬過檔案）。`uv tool uninstall notebooklm-mcp-cli` 後重裝，**不要**自己去搬 `.local\bin` 裡的檔案 |
| Windows 上指令格式錯誤 | 使用 PowerShell，不要用 CMD；不要用 `&&` 串指令，用 `;` |
| `nlm list` 之類的指令在 Windows 顯示亂碼或 `UnicodeDecodeError` | 設定 `$env:PYTHONIOENCODING = "utf-8"` 再跑。這是 Windows 中文版 cp950 編碼的已知問題，不影響 MCP 功能 |
| 網頁介面改叫別的名字了 | Google 有在調整 Gemini Notebook 的品牌與網址。工具會自動處理新舊網域，指令不用改；真的連不上再跑 `nlm doctor` 看診斷 |

---

## 相關懶人包

- `00-env-setup` — uv 的安裝權威版本，以及**MCP 通用守則**（逾時、設定格式、金鑰安全）
- `04-obsidian` — 另一條把素材收進第二大腦的路線
- `02-file-toolkit` — 把 Gemini Notebook 產出的檔案再加工
