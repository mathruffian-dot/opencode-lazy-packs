---
name: opencode-env-setup
description: 從零把 OpenCode 環境裝好——OpenCode 桌面版與 CLI、Git、GitHub CLI、Node.js、uv，先偵測再安裝、每步驗證。說「建置環境」「安裝 OpenCode」「安裝開發環境」「環境建置」時載入。
---

# 環境建置（懶人包 #00）

**做完這一包，你的電腦會有：**

| 工具 | 是什麼 | 之後誰會用到 |
|------|--------|--------------|
| **OpenCode 桌面版** | 你要用的 AI 助手，有視窗介面 | 研習全程的主角 |
| **OpenCode CLI** | 同一個工具的終端機版本 | 進階操作、腳本自動化 |
| **Git** | 幫檔案存檔、記錄每次修改 | GitHub 那一包（`07-github`） |
| **GitHub CLI（gh）** | 讓 AI 直接操作 GitHub | GitHub 那一包（`07-github`） |
| **Node.js** | 一堆外掛（MCP）的執行環境 | 後續幾乎每一包 |
| **uv** | Python 工具的安裝器 | NotebookLM、檔案工具包等 Python 系工具 |

> ⚠️ **這一包裝完，OpenCode 還不會回答你。**
> 它只是「裝好了」，還沒有連上任何 AI 模型。
> **請務必接著做 #01：連接模型（`01-connect-model`）**，那一包才會讓它真的開始工作。

---

## 這包負責什麼、不負責什麼

| ✅ 這包負責 | ❌ 這包不負責（誰負責） |
|------------|----------------------|
| 安裝 OpenCode 桌面版 + CLI | 連接 AI 模型、API 金鑰 → **`01-connect-model`** |
| **安裝** Git 與 GitHub CLI | **登入** GitHub、設定 git 使用者資訊 → **`07-github`** |
| 安裝 Node.js、uv | 安裝各種 MCP 與外掛 → 後續各包 |
| 驗證每個工具真的能跑 | 教你怎麼用 OpenCode → 研習現場 |


---

## 開始之前：你現在是哪一種狀況？

這份文件是「**貼給 AI agent 執行**」用的。但如果你連 AI agent 都還沒有，會有雞生蛋問題。對號入座：

| 狀況 | 怎麼做 |
|------|--------|
| **A. 我什麼 AI 工具都沒有** | 先自己手動做完 **步驟 2**（裝 OpenCode 桌面版，就是下載點兩下）。打開 OpenCode 後，去做 **#01 連接模型**，回來把這份整份貼給它做完剩下的 |
| **B. 我已經有 Claude Code / Codex / 其他 agent** | 直接把這份整份貼給它，從步驟 1 開始跑 |
| **C. 我已經有 OpenCode 而且能對話了** | 直接把這份整份貼給 OpenCode，從步驟 1 開始跑 |

---

## 給 AI Agent 的執行原則

執行這份文件時請遵守：

1. **先偵測再安裝**：每個工具都先跑偵測指令，已經有的就跳過，不要重裝。
2. **每步都要驗證**：安裝完一定要跑驗證指令，看到預期輸出才算過。
3. **失敗不要硬闖**：某一項失敗就記下來繼續下一項，最後在回報裡標 ❌，不要整份中斷。
4. **需要人手動的地方要停下來**：例如需要管理員權限、需要在瀏覽器點同意，明白告訴使用者要做什麼，等他回覆。
5. **Windows 一律用 PowerShell**，不要用 CMD。
6. **不要在 PowerShell 用 `&&` 串指令**（Windows PowerShell 5.1 會直接語法錯誤），要用 `;`。
7. **不要對原生指令加 `2>$null` 或 `2>&1`**：PowerShell 5.1 會把它們的 stderr 包成錯誤物件，讓你把成功的安裝誤判成失敗。判斷成敗請看**輸出內容**，不要只看 exit code。
8. **不要幫使用者選付費方案、不要輸入任何金鑰**，那是 #01 的事。

---

## 步驟 1：全面偵測（先看你已經有什麼）

