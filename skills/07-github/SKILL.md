---
name: opencode-github
description: GitHub 三階段教學 — ①用 git clone 拿別人的懶人包 ②建自己的 repo 把作品 push 上去 ③換電腦或隔天 git pull 驗收。說「連接 GitHub」「clone 懶人包」「把作品放上 GitHub」「我怕弄丟」「換電腦拿回檔案」時載入。
---

# 連接 GitHub（三階段）

GitHub 在研習裡會出現三次，因為它不是一堂工具課，而是**三個痛點的解答**。
不要一次教完，也不要照順序一口氣做完——每一階段都在使用者「已經痛過」之後才出現。

| 階段 | 什麼時候出現 | 老師心裡的話 | 這階段只做 | 要帳號嗎 |
|------|-------------|-------------|-----------|---------|
| ① 消費端 | 研習一開場 | 「講師的東西怎麼拿？」 | `git clone` 別人的 repo | ❌ 不用 |
| ② 生產端 | 做出第一個作品後 | 「這個我怕弄丟」 | 註冊 → 建 repo → push | ✅ 要 |
| ③ 跨電腦驗收 | 隔天 / 換一台電腦 | 「真的還在嗎？」 | `git pull` 拿回來 | ✅ 要 |

> ⚠️ 給 AI agent 的提醒：使用者說「連接 GitHub」時，**先問他現在在哪一階段**，只做那一階段。
> 一次把三階段全部倒給零基礎老師，是這包最容易失敗的方式。

---

## 先備條件

- [ ] 已完成 **`00-env-setup`**（Git 與 GitHub CLI 都由 00 負責安裝，本包不處理安裝）
- [ ] 電腦有網路

驗證：

```powershell
git --version
gh --version
```

預期輸出（版號不同沒關係）：

```
git version 2.49.0.windows.1
gh version 2.65.0 (2025-01-06)
```

若出現「無法辨識 'git'／'gh' 這個詞彙是否為 Cmdlet」或 `command not found`
→ **回去跑 `00-env-setup`**，不要在這裡臨時安裝。

---

## 關鍵知識：GitHub 有「兩道門」

這一段是本包真正的價值，值得在研習裡花三分鐘講。

GitHub 其實是**兩個系統疊在一起**，要分別開門：

1. **Git 協定門** — `clone` / `push` / `pull`，負責「搬檔案」
2. **API 門** — 建 repo、開 issue、發 PR、設定 Pages，負責「管理你帳號上的東西」

不同的驗證方式，只開得了不同的門：

| 驗證方式 | Git 協定門（clone/push/pull） | API 門（建 repo、issue、PR） | 老師要付出的代價 |
|---------|------------------------------|------------------------------|-----------------|
| SSH key | ✅ | ❌ | 要產金鑰、貼公鑰、記 passphrase |
| PAT 手動貼 | ✅ | ✅ 但要自己管、易外流 | token 散在設定檔／剪貼簿，會過期、會外洩 |
| **gh CLI** | ✅ | ✅ | **登入一次，之後不用管** |

這解釋了一個常見的鬼打牆：很多教學只教 SSH key，老師 clone、push 都好好的，
結果一叫 agent「幫我建一個 repo」就失敗——因為那是 **API 門**，SSH key 開不了。

### 統一做法：gh CLI ＋ 一條很少人提的指令

`gh auth login` 預設只開 **API 門**（gh 自己的指令能用），
git 的 `push` / `pull` 仍然會另外跟你要帳號密碼。補上這一條，兩道門才會一起開：

```powershell
gh auth setup-git
```

它做的事：**把 git 的 HTTPS 憑證也接到 gh 的 token 上**。
之後 git 每次需要驗證，就自動去問 gh 拿 token。結果是：

- **登入一次，兩道門都開**
- token 存在**作業系統的憑證保管庫**（Windows 認證管理員 / macOS 鑰匙圈 / Linux keyring），**不落地成明文檔案**
- **AI agent 看不到 token**——agent 只是呼叫 `git` 和 `gh`，token 由作業系統遞交
- **跨 agent 完全一致**：OpenCode、Claude Code、Codex、AntiGravity 都是叫同一支 `gh`，設定一次全部通用

**老師版最小步驟（全程不用碰 token、不用產 SSH key）：**

```
gh auth login      →  選 GitHub.com → HTTPS → 瀏覽器登入輸入 8 碼
gh auth setup-git  →  把 git 的憑證也接上去
gh auth status     →  驗證
```

### 為什麼不建議走 GitHub MCP

GitHub MCP **只開 API 門**。裝了它，agent 能建 repo、開 issue，
但 `clone` / `push` / `pull` 這些 git 操作它辦不到，還是得回頭用 CLI。

於是你同時維護兩套東西：**MCP 的 token** ＋ **git 的憑證**——
多一份會過期、會外流、換 agent 要重設的設定，卻沒有少做任何一件事。
`gh` 一套就把兩道門都開了，所以**本包不裝 GitHub MCP**。

（例外：真的要大量自動化 issue／PR 分析時再考慮。研習用不到。）

---

# 第一階段（消費端）：把別人的懶人包抓下來

**目標**：拿到講師的教材。
**這階段不做**：不註冊帳號、不 push、不解釋什麼是 git、不講 commit。
對老師就一句話：「這是一個下載指令，會把整個資料夾抓到你電腦。」

### 1-1 選一個放東西的地方

**Windows（PowerShell）**：

```powershell
cd $HOME\Documents
```

> Mac / Linux：`cd ~/Documents`

### 1-2 抓下來

```powershell
git clone https://github.com/mathruffian-dot/opencode-lazy-packs.git
```

預期輸出：

```
Cloning into 'opencode-lazy-packs'...
remote: Enumerating objects: 120, done.
remote: Total 120 (delta 40), reused 118 (delta 38), pack-reused 0
Receiving objects: 100% (120/120), 84.21 KiB | 2.10 MiB/s, done.
Resolving deltas: 100% (40/40), done.
```

### 1-3 驗證

```powershell
cd $HOME\Documents\opencode-lazy-packs
dir
git remote -v
```

> Mac / Linux：`ls` 取代 `dir`

預期看到 `README.md`、`skills` 等檔案，以及：

```
origin  https://github.com/mathruffian-dot/opencode-lazy-packs.git (fetch)
origin  https://github.com/mathruffian-dot/opencode-lazy-packs.git (push)
```

**看到檔案 = 這階段就完成了。** 不要再往下講。

### 1-4 備援：clone 失敗就下載 ZIP

學校網路擋 git、或指令怎麼樣都跑不起來時：

1. 瀏覽器打開 repo 網址
2. 點綠色的 **Code** 按鈕 → **Download ZIP**
3. 解壓縮到 `文件` 資料夾

缺點：**日後對方更新，你要整包重下載**；用 `git clone` 的話一行 `git pull` 就更新（見第三階段）。

### 給老師的定心丸

抓下來的是**別人的**東西，你在自己電腦上怎麼改都不會影響到對方，
也**推不上去**（你沒有那個 repo 的權限）。這就是第一階段安全的原因——只能拿，不會弄壞。

---

# 第二階段（生產端）：把自己的作品放上去

**動機**：老師剛做出一份教材、一個網頁、一份簡報，心裡冒出「**這個我怕弄丟**」。
這時候才教這一段，接受度最高。

### 2-1 註冊 GitHub 帳號

到 <https://github.com/signup> 用瀏覽器自己註冊。

> ⚠️ **AI agent 不要代填帳號密碼、不要代為註冊、也不要代解驗證**，請老師自己在瀏覽器完成。
> 記下你的**帳號名稱**（後面會一直用到）。

### 🔴 註冊一定要在家裡先做，不要留到研習現場

GitHub 註冊會出現拼圖驗證（Arkose Labs FunCaptcha）。它**不是在考你解得對不對，是在給你的來源打分數**——分數低就一直發新題目，所以會出現「解了十題還在解」的無限循環，而且解對也沒用。