### Windows（PowerShell）

整段複製貼上：

```powershell
foreach ($c in 'opencode','git','gh','node','npm','uv') {
  $cmd = Get-Command $c -ErrorAction SilentlyContinue
  if ($cmd) { "[有] $c  ->  " + ((& $c --version) | Select-Object -First 1) }
  else { "[缺] $c" }
}
"--- 桌面版 ---"
winget list --id SST.OpenCodeDesktop --exact --accept-source-agreements
```

### macOS / Linux（終端機）

```bash
for c in opencode git gh node npm uv; do
  if command -v "$c" >/dev/null 2>&1; then
    echo "[有] $c  →  $("$c" --version 2>/dev/null | head -n1)"
  else
    echo "[缺] $c"
  fi
done
```

**判定**：把 `[缺]` 的項目記下來，後面只補這些。標 `[有]` 的一律跳過，不要重裝。

> 💡 **順便確認 winget 在不在**（Windows 才需要）：`winget --version`。
> 沒有的話 → 到 Microsoft Store 安裝「**應用程式安裝程式 / App Installer**」，或用各步驟附的「下載頁備案」。

---

## 步驟 2：安裝 OpenCode 桌面版（研習主線）

> 🎯 研習現場以桌面版為主。有視窗、可以拖檔案進去、看得到程式碼差異，對第一次接觸的老師最友善。

### Windows

```powershell
winget install --id SST.OpenCodeDesktop --exact --accept-source-agreements --accept-package-agreements
```

**驗證：**

```powershell
winget list --id SST.OpenCodeDesktop --exact
```

**預期輸出**：出現一行含有 `SST.OpenCodeDesktop` 和版本號的表格列。
**失敗長相**：`找不到任何符合輸入準則的已安裝套件`。

### macOS

```bash
brew install --cask opencode-desktop
```

驗證：`ls /Applications | grep -i opencode` → 應列出 OpenCode 應用程式。

### Linux

到 <https://opencode.ai/download> 下載 `.deb` 或 `.rpm`，然後：

```bash
# Debian / Ubuntu
sudo apt install ./opencode-desktop_*.deb
# Fedora / RHEL
sudo dnf install ./opencode-desktop-*.rpm
```

### 🖐️ 所有平台的共同備案

winget / brew 失敗（沒有管理員權限、公司電腦鎖住、網路擋住），一律改走這條：

1. 開瀏覽器到 <https://opencode.ai/download>
2. 選你的系統下載（Windows 是安裝檔、macOS 是 `.dmg`、Linux 是 `.deb` / `.rpm`）
3. 點兩下安裝

> ℹ️ 桌面版目前標示為 beta。日常使用穩定，但偶爾有小狀況屬正常；遇到怪事先重開，再看常見問題表。

---

## 步驟 3：安裝 OpenCode CLI（輔助）

> 🎯 CLI 是終端機版本，跟桌面版**可以並存**，設定檔共用。之後要寫自動化腳本、要在遠端機器上跑，會需要它。

### Windows

```powershell
winget install --id SST.opencode --exact --accept-source-agreements --accept-package-agreements
```

### macOS

```bash
brew install opencode
```

### Linux

```bash
curl -fsSL https://opencode.ai/install | bash
```

### 驗證（所有平台）

```
opencode --version
```

**預期輸出**：一組版本號（例如 `1.x.x`）。
**失敗長相**：`opencode : 無法辨識…` / `command not found` → 見下方 PATH 說明。

> ⚠️ **只選一種安裝方式，不要混裝。**
> 同一台電腦上如果既用 winget（或 brew）裝、又用 `npm install -g opencode-ai` 裝，PATH 上會有兩個 `opencode`，之後升級會很混亂。
> 本包一律以「winget / brew / 官方安裝腳本」為準；npm 版只在前三者都不可用時當備案。

> 🐧 **Linux/macOS 用 `curl … | bash` 的一句提醒**：這是官方文件公告的安裝方式，但「把網路上的腳本直接交給 shell 執行」本質上就是信任該網域。不放心的話，改用 brew（macOS）或到 GitHub Releases 下載執行檔。