**扣分最重的一項，正好就是研習現場的樣子：同一個 IP 在短時間內多人註冊。** 整間教室共用一個對外 IP，大概第三、四個人開始就會被當成機器人在批次開帳號。

所以：

- ✅ **前一晚在自己家裡註冊**——每個人 IP 不同，這一關天然就過了
- ❌ 不要全班在現場一起註冊

**現場真的有人非註冊不可時，照這個順序救：**

1. **請他開手機熱點，用行動網路註冊**（換一個乾淨 IP，最有效）
2. 關掉 VPN
3. 改用無痕視窗，或換一個沒裝擴充功能的瀏覽器（廣告攔截器與隱私擴充會擋掉驗證用的網域、或清掉驗證 cookie，造成「解對了卻又跳一題」）
4. **不要連續猛按重試**，越試分數越低，等幾分鐘再來

> 講師請自備一支手機熱點當救援。這一關助教沒有別的辦法可以救。

### 2-2 登入 GitHub CLI

先看狀態：

```powershell
gh auth status
```

未登入時會顯示：

```
You are not logged into any GitHub hosts. To log in, run: gh auth login
```

登入：

```powershell
gh auth login
```

會問四個問題，照這樣選：

| 問題 | 選 |
|------|-----|
| What account do you want to log into? | **GitHub.com** |
| What is your preferred protocol for Git operations? | **HTTPS** |
| Authenticate Git with your GitHub credentials? | **Yes** |
| How would you like to authenticate GitHub CLI? | **Login with a web browser** |

接著終端機會顯示一組 **8 碼一次性驗證碼**（例如 `A1B2-C3D4`），
按 Enter 開瀏覽器 → 貼上驗證碼 → 按 **Authorize**。

> 想少選兩題可以直接用：`gh auth login --web --git-protocol https`

### 🔴 給 AI agent：這一行絕對不要在你自己的 shell 裡跑

**這是研習現場最常見的無限循環，成因如下：**

`gh auth login` 印出 8 碼之後**不會結束**——它要一直活著輪詢 GitHub，等你授權完才拿得到 token。
而你（agent）的指令通常有逾時（常見 2 分鐘）。**老師還在手機上打那八碼，`gh` 就已經被你的逾時砍掉了。**

結果是：老師在瀏覽器按了 Authorize、畫面顯示成功，**但已經沒有 `gh` 在等著收 token**。
你查 `gh auth status` 還是未登入 → 你以為失敗 → 重跑 `gh auth login` → **產生一組全新的 8 碼**
→ 老師螢幕上還是舊碼，怎麼打都不對 → 你再重跑 → 無限循環。

**實測（gh 2.89）**：無 TTY 時 `gh auth login` 會立刻印出碼與網址，然後持續執行；被逾時砍掉時退出碼是 `124`。

#### 正確做法 A：開一個獨立視窗讓它活著（Windows）

```powershell
Start-Process powershell -ArgumentList '-NoExit','-Command','gh auth login --web --git-protocol https'
```

這個視窗**不會被你的逾時影響**，`gh` 可以一直等。macOS／Linux 沒有等價的一行指令，請直接走做法 B。

#### 正確做法 B：請老師自己開終端機

跟他說：「請你自己開一個 PowerShell 視窗，貼上 `gh auth login`，照著它的指示做。**那個視窗先不要關。**」

#### 然後——輪詢，不要重跑

不論 A 或 B，接下來你要做的是**每隔幾秒查一次狀態**，直到成功：

```powershell
gh auth status
```

| 退出碼 | 意思 |
|-------|------|
| `0` | ✅ 登入成功，可以往下做 |
| `1` | 還沒好，**等一下再查** |

> 🔴 **看到「還沒好」不要重跑 `gh auth login`。**
> 重跑一次就作廢一組碼，老師會愈打愈亂。**第一次跑出來的那組碼，在他授權完之前一直有效。**

建議輪詢節奏：每 10 秒查一次，查到 5 分鐘。仍未成功再問老師「你那邊卡在哪一步？」，
而不是自己重跑。

驗證：

```powershell
gh auth status
```

預期輸出：

```
github.com
  ✓ Logged in to github.com account 你的帳號 (keyring)
  - Active account: true
  - Git operations protocol: https
  - Token: gho_************************
  - Token scopes: 'gist', 'read:org', 'repo', 'workflow'
```

**`Token scopes` 裡要有 `repo`**，否則後面建 repo 會失敗。

### 2-3 把 git 的憑證也接上去（本包最關鍵的一行）

```powershell
gh auth setup-git
```

**成功時沒有任何輸出**（沒消息就是好消息）。

驗證——**看所有層級，不要只看 `--global`**：

```powershell
git config --show-origin --get-all credential.helper
```

**下面三種結果任一種都算通過**：

| 你看到的 | 意思 | 判定 |
|---------|------|------|
| 含 `gh.exe' auth git-credential`（或 `/usr/bin/gh auth git-credential`） | `gh auth setup-git` 生效了 | ✅ 通過 |
| `manager`，來源是 `C:/Program Files/Git/etc/gitconfig` | **Git for Windows 內建的 Git Credential Manager (GCM)** 在管，一樣能用 | ✅ 通過 |
| 完全沒有輸出 | 沒有任何憑證管理員 | ⚠️ 這時才需要處理 |

> 🔴 **給 AI agent 的重要提醒：不要用 `git config --global --get-regexp credential` 當判斷依據。**
> Windows 用 winget 裝的 Git for Windows **預設就帶 GCM，而且設定寫在 system 層不是 global 層**。
> 只查 `--global` 會得到空結果，於是誤判成「setup-git 失敗」——但使用者的 `git push` 其實好好的。
> **這是本包最容易誤報的一項。**

**真正的判斷標準是 `git push` 會不會成功**，不是設定檔長什麼樣子。設定看不出來但 push 得上去 → 通過，不要叫使用者去修一個不存在的問題。

**兩種憑證管理員的差別**（都能用，不用二選一）：

| | `gh auth setup-git` | GCM（Git for Windows 內建） |
|---|---|---|
| 第一次 push 時 | 直接過，不問你 | **開瀏覽器**讓你授權一次，之後就記住了 |
| token 存哪 | 作業系統憑證保管庫 | 作業系統憑證保管庫 |
| 要另外裝嗎 | 不用（跟著 gh） | 不用（跟著 Git for Windows） |

兩個都沒有的時候，`git push` 才會退回去要帳號密碼——而 GitHub 早就不接受密碼推送，所以一定失敗。**`gh auth setup-git` 的價值是「保證有」，不是「唯一解」。**

### 2-4 設定 Git 使用者資訊

每一個 commit 都會**永久記錄**你的名字與 email，公開 repo 任何人都看得到。

先檢查：

```powershell
git config --global user.name
git config --global user.email
```

**沒有任何輸出 = 尚未設定。** 補上：

```powershell
git config --global user.name "王小明"
git config --global user.email "12345678+你的帳號@users.noreply.github.com"
```

> 💡 **強烈建議 email 用 GitHub 給的 `@users.noreply.github.com` 位址**，
> 不要用學校信箱或私人 Gmail——避免公開 repo 把你的信箱送給爬蟲。
>
> 去哪裡拿：GitHub 網頁 → 右上角頭像 → **Settings** → **Emails**
> → 勾選 **Keep my email addresses private** → 上面就會顯示你的
> `數字ID+帳號@users.noreply.github.com`。
>
> 進階（已登入 gh 的話可以一行拿到，跑不出來就走網頁那條路）：
> ```powershell
> gh api user --jq '"\(.id)+\(.login)@users.noreply.github.com"'
> ```

### 2-5 把作品變成一個 repo 並上傳

進到**你真的想備份的那個資料夾**（不要另外建測試資料夾，用真作品動機才強）：

```powershell
cd $HOME\Documents\我的教材
git init
git branch -M main
git add .
git commit -m "第一次備份我的教材"
```

指令在做什麼（給老師的白話版）：