> 🪟 **Windows 不能用 `curl … | bash`**：那是 bash 語法，PowerShell 跑不動。Windows 請用 winget。

---

## 步驟 4：安裝 Git

> 🎯 Git 幫你的檔案存檔、記錄每一次修改。之後 GitHub 那一包（`07-github`）要用它把教材推上 GitHub。
> **v2 起，Git 的安裝由本包負責，不再推給別包。**

**先偵測**：`git --version` 有輸出版本號就跳過這步。

### Windows

```powershell
winget install --id Git.Git --exact --accept-source-agreements --accept-package-agreements
```

> 🔒 **沒有管理員權限時**（學校電腦常見）：先試 `winget install --id Git.Git --exact --scope user`；仍失敗就到 <https://git-scm.com/downloads> 下載安裝檔，或請資訊組協助。

> 📌 **順便知道一件事：Windows 版的 Git 會一起裝「Git Credential Manager」(GCM)。**
> 它負責記住你的 GitHub 登入，第一次 `git push` 時會開瀏覽器讓你授權一次，之後就不用再輸入任何東西。
>
> 這件事在 `07-github` 那一包很重要：**GCM 的設定寫在 system 層，用 `git config --global` 查不到**。
> 查不到不代表沒有——很多人因此誤以為憑證沒設好，跑去修一個根本不存在的問題。詳見 `07-github` 的步驟 2-3。

### macOS

macOS 通常已內建 Apple 版 Git，先跑 `git --version`。真的沒有：

```bash
brew install git
```

（或執行 `xcode-select --install`，系統會跳視窗引導安裝。）

### Linux（Debian / Ubuntu）

```bash
sudo apt update && sudo apt install -y git
```

### 驗證

```
git --version
```

**預期輸出**：`git version 2.x.x`。

> 📌 **不要在這裡設定 `git config --global user.name / user.email`**——那屬於 GitHub 那一包（**`07-github`**），避免兩包重複問使用者同一件事。

---

## 步驟 5：安裝 GitHub CLI（gh）

> 🎯 `gh` 讓 AI 可以直接幫你開 repo、上傳、開網站，不用你自己點網頁。

**先偵測**：`gh --version` 有輸出就跳過。

### Windows

```powershell
winget install --id GitHub.cli --exact --accept-source-agreements --accept-package-agreements
```

### macOS

```bash
brew install gh
```

### Linux（Debian / Ubuntu）

```bash
sudo apt update && sudo apt install -y gh
```

找不到套件的話（較舊的發行版），改用 GitHub 官方來源：

```bash
sudo apt install -y curl
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list
sudo apt update && sudo apt install -y gh
```

### 驗證

```
gh --version
```

**預期輸出**：`gh version 2.x.x (…)`。

> 📌 **這一步不要登入**。`gh auth login` 需要開瀏覽器輸入驗證碼，是 GitHub 那一包（**`07-github`**）的內容。這裡只確認「裝好了、叫得動」。

---

## 步驟 6：安裝 Node.js

> 🎯 之後很多 MCP 外掛是用 `npx` 啟動的，那需要 Node.js。
> **OpenCode 本身不需要 Node.js**（它是獨立執行檔），所以先前 v1 把 Node 當成 OpenCode 的前置條件是寫錯的——它是「後面幾包」的前置條件。

**先偵測**：`node --version` 與 `npm --version` 都有輸出就跳過。

### 版本要求

裝**目前的 LTS（長期支援版）**即可，本文件刻意不釘死版本號——你在 2026 年裝到的 LTS 和 2027 年裝到的不會一樣，釘死只會讓文件過期。下面每個指令都會自動抓當下的 LTS。

### Windows

```powershell
winget install --id OpenJS.NodeJS.LTS --exact --accept-source-agreements --accept-package-agreements
```

### macOS

```bash
brew install node
```

### Linux（Debian / Ubuntu）

```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs
```

> `setup_lts.x` 會自動對應到「當下的 LTS」，不需要每年改文件（v1 寫死的 `setup_20.x` 已經過時了）。
> 若不想加外部套件庫，也可以 `sudo apt install -y nodejs npm`，但發行版內建的版本通常較舊。