| 指令 | 白話 |
|------|------|
| `git init` | 跟這個資料夾說「開始幫我記錄版本」 |
| `git branch -M main` | 把主線改叫 `main`（GitHub 的慣例，避免之後對不上） |
| `git add .` | 把資料夾裡所有東西「放進打包箱」 |
| `git commit -m "..."` | 蓋章存檔，引號裡寫你自己看得懂的說明 |

> `git init` 後若出現 `hint: Using 'master' as the name for the initial branch...`
> 那是**提示不是錯誤**，`git branch -M main` 就是在處理它。

建立 GitHub repo 並一次上傳：

```powershell
gh repo create 我的教材英文名 --private --source=. --push
```

參數逐字解釋：

- `我的教材英文名` — repo 名字，只用**英數字和 `-`**，不要中文、不要空白（例：`my-teaching-materials`）
- `--private` — 只有你自己看得到（**研習一律先用 private**，確定要公開再改）
- `--source=.` — 用「現在這個資料夾」的內容
- `--push` — 建好後立刻上傳

預期輸出：

```
✓ Created repository 你的帳號/my-teaching-materials on GitHub
  https://github.com/你的帳號/my-teaching-materials
✓ Added remote https://github.com/你的帳號/my-teaching-materials.git
✓ Pushed commits to https://github.com/你的帳號/my-teaching-materials.git
```

### 2-6 驗證

眼睛看（推薦，老師的「哇」就在這一刻）：

```powershell
gh repo view --web
```

瀏覽器會打開你的 repo，看到自己的檔案清單 = 成功。

純文字驗證（給 agent 判讀，不開瀏覽器）：

```powershell
git remote -v
git log --oneline -1
gh repo view --json name,visibility,url
```

預期：

```json
{"name":"my-teaching-materials","url":"https://github.com/你的帳號/my-teaching-materials","visibility":"PRIVATE"}
```

### 2-7 之後的日常（三行循環）

改完東西之後：

```powershell
git add .
git commit -m "說明你這次改了什麼"
git push
```

老師只要記這三行就夠用一整年。

---

# 第三階段（跨電腦驗收）：確認東西真的還在

**動機**：第二階段結束時，老師心裡其實半信半疑——「真的存上去了嗎？」
這一階段就是**把疑慮變成證據**。最好安排在**隔天**或**換一台電腦**時做。

### 情境 A：換一台電腦（或研習現場的第二台）

新電腦上依序做四件事：

```powershell
# 1. 先跑 00-env-setup 裝好 git 與 gh（略）
gh auth login          # 2. 登入（同 2-2）
gh auth setup-git      # 3. 接上 git 憑證（同 2-3）
cd $HOME\Documents
gh repo clone 你的帳號/my-teaching-materials    # 4. 抓回來
```

> 這裡剛好可以回扣「兩道門」：`gh repo clone` 走 **API 門** 找到 repo，
> 實際下載檔案走 **Git 協定門**。因為 `gh auth setup-git` 兩道都開了，所以一行就過。
> 私有 repo 也可以直接 `git clone <網址>`，同樣不用再輸入任何密碼。

### 情境 B：同一台電腦、隔天回來

```powershell
cd $HOME\Documents\my-teaching-materials
git pull
```

預期輸出（二擇一）：

```
Already up to date.
```

或（有更新時）：

```
Updating a1b2c3d..e4f5g6h
Fast-forward
 教案.md | 12 ++++++++----
 1 file changed, 8 insertions(+), 4 deletions(-)
```

### 驗收清單

```powershell
dir
git log --oneline -3
```

- [ ] 檔案數量與你當初上傳的一致
- [ ] `git log` 看得到你昨天寫的那句 commit 訊息
- [ ] 隨便打開一個檔案，內容是對的

三項都過 = **東西真的在雲端，不是只在你那台電腦。**

### 建議在研習現場做的「活體演練」

1. A 電腦改一行字 → `git add .` → `git commit -m "測試"` → `git push`
2. B 電腦（或隔壁老師的電腦）`git pull`
3. 改動出現了

這一刻老師才會真的相信「它是活的」，而不是又一個要背的指令。

### 順便回答「那我電腦壞掉怎麼辦？」

四行救回來：

```powershell
# 新電腦：跑完 00-env-setup 之後
gh auth login
gh auth setup-git
gh repo clone 你的帳號/my-teaching-materials
```

---

## 關於「刪除」：本包刻意不教

**本包不教 `gh repo delete`，也不教 `rm -rf`。** 理由有三：

1. **兩個都不可逆**。`gh repo delete` 刪雲端、`rm -rf` 刪本機，**都沒有資源回收桶**。
   對零基礎老師來說，一個打錯路徑就是災難。
2. **和本包的目的相反**。這整包在教「不要再弄丟東西」，
   中間插一個「會把東西弄不見」的指令，風險遠大於效益。
3. **技術上也會卡**：`gh auth login` 預設拿到的 token **沒有 `delete_repo` scope**，
   `gh repo delete` 會直接回 `HTTP 403`，要另外跑
   `gh auth refresh -h github.com -s delete_repo` 加權限——
   研習中途卡在這裡，時間全沒了。

**真的要刪**：請到 GitHub 網頁 → 你的 repo → **Settings** → 最下面 **Danger Zone** → Delete。
網頁會要求你**手動打出 repo 的完整名稱**才肯刪。這個「刻意的麻煩」就是保護，不要繞過它。

---

## 常見坑

| 症狀 | 原因 / 解法 |
|------|------------|
| `gh auth login` 跑下去沒反應、卡住 | 這是**互動指令**，agent 在自己的 shell 跑會被逾時砍掉。改用獨立視窗＋輪詢（見 2-2） |
| **打完八碼，agent 一直說沒收到驗證，一直給新碼** | **最常見的循環。** `gh` 被 agent 的逾時砍掉了，沒人在等 token。<br>解法：在獨立視窗跑 `gh auth login`，agent 只負責輪詢 `gh auth status`（exit 0 才算成功），**不要重跑 login**（見 2-2） |
| 八碼怎麼打都說錯 | 多半是**看到舊碼**——login 被重跑過，舊碼已作廢。以最新那個視窗顯示的碼為準 |
| **註冊時拼圖一直跳、解十題也過不了** | 不是你解錯——那是風險評分，**同一個 IP 多人註冊**會被當成機器人批次開帳號。<br>解法：**改用手機熱點**（換 IP，最有效）／關 VPN／換無痕視窗或沒裝擴充的瀏覽器／不要連續猛按。<br>**根本解法是前一晚在家裡註冊好**（見 2-1） |
| 8 碼驗證碼失效 | 裝置碼流程只有幾分鐘有效。**研習現場數十人同時登入時特別容易踩到**：講解一拖，碼就過期了。重跑 `gh auth login` 拿新碼即可 |
| 研習現場一堆人同時登不進去 | 幾十台電腦同時開 `github.com/login/device`＋網路壅塞。建議：**請大家前一天先註冊好帳號**，現場**分批（一排一排）**進行，講師先示範完整一輪再放行 |
| `git push` 跳視窗要帳號密碼 | 這台完全沒有憑證管理員。跑 `gh auth setup-git`（見 2-3）。GitHub 已不接受密碼推送，硬輸入也會失敗 |
| `git push` 開了瀏覽器要你授權 | **正常的**，那是 Git for Windows 內建的 GCM 在做事。授權一次之後就記住了，不用改成 gh |
| 明明 push 得上去，卻被判定「setup-git 沒做」 | 誤判。GCM 的設定在 **system 層**，用 `git config --global` 查不到。改用 `git config --show-origin --get-all credential.helper`（見 2-3） |
| `git commit` 說 `Author identity unknown` | 回去做 2-4 的 `git config --global user.name` / `user.email` |
| `gh repo create` 回 `HTTP 403` / 權限不足 | `gh auth status` 看 `Token scopes` 有沒有 `repo`；沒有就 `gh auth refresh -h github.com -s repo` |
| `remote origin already exists` | 這個資料夾已經接過別的 repo。`git remote -v` 看是哪一個，通常是你在**別人 clone 下來的資料夾**裡執行了 `gh repo create` |
| push 失敗說檔案太大 | GitHub **單檔 100MB 上限**。影片、大型 PDF 放雲端硬碟，repo 只放文字與程式；用 `.gitignore` 排除大檔 |
| 不小心把私人資料 push 上去 | **刪掉檔案再 commit 是沒用的，歷史紀錄還在。** 先把 repo 改成 private，如果外洩的是密碼或 API 金鑰，**立刻去原平台換掉那把金鑰**——這比清歷史重要一百倍 |
| 中文檔名在 GitHub 網頁顯示成亂碼編號 | 顯示問題不影響檔案內容；在意的話 `git config --global core.quotepath false` |