### 驗證

```
node --version
npm --version
```

**預期輸出**：`node` 印出 `v` 開頭的版本號、`npm` 印出數字版本號，兩個都要有。

---

## 步驟 7：安裝 uv

> 🎯 `uv` 是 Python 工具的安裝器。NotebookLM MCP、教學檔案處理工具包等 Python 系工具都靠它安裝。
> 📌 **這是全套懶人包裡 uv 安裝的唯一權威版本**（v1 在三個檔案裡各寫一次，版本還不一致）。其他包需要 uv 時，一律回來看這一節。

**先偵測**：`uv --version` 有輸出就跳過。

### Windows（建議）

```powershell
winget install --id astral-sh.uv --exact --accept-source-agreements --accept-package-agreements
```

winget 不可用時的官方腳本備案：

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### macOS

```bash
brew install uv
```

### Linux / 通用備案

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### 驗證

```
uv --version
```

**預期輸出**：`uv 0.x.x`（或更高）。

---

## 步驟 8：重開終端機，然後做最終驗證

> 🚨 **這一步最常被跳過，也最常害人以為安裝失敗。**
> 剛剛裝的工具會把自己加進系統 PATH，但**已經開著的終端機視窗讀不到新的 PATH**。所以在舊視窗打 `git`、`gh` 一定會說「找不到」——不是沒裝好，是視窗太舊。

**做法**：關掉目前的 PowerShell / 終端機，**開一個新的**，再跑驗證。

Windows 若不想關視窗，可以就地刷新 PATH：