---

## 成功訊號（機器可判讀）

執行以下檢查，agent 依規則判定：

```powershell
gh auth status
git config --show-origin --get-all credential.helper
git config --global user.email
git -C <專案資料夾> remote -v
git -C <專案資料夾> log --oneline -1
```

判定規則：

| 檢查項 | 通過條件 |
|--------|---------|
| `GH_AUTH` | `gh auth status` 含 `Logged in to github.com` |
| `GH_SCOPE_REPO` | `Token scopes` 字串含 `repo` |
| `GH_SETUP_GIT` | credential helper 含 `gh ... auth git-credential` **或** 含 `manager`（GCM）。<br>兩者都沒有但 `STAGE2_PUSH` 成功 → 一樣記 `OK`（見下方註記） |
| `GIT_IDENTITY` | `user.name` 與 `user.email` 皆非空 |
| `STAGE1_CLONE` | 目標資料夾存在，且 `git remote -v` 有 `origin` |
| `STAGE2_PUSH` | `gh repo view --json url` 回得出網址，且 `git log` 至少一筆 commit |
| `STAGE3_PULL` | `git pull` 回傳 `Already up to date.` 或成功 Fast-forward |

最後輸出這一段（未執行的階段填 `SKIP`，失敗填 `FAIL:<原因>`）：

```
GITHUB_PACK_STATUS=OK
GH_AUTH=OK
GH_SCOPE_REPO=OK
GH_SETUP_GIT=OK
GIT_IDENTITY=OK
STAGE1_CLONE=OK
STAGE2_PUSH=OK
STAGE3_PULL=SKIP
```

> 只要有任何一項是 `FAIL`，`GITHUB_PACK_STATUS` 就必須是 `FAIL`。
>
> ⚠️ **唯一例外：`GH_SETUP_GIT`。**
> 憑證管理員的設定可能寫在 system 層（Git for Windows 內建的 GCM），查不到不代表壞掉。
> **只要 `STAGE2_PUSH` 是 `OK`，就把 `GH_SETUP_GIT` 記成 `OK`**——push 得上去就是最終證據。
> 不要因為這一項就叫使用者去修一個不存在的問題。

---

## 回報範本

```md
## GitHub 連接結果

**目前階段**：① 消費端 / ② 生產端 / ③ 跨電腦驗收

- 先備條件（00-env-setup）：git <版本> / gh <版本> ✅
- gh 登入：✅ 帳號 `<帳號>`（scopes 含 repo）
- gh auth setup-git：✅ 兩道門已開（credential helper 指向 gh）
- Git 使用者資訊：`<name>` / `<...@users.noreply.github.com>` ✅

### 階段一：消費端
- clone 目標：`<repo 網址>`
- 本機位置：`<路徑>`
- 結果：✅ 成功 / ⚠️ 改用 ZIP 下載 / ❌ 失敗（原因）

### 階段二：生產端
- repo：`<帳號>/<repo名>`（private）
- 網址：`<url>`
- 首次 commit：`<commit 訊息>`
- push：✅ 成功 / ❌ 失敗（原因）

### 階段三：跨電腦驗收
- 方式：換電腦 clone / 隔天 git pull
- 結果：✅ 檔案與 commit 紀錄都在 / ❌ 不一致（說明）

### 待處理
- <若有，列出；沒有就寫「無」>
```