```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

macOS / Linux：`source ~/.zshrc`（或 `source ~/.bashrc`）。

### 最終驗證

**Windows（PowerShell，用 `;` 不是 `&&`）：**

```powershell
opencode --version; git --version; gh --version; node --version; npm --version; uv --version
```

**macOS / Linux：**

```bash
opencode --version && git --version && gh --version && node --version && npm --version && uv --version
```

**預期輸出**：六行，每行都是版本號，沒有任何「找不到指令」。

Windows 再補一行確認桌面版：

```powershell
winget list --id SST.OpenCodeDesktop --exact
```

---

## 步驟 9：把桌面版打開來看一眼

1. 從開始功能表 / 啟動台 / 應用程式清單開啟 **OpenCode**。
2. 能看到主視窗、不會閃退，這一包就算完成。
3. 這時候你跟它講話**它還不會回答**，因為還沒接上模型——這是正常的，不是壞掉。

---

## 📎 附錄：MCP 通用守則（後面每一包都會用到，先看一眼）

從 `03-notebooklm` 開始，幾乎每一包都要在 `~/.config/opencode/opencode.json` 的 `mcp` 區塊加一段設定。
下面六條是**每一包共用**的，各包不再重複說明。**裝 MCP 卡住的時候，先回來對這一頁。**

> 🎯 這一節現在不用動手，只要知道「有這一頁」就好。等你做到後面幾包再回來查。

---

### 1. 🔴 一定要加 `timeout`，不加幾乎必翻車

**OpenCode 的 MCP 逾時預設只有 5000 毫秒（5 秒）。**

用 `npx` 啟動的 MCP，第一次要先上網把套件下載下來，通常遠超過 5 秒 → OpenCode 直接判定連線失敗。
研習現場整間教室同時下載，只會更慢。**這是最容易整班一起卡住的地方。**

```json
{
  "mcp": {
    "example": {
      "type": "local",
      "command": ["npx", "-y", "某個-mcp-套件"],
      "enabled": true,
      "timeout": 300000
    }
  }
}
```

`300000` 毫秒 = 5 分鐘。套件下載過一次會被快取，之後啟動很快，但這個值留著不會有副作用，**每一台 MCP 都加上去就對了**。

> 💡 **加了還是逾時？** 部分版本的 OpenCode 對「單一 server 的 timeout」支援不穩定。再補一個全域設定（跟 `mcp` 同一層）：
> ```json
> {
>   "experimental": {
>     "mcp_timeout": 300000
>   }
> }
> ```

> 🖐️ **研習現場的預防針**：講師可以請大家**先在終端機手動跑一次** `npx -y 某個-mcp-套件`，讓套件先下載完（跑起來後按 Ctrl+C 中止即可）。之後 OpenCode 啟動就不會卡在下載。

---

### 2. ✅ 驗證用 `opencode mcp list`，不要用「重開問問看」

改完設定，**不要**靠「重開 OpenCode，問它有沒有工具」來判斷成功——問不出來也可能只是它沒想到要用。

```bash
opencode mcp list
```

**預期輸出**：每一台 server 一列，狀態顯示 **`✓ connected`**。看到 `✓` 才算成功，這是唯一可靠的判準。

其他相關指令：

| 指令 | 用途 |
|------|------|
| `opencode mcp add` | 互動式新增一台 server（會幫你寫成正確格式，不用背欄位） |
| `opencode mcp list` | 列出所有 server 與連線狀態 ← **驗證就看這個** |
| `opencode mcp auth <名稱>` | 對支援 OAuth 的遠端 server 登入（見第 6 點） |
| `opencode mcp logout <名稱>` | 清掉該 server 的登入憑證 |
| `opencode mcp debug <名稱>` | 連不上時看細節 |

---

### 3. ⚠️ 網路上抄來的設定多半是 Claude Code 的，直接貼一定壞

**OpenCode 的欄位名稱跟 Claude Code 不相容。** 這是照抄教學最常見的死法：

| 項目 | Claude Code 的寫法 | OpenCode 的寫法 |
|------|-------------------|-----------------|
| 區塊名稱 | `"mcpServers"` | **`"mcp"`** |
| 執行指令 | `"command": "npx"`（字串） | **`"command": ["npx", …]`（陣列）** |
| 參數 | `"args": ["-y", "套件名"]` | **沒有 `args` 欄位**，全部併進 `command` 陣列 |
| 環境變數 | `"env": { … }` | **`"environment": { … }`** |
| 遠端 server | `"type": "sse"` / `"http"` | **`"type": "remote"` + `"url"`** |

**❌ 錯誤（Claude Code 的格式，OpenCode 讀不懂）**

```json
{
  "mcpServers": {
    "example": {
      "command": "npx",
      "args": ["-y", "某個-mcp-套件"],
      "env": { "MY_TOKEN": "abc123" }
    }
  }
}
```

**✅ 正確（OpenCode 的格式）**

```json
{
  "mcp": {
    "example": {
      "type": "local",
      "command": ["npx", "-y", "某個-mcp-套件"],
      "environment": { "MY_TOKEN": "{env:MY_TOKEN}" },
      "enabled": true,
      "timeout": 300000
    }
  }
}
```

> 對照著看：`command` 和 `args` **合併成一個陣列**，`env` **改名叫** `environment`，最外層 **`mcpServers` 改成 `mcp`**。

---

### 4. 💬 OpenCode 的設定檔**可以**寫註解、**容許**尾逗號

OpenCode 用 JSONC（JSON with Comments）解析設定檔，所以下面這些都不會壞：

```jsonc
{
  "mcp": {
    // 這行註解是合法的
    "example": {
      "type": "local",
      "command": ["npx", "-y", "某個-mcp-套件"],
      "timeout": 300000,   // 行末註解也可以
    },                     // 最後一項後面多一個逗號，也不會壞
  }
}
```

檔案也可以直接存成 `opencode.jsonc`。

> 📌 **如果你看到某份教學說「opencode.json 絕對不能有註解、最後一項不能有逗號」——那是在講一般 JSON，對 OpenCode 不適用。**
> 但**其他格式錯誤照樣會壞**：大括號少一個、引號沒成對、Windows 路徑的反斜線沒寫成兩條 `\\`。
> 所以設定改完，還是要用 `opencode mcp list` 驗一次。

---

### 5. 🔑 金鑰不要寫明文，用 `{env:變數名}`

**研習現場的設定檔會投影在大螢幕上，明文金鑰等於當場公開**，還會被拍照。
OpenCode 支援從環境變數讀值：

```json
{
  "mcp": {
    "example": {
      "type": "local",
      "command": ["npx", "-y", "某個-mcp-套件"],
      "environment": { "MY_API_KEY": "{env:MY_API_KEY}" },
      "enabled": true,
      "timeout": 300000
    }
  }
}
```

**怎麼設環境變數：**

Windows（PowerShell，設完要**重開終端機**才生效）：
```powershell
[System.Environment]::SetEnvironmentVariable("MY_API_KEY", "你的金鑰", "User")
```

macOS / Linux（寫進 `~/.zshrc` 或 `~/.bashrc`）：
```bash
export MY_API_KEY="你的金鑰"
```

也可以改成讀檔：`"MY_API_KEY": "{file:~/.secrets/my-api-key}"`。

> 🖐️ 不管用哪種，**都不要把含金鑰的檔案推上 GitHub**（見 `07-github`）。
> 🖐️ 金鑰打錯時，`opencode mcp list` 通常仍顯示 connected（連得上、但呼叫會被拒絕）。真正要驗的是實際叫一次工具。

---

### 6. 🌐 遠端 MCP（`type: "remote"`）最省事：完全不用管 token

有些服務直接提供「遠端 MCP」——不用在你電腦上裝任何東西，也不用申請金鑰。OpenCode 支援 OAuth 自動註冊：

```json
{
  "mcp": {
    "example-remote": {
      "type": "remote",
      "url": "https://某服務的網址/mcp",
      "enabled": true,
      "timeout": 300000
    }
  }
}
```

然後跑一次：

```bash
opencode mcp auth example-remote
```

瀏覽器會開啟授權頁，**按同意就好**。不需要申請、複製、貼上任何 token，也沒有金鑰外洩的問題。

- 要換帳號 / 重來：`opencode mcp logout example-remote`，再 auth 一次
- 授權卡住：`opencode mcp debug example-remote`

> 💡 **只要某個服務有提供遠端 MCP，優先走這條。** 對零程式基礎的老師來說，它比「本機安裝工具 + 申請金鑰 + 貼進設定檔」簡單一個數量級。

---

### 📋 加完任何一台 MCP，照這四步收尾

1. **對格式**：`command` 是陣列、**沒有** `args`、環境變數欄位叫 `environment`、外層是 `mcp`
2. **加逾時**：`"timeout": 300000`（還逾時就再補 `experimental.mcp_timeout`）
3. **完全關閉 OpenCode 再重開**（不是關視窗，是整個結束程式）
4. **`opencode mcp list`** → 看到 `✓ connected` 才算完成

---

## ➡️ 下一步：#01 連接模型

環境有了，但 OpenCode 還沒有「腦」。**接著做懶人包 `01-connect-model`**，把 AI 模型接上去，它才會開始回答你、開始幫你做事。

```bash
npx skills add mathruffian-dot/opencode-lazy-packs --skill 01-connect-model -g -y
```

或直接對 AI 說：「幫我跑懶人包 01：連接模型」。

> 模型選擇、金鑰申請、費用、額度，全部在 #01 說明，本包不涉及。

---

## 🤖 機器可判讀的成功訊號

**執行完請完整輸出下面這個區塊**，讓後續的 agent（或「一次全裝」那一包）可以直接判讀，不必讀懂中文說明。

```
=== OPENCODE_ENV_SETUP ===
result=PASS            # PASS | PARTIAL | FAIL
os=windows             # windows | macos | linux
opencode_desktop=ok    # ok | missing | skipped
opencode_cli=1.18.5    # 版本號 | missing
git=2.55.0             # 版本號 | missing
gh=2.96.0              # 版本號 | missing
node=v24.18.0          # 版本號 | missing
npm=11.6.2             # 版本號 | missing
uv=0.11.32             # 版本號 | missing
shell_restarted=yes    # yes | no
next=01-connect-model
=== END ===
```

**判定規則（agent 請照這個判斷，不要自行認定）：**

| 結果 | 條件 |
|------|------|
| `PASS` | `git` / `gh` / `node` / `npm` / `uv` 全部有版本號，**且** `opencode_desktop=ok` 或 `opencode_cli` 有版本號（至少一個） |
| `PARTIAL` | OpenCode（桌面或 CLI 任一）有了，但其他工具至少缺一個 |
| `FAIL` | 桌面版和 CLI 都沒裝成功 |

> `PASS` 只代表「工具都裝好了」，**不代表 OpenCode 可以使用**。可用與否由 #01 判定。

---

## 完成回報範本（給人看的）

```md
## 環境建置完成

| 項目 | 狀態 | 版本 |
|------|------|------|
| OpenCode 桌面版 | ✅ 已安裝 / ⚠️ 已補裝 / ❌ 失敗 | — |
| OpenCode CLI | ✅ / ⚠️ / ❌ | v… |
| Git | ✅ / ⚠️ / ❌ | … |
| GitHub CLI | ✅ / ⚠️ / ❌ | … |
| Node.js | ✅ / ⚠️ / ❌ | … |
| uv | ✅ / ⚠️ / ❌ | … |

- 作業系統：Windows / macOS / Linux
- 終端機已重開：是 / 否
- 需要你手動處理的事：（沒有 / 條列）
- 下一步：懶人包 #01 連接模型 → OpenCode 才會開始回答你
```

（✅ 本來就有 ／ ⚠️ 這次補裝的 ／ ❌ 失敗，失敗一定要寫原因。）

---

## 如果裝壞了，如何重來

對 AI 說：「上次環境建置沒跑完，幫我檢查現況，把缺的補上。」

單獨移除某個工具重裝：

```powershell
# Windows
winget uninstall --id SST.OpenCodeDesktop --exact
winget uninstall --id SST.opencode --exact
```

```bash
# macOS
brew uninstall --cask opencode-desktop
brew uninstall opencode
```

移除後回到步驟 1 重新偵測即可。**Git、Node.js、uv 沒必要移除重裝**——版本舊不影響本包，真的要更新用 `winget upgrade --id <ID>` 或 `brew upgrade <名稱>`。

---

## 常見問題

| 問題 | 解法 |
|------|------|
| 裝完了但打指令說「找不到」 | **九成是這個**：關掉終端機重開（見步驟 8）。仍不行才查 PATH |
| `winget` 這個指令不存在 | Windows 10 1809 以上內建。沒有就到 Microsoft Store 裝「應用程式安裝程式 / App Installer」，或改用各步驟的下載頁備案 |
| winget 說需要系統管理員權限 | 先試加 `--scope user`；不行就用官方下載頁的安裝檔，或請學校資訊組協助 |
| PowerShell 打 `A && B` 直接語法錯誤 | Windows PowerShell 5.1 不支援 `&&`，改用 `;` 分隔 |
| `curl … \| bash` 在 Windows 跑不動 | 那是 bash 語法。Windows 請用 winget，不要在 PowerShell 硬跑 |
| PowerShell 說指令碼執行被停用 | uv 的腳本備案已含 `-ExecutionPolicy ByPass`；請照原樣整段貼上，不要拆開 |
| 桌面版打開後打字沒反應 | 正常，還沒接模型 → 去做 #01 |
| 電腦裡有兩個 `opencode`，版本不一樣 | 混裝了（winget + npm）。留一個、移除另一個，見步驟 3 的警告 |
| Windows 使用者名稱是中文，某些工具報錯 | 少數工具對非 ASCII 路徑有問題。若卡住，改把專案放在 `C:\projects\` 這類純英文路徑下 |
| 學校網路擋下載 / 一直逾時 | 校園防火牆或 Proxy。改用手機熱點，或請資訊組把 `opencode.ai`、`github.com`、`npmjs.org`、`astral.sh` 開通 |
| Linux 上 `apt` 找不到 `gh` | 用步驟 5 的 GitHub 官方套件來源那段 |
| 一切都裝好了，但 OpenCode 還是不回答 | 這包**本來就不會**讓它回答。請做 #01 連接模型 |

