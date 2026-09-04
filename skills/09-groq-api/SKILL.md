---
name: opencode-groq-api
description: 用 Groq 的免費 API 做「語音轉字幕」與「逐字稿清洗」，並把整條流程包成可重複使用的技能。說「語音轉字幕」「影片上字幕」「做逐字稿」「Whisper」「Groq」「把錄音整理成講義」「把 API 變成工具」時載入。
---

# 把 API 變成自己的工具（Groq）

> 官方文件查證日期：**2026-07-27**
> 依據：<https://console.groq.com/docs/speech-to-text>、<https://console.groq.com/docs/api-reference>、<https://console.groq.com/docs/rate-limits>、<https://console.groq.com/docs/models>、<https://console.groq.com/docs/your-data>、<https://console.groq.com/docs/billing-faqs>、<https://console.groq.com/docs/quickstart>

---

## 🔴 先看這裡：隱私紅線（這一段是本包最重要的內容）

**這一包做的每一件事，都會把你的聲音檔送到別人的伺服器上。**

不是「處理完就消失」那種送，是**整個音檔上傳、在美國的機器上跑完、再把文字傳回來**。你電腦上的麥克風錄到什麼，就送出去什麼。

### 這三類錄音，一律不要上傳

| ❌ 不要上傳 | 為什麼 |
|------------|--------|
| **含學生聲音的課堂錄音** | 學生是未成年人。**要不要讓孩子的聲音上傳到境外雲端，這件事你不能代替家長同意。** |
| **家長會談、親師溝通、電話錄音** | 對方通常不知道有錄音，更不知道會被上傳 |
| **輔導紀錄、個案會議、學生私下談話** | 這類內容一旦外流，受傷的是孩子，而且你會有法律責任 |

順便三條也不要：**唸到學生姓名的點名錄音**、**學生口說測驗的作答錄音**、**任何你不確定裡面有誰在講話的檔案**。

### 這些可以上傳

- 你自己一個人講課、備課、口述的錄音（**只有你的聲音**）
- 你自己的公開演講、研習分享
- 你自己錄的旁白、口播稿
- 公開影片（新聞、公播教材、你自己頻道的影片）

### 一句話的判斷標準

> ## 🔴 「這個音檔如果明天出現在網路上，會不會有人受傷？會不會需要通報？」
>
> 只要答案是「會」或「不確定」——**不上傳**。沒有例外，不看時間趕不趕。

### ⚠️ 最容易出事的地方：檔案結尾

**課堂錄音最常見的意外不是內容，是「忘記按停止」。**

你以為錄的是自己講課的 40 分鐘，實際上錄音一直開到下課、開到學生跑上來問問題、開到你跟隔壁老師聊完。

> 🔴 **規矩：上傳前先跳到檔案的最後 30 秒聽一遍。**
> 這一步花 30 秒，可以擋掉這一包 90% 的真實風險。

### Groq 官方的資料政策（查證 2026-07-27）

講完紅線再講政策，順序不能反——**政策再好，也不會讓紅線消失。**

| 項目 | 官方說法 | 出處 |
|------|---------|------|
| 推論請求的資料會保留嗎 | **預設不保留**（"By default, Groq does not retain customer data for inference requests."） | Your Data in GroqCloud |
| 會拿去訓練模型嗎 | **不會**。服務協議明訂：未經客戶明示同意，不得將輸入／輸出用於訓練或微調模型 | Groq Services Agreement |
| 有例外嗎 | **有**。為了排除故障、調查濫用，可能**暫時**記錄輸入與輸出，最長保留 **30 天**（法律要求時可能更久） | Your Data in GroqCloud |
| 可以完全關掉嗎 | **可以**。所有客戶都能在 Console 的 **Data Controls** 開啟 **Zero Data Retention（ZDR）** | Your Data in GroqCloud |
| 免費方案有比較差嗎 | **官方文件沒有為免費方案另訂較寬鬆的規定**，寫的是同一套 | 同上 |

**建議研習前先做一件事**：登入 Console → **Settings** → **Data Controls** → 開啟 **Zero Data Retention**。花 10 秒，之後所有請求都適用。

> 🔴 **但是——開了 ZDR，紅線一條都不會消失。**
> 政策是**廠商的承諾**，不是「你的檔案沒有離開過你的電腦」的技術保證。
> 中間還有網路、還有你的電腦、還有你等一下會不會把 srt 檔傳到 LINE 群組。
> **真正安全的做法只有一個：那個檔案根本不要上傳。**

### 真的非處理不可怎麼辦

有些內容就是不能上雲端，但你又真的需要逐字稿。三條路：

1. **請當事人自己來**：讓學生／家長自己用手機的語音輸入打字，你只收文字
2. **本機跑**：在自己電腦上跑離線的語音辨識模型（不連網、不上傳）。這條**不在本研習範圍**，也不在本包
3. **不做**：這是完全合理的選項。省下的 20 分鐘，遠遠比不上一次個資事件

---

## 這一包在研習裡的位置：作品⑤「把 API 變成自己的工具」

**第三天上午。這一包做的是「內部工具」——只有你自己在電腦上用，還不碰網頁。**

前面幾包是「把別人做好的東西接進來」（NotebookLM、Obsidian、Supabase）。這一包不一樣：

> **你會拿一個原始的 API，做出一個以後可以一直用的東西。**

這是整套研習裡第一次「呼叫外部服務、拿回結果、包成工具」。學會這個模式，之後接任何服務都是同一套邏輯。

### 🔑 這一包成不成功，判斷標準只有一個

| | 跑一次 | **變成工具** |
|---|-------|-------------|
| 你做的事 | 貼一串指令，看到 srt 跑出來 | 對 OpenCode 說一句人話，它自己做完 |
| 換一份新素材 | **要回來翻講義、重貼指令** | **同一句話再說一次就好** |
| 下個月還會用嗎 | 不會，因為忘了怎麼跑 | 會，因為只要記得那句話 |
| 這一包的驗收 | ❌ 不算完成 | ✅ **這才算完成** |

**所以步驟六（包成 Skill）不是加分題，是這一包的正題。**
前面五個步驟都只是為了搞清楚「那句人話背後要做什麼」。

> 📌 下午的 `10-netlify` 會把這個工具再往外推一層：**變成一個網頁，讓別人也能用**。
> 今天上午先把「自己能用」做穩，下午才有東西可以搬上網。

---

## 這一包會幫你做什麼

兩條主線，都用同一把金鑰：

| 線 | 做什麼 | 輸入 → 輸出 | 用到的服務 |
|----|--------|-------------|-----------|
| **A 轉字幕** | 把聲音變成有時間碼的字幕檔 | `.mp4` / `.mp3` / `.m4a` → **`.srt`** | Groq 的 Whisper |
| **B 文字清洗** | 把逐字稿整理成能發給學生看的講義 | `.srt` / `.txt` → **`.md`** | Groq 的文字模型 |

A 線的產物剛好是 B 線的原料。合起來就是：**一段錄音 → 一份講義**。

---

## 🚨 這一包的四個陷阱（先看完再動手）

| # | 陷阱 | 症狀 | 一句話解法 |
|---|------|------|-----------|
| ① | **Groq 沒有 `response_format=srt`** | 照 OpenAI 的教學打，回 `400` | 拿 `verbose_json`，**自己組 srt**（步驟三附小程式） |
| ② | **免費方案單檔上限 25 MB** | 一支 40 分鐘影片直接被打回 | 先用 ffmpeg 壓成 16k 單聲道，超過 25 MB 就切段 |
| ③ | **Whisper 的中文不分繁簡** | 辛苦轉完，字幕是簡體 | `language=zh` 只能到「中文」；繁體要另外處理（步驟三 A5） |
| ④ | **切段後時間碼要自己加偏移** | 第二段字幕全部從 00:00 開始，整份對不上畫面 | 用**每一段的實際長度**累加，不要用名目的「10 分鐘」 |

陷阱 ① 是最花時間的一個——**網路上 95% 的 Whisper 教學都是寫給 OpenAI 的，那邊有 srt，Groq 沒有。** 你會以為自己打錯字。

---

## 免費方案（不用綁信用卡）

> ⚠️ 以下為 **2026-07-27** 查到的數字。**服務商隨時會調整，以官方頁面為準**（本節最後教你怎麼自己查）。

### 要不要綁卡

| 問題 | 答案（查證 2026-07-27） |
|------|------------------------|
| 註冊要不要信用卡 | **不用。** 用 Google／GitHub／Email 註冊，直接就能產金鑰 |
| 免費用量要不要信用卡 | **不用。** 免費方案是「限速」不是「限時」，沒有試用到期這回事 |
| 什麼時候才需要卡 | **只有升級 Developer 方案時**。官方文件寫得很清楚：升級需要「a valid payment method（credit card, US bank account, or SEPA debit account）」 |

> 🔴 **研習現場的規矩：整個過程不會有任何畫面要你輸入卡號。**
> 只要看到要求付款方式的畫面，代表你**走進了升級流程**——**按上一頁退出來**，不要輸入。
> 這一包主線完全不需要 Developer 方案。

### 免費額度（Whisper 語音）

| 項目 | 免費方案 | 換算成老師聽得懂的話 |
|------|---------|---------------------|
| 單檔大小上限 | **25 MB** | 壓成 16k 單聲道 FLAC 後，約 **25～30 分鐘**的音訊 |
| 每分鐘請求數 | 20 次 | 一次跑 20 個檔沒問題 |
| 每日請求數 | 2,000 次 | 用不完 |
| **每小時音訊秒數** | 7,200 秒 | **每小時可以轉 2 小時的音訊** |
| **每日音訊秒數** | 28,800 秒 | **每天可以轉 8 小時的音訊** |
| 最短音訊 | 0.01 秒 | — |
| **最低計費長度** | **10 秒** | ⚠️ 見下方 |

> ⚠️ **「最低計費 10 秒」這條會咬人。**
> 意思是：**再短的檔，也至少吃掉 10 秒額度。**
> 所以「把一堂課切成 200 個小片段各自送一次」是最浪費的做法——200 個 3 秒的檔會被算成 2,000 秒。
> **正確做法：切大段（10 分鐘一段），不要切小段。**

（付費的 Developer 方案單檔上限是 100 MB。本包不走那條，寫在這裡只是讓你看到別人的教學寫 100 MB 時不會困惑。）

### 免費額度（文字模型，B 線用）

| 模型 | 每分鐘請求 | 每日請求 | **每分鐘 token** | 每日 token |
|------|-----------|---------|-----------------|-----------|
| `llama-3.3-70b-versatile` | 30 | 1,000 | **12,000** | 100,000 |
| `openai/gpt-oss-120b` | 30 | 1,000 | 8,000 | 200,000 |
| `llama-3.1-8b-instant` | 30 | 14,400 | 6,000 | 500,000 |

> 🚨 **這裡有一個沒人講、但一定會撞到的坑：**
>
> **「每分鐘 token」小得驚人。** 一小時的中文演講逐字稿大約 9,000～12,000 字，換算下來**遠遠超過** 12,000 token 的每分鐘上限。
>
> 也就是說：**你不可能把一整份逐字稿一次丟給它清洗。** 一定會拿到 `429`。
>
> **解法：分段送。** 步驟五的小程式已經內建分段（預設一段 1,200 字）和自動重試，照著跑就好。

### 怎麼自己查最新的（模型會改名、額度會變）

| 想查什麼 | 去哪查 |
|---------|--------|
| **現在有哪些模型**（最可靠） | 在終端機跑：`curl.exe https://api.groq.com/openai/v1/models -H "Authorization: Bearer $env:GROQ_API_KEY"` |
| 模型說明頁 | <https://console.groq.com/docs/models> |
| **我這個帳號的實際限額** | Console → **Settings** → **Limits**（這裡看到的才是你的，文件是通則） |
| 語音的檔案大小上限 | <https://console.groq.com/docs/speech-to-text> |
| 資料政策 | <https://console.groq.com/docs/your-data> |

> 📌 **本包刻意不寫任何價格。** 免費方案用不到，而寫死的價格只會在半年後變成錯誤資訊。
> 模型名稱也一樣：如果下面哪個模型名在你跑的時候不存在了，**先跑上面那行 `/v1/models` 看它改叫什麼**，不要硬套本文件。

---

## 選哪個 Whisper 模型：研習主線用 `whisper-large-v3`

> 註：作者的 `audio-to-srt` 與 youtube-workflow 技能預設用 `whisper-large-v3-turbo`（求速度，word-level 時間碼一樣有）。兩者都能用，本包的推薦理由見下。

Groq 上目前有兩個（查證 2026-07-27）：

| 模型 | 官方標示的準確度 | 速度 | 語言 |
|------|-----------------|------|------|
| **`whisper-large-v3`** | 錯字率 **10.3%** | 189 倍即時 | 多語（含中文） |
| `whisper-large-v3-turbo` | 錯字率 **12%** | 216 倍即時 | 多語（含中文） |

### 🔴 推薦：`whisper-large-v3`。理由是一個很多人沒注意到的事實——

> **在免費方案下，這兩個模型的額度完全一樣**（都是 20 次/分、7,200 音訊秒/小時、28,800 音訊秒/日）。
>
> turbo 的賣點是「比較便宜」。**但免費方案本來就不用錢，所以那個賣點在這裡等於零。**
>
> 而速度差多少？189 倍 vs 216 倍。一支 30 分鐘的錄音，一個要 9.5 秒、一個要 8.3 秒。**你根本感覺不到。**
>
> 結論：**免費用戶沒有任何理由選 turbo。用準的那個。**

**什麼時候才換 turbo**：你要一次跑幾十個檔、而且真的坐在旁邊等；或者以後升級成付費、開始在意費用。

> 📌 **關於繁體中文的表現**：**Groq 官方沒有公布任何中文專屬的準確度數字**，Whisper 官方也只有整體多語數字。
> 本包推薦 `whisper-large-v3` 的依據是「整體錯字率較低」＋「免費方案額度相同」，**不是**任何中文的實測比較。
> 實際上兩個模型對台灣國語的表現都算堪用，主要問題不是聽錯字，是**專有名詞**（人名、地名、課本術語）和**繁簡**——這兩件事都靠步驟五的清洗解決，換模型幫助不大。

---

## 選哪個文字模型：主線用 `llama-3.3-70b-versatile`

| 模型 | 為什麼選／不選 |
|------|---------------|
| **`llama-3.3-70b-versatile`** | ✅ **主線**。中文品質在免費模型裡較穩，而且**每分鐘 token 上限 12,000 是免費方案裡最大的**——清洗逐字稿吃的就是這個 |
| `openai/gpt-oss-120b` | 🟡 **備援**。主線那個掛掉或改名時用這個 |
| `llama-3.1-8b-instant` | 🟡 **量大時用**。每日 500,000 token 最寬鬆，但中文品質明顯較弱，適合「整批粗清、之後自己再看過」 |

> ⚠️ 模型名稱**會改**。跑之前先用 `/v1/models` 確認一次（見上方「怎麼自己查最新的」）。

---

## 🤔 等一下——文字清洗為什麼不直接叫 OpenCode 做就好？

這是研習現場一定會有人問的問題，而且**問得很對**。

你已經在 `01-connect-model` 幫 OpenCode 接了模型。要清洗逐字稿，把檔案丟給它說「幫我整理成講義」，它就做完了。**幹嘛還要多申請一把 Groq 金鑰、多寫一支程式？**

答案是三件事：

| 理由 | 說明 |
|------|------|
| ① **這一包在教「模式」，不是在教「清洗」** | 呼叫 API 拿結果，這個模式之後接任何服務都通用。清洗只是拿來練手的題目 |
| ② **語音那條沒有替代方案** | OpenCode 的模型**聽不懂聲音檔**。A 線非走 API 不可，而 B 線用同一把金鑰、同一套寫法，順著學最省力 |
| ③ **下午要變成網頁** | `10-netlify` 會把這個工具放到網路上給別人用。**網頁不能呼叫你電腦裡的 OpenCode**，只能呼叫 API。今天寫的這支程式，下午直接搬上去 |

> 💡 所以：**日常自己用，直接叫 OpenCode 清洗完全 OK，不必每次都走 API。**
> 但這一包要你走一次 API，是為了讓你手上有一個「離開 OpenCode 也能跑」的東西。

---

## 先備條件

- [ ] 已完成 **`00-env-setup`** 與 **`01-connect-model`**
- [ ] 已完成 **`02-file-toolkit` 的 B 段**（要用 `ffmpeg` 和 `ffprobe` 壓縮、切檔、量長度）
- [ ] 建議完成 **`07-github`**（金鑰不外洩這件事在那一包有完整說明）
- [ ] 有網路
- [ ] 手上有一份**符合上面隱私紅線**的音訊或影片檔

驗證：

```powershell
ffmpeg -version; ffprobe -version
```

兩個都印出版本 = 過。

> ⚠️ **`ffprobe` 常常缺席。** 如果只裝了 yt-dlp 自帶的那套 FFmpeg，`ffprobe` 可能不在 PATH 上。
> 補裝系統版：`winget install --id Gyan.FFmpeg -e`（沒有管理員權限就加 `--scope user`）。
> mac：`brew install ffmpeg`／Linux：`sudo apt install -y ffmpeg`。
> 真的裝不起來也還有救——步驟四的小程式有備援做法，看那一節。

### 📁 先建一個工作資料夾，名字用英數字

```powershell
New-Item -ItemType Directory -Force "$HOME\Documents\groq-subtitle"
```

> 🚨 **資料夾和檔名先不要用中文。**
> Windows 的命令列（PowerShell 5.1）傳中文檔名給 `ffmpeg`、`curl` 時偶爾會變亂碼，你會看到「找不到檔案」但檔案明明就在。
> **做法：轉檔前先把來源改成 `lesson01.mp4` 這種名字，全部跑完再改回中文。**
> 這一條看起來很瑣碎，但研習現場每一梯都有人卡在這裡半小時。

---

# 步驟一：申請 Groq 金鑰

> 🖐️ 全程在瀏覽器手動做。**AI agent 不要代為註冊、不要代填任何欄位。**

### 1-1 註冊

1. 開 <https://console.groq.com>
2. 選 **Continue with Google** 或 **Continue with GitHub**（建議用 `07-github` 那個 GitHub 帳號，之後好管理），也可以用 Email
3. 沒有等待清單、不用審核、**不會問你要信用卡**

### 1-2 建立金鑰

左側選單 **API Keys** → **Create API Key** → 取一個看得懂的名字（例如 `研習-字幕工具`）→ 建立。

> 🔴 **金鑰只會完整顯示這一次。**
> 關掉視窗之後**看不到第二次**，只能刪掉重產一把。
> **現在就複製起來，貼到你的密碼管理器**（不是貼到記事本、不是貼到 LINE 給自己）。

金鑰長得像 `gsk_` 開頭的一長串。

### 1-3 順手開啟 Zero Data Retention

**Settings** → **Data Controls** → 開啟 **Zero Data Retention**。

10 秒的事，之後所有請求都適用。（再說一次：**開了它，前面那些紅線一條都不會消失。**）

### 1-4 驗證：金鑰是活的嗎

先做完步驟二設定好環境變數，再回來跑這一行（步驟二的驗證裡有）。

---

# 步驟二：金鑰要放哪裡（🚨 這一步做錯，金鑰會上 GitHub）

## 🔴 三條絕對規則

> **① 金鑰不寫進任何 `.md` 檔案裡** —— 包含你等一下要做的那個 Skill 檔。
> **② 金鑰不寫進任何會被 `git add` 的檔案裡。**
> **③ 金鑰不貼進聊天視窗**（包含貼給 AI agent 看）。

**理由**：研習的設定檔會投影在大螢幕上、會被拍照；而 GitHub 上有機器人專門在掃新推上去的金鑰，**掃到的速度比你發現的速度快**。

## 主線做法：設成環境變數（推薦，整台電腦設一次）

環境變數的好處是：**金鑰不存在於任何一個檔案裡**，所以永遠不可能被 commit。

**Windows（PowerShell）**

```powershell
[System.Environment]::SetEnvironmentVariable("GROQ_API_KEY", "貼上你的金鑰", "User")
```

> 設完**一定要重開終端機**（連 OpenCode 也要完全關掉重開），新的環境變數才會生效。

**macOS / Linux**（寫進 `~/.zshrc` 或 `~/.bashrc`，然後 `source` 一次）

```bash
echo 'export GROQ_API_KEY="貼上你的金鑰"' >> ~/.zshrc
source ~/.zshrc
```

### 驗證（🔴 這行刻意不印出金鑰本身，可以安心投影）

**Windows**

```powershell
if ($env:GROQ_API_KEY) { "KEY_SET len=" + $env:GROQ_API_KEY.Length } else { "KEY_MISSING" }
```

**macOS / Linux**

```bash
if [ -n "$GROQ_API_KEY" ]; then echo "KEY_SET len=${#GROQ_API_KEY}"; else echo "KEY_MISSING"; fi
```

看到 `KEY_SET len=<某個數字>` = 過。看到 `KEY_MISSING` → 沒重開終端機，或變數名打錯。

### 驗證金鑰是活的

```powershell
curl.exe https://api.groq.com/openai/v1/models -H "Authorization: Bearer $env:GROQ_API_KEY"
```

- 回傳一大段 JSON（裡面看得到 `whisper-large-v3`）→ ✅ **金鑰可用**，順便看到現在有哪些模型
- 回 `invalid_api_key` → 金鑰複製時少了一截，或前後有多餘的空白，重複製一次

> Windows 一定要打 `curl.exe`（有 `.exe`），否則會叫到 PowerShell 內建的別名，行為完全不同。

## 專案做法：`.env` ＋ `.gitignore`（下午的 `10-netlify` 會用到）

如果你的工具要放進一個「會推上 GitHub 的專案資料夾」，用 `.env`：

**先建 `.gitignore`（🔴 先建這個，再建 `.env`，順序不能反）**

```
.env
*.key
credentials.*
desktop.ini
*.tmp
```

**再建 `.env`**

```
GROQ_API_KEY=貼上你的金鑰
```

**驗證 `.env` 真的被擋掉了：**

```powershell
git status --short
```

> 🔴 **輸出裡出現 `.env` 就是失敗**——代表 git 還看得到它，下一次 `git add .` 就會把金鑰送上 GitHub。
> 回頭檢查 `.gitignore` 是不是拼錯、是不是放在 repo 根目錄。

**更嚴格的檢查（agent 請跑這一行）：**

```powershell
git check-ignore -v .env
```

有輸出（會告訴你被 `.gitignore` 第幾行擋掉）= ✅ 過。沒有輸出 = ❌ 沒擋到。

> 📌 **關於 Google 雲端硬碟**：`.env` 放在雲端硬碟資料夾裡會被同步到你的雲端帳號——這通常可以接受（那是你自己的帳號）。
> **但它跟 `.gitignore` 是兩回事**，雲端同步不會擋 git。兩件事都要做。

## 如果你要在 `opencode.json` 裡引用金鑰

本包**用不到**這件事（我們是自己呼叫 API，不是把 Groq 接成 OpenCode 的模型供應商）。但萬一你之後要：

```json
"environment": { "GROQ_API_KEY": "{env:GROQ_API_KEY}" }
```

> 🔴 **寫 `{env:GROQ_API_KEY}`，不要寫實際那串字。**
> 這是 `00-env-setup` 附錄「MCP 通用守則」第 5 條的規矩，本包完全沿用。

## 🚨 萬一金鑰不小心外流了

**刪掉檔案、刪掉訊息、清掉 git 歷史——全部沒有用。** 金鑰已經在別人手上了。

> ## 唯一有效的處置：回 Console → API Keys → **把那把金鑰刪掉，重新產一把**。
>
> 這件事做完之前，其他都不用做。做完之後，記得把環境變數也換成新的。

---

# 步驟三：A 線 — 把聲音變成字幕（`.srt`）

## A1. 先把檔案壓小（🚨 這裡處理陷阱 ②）

免費方案單檔上限 **25 MB**。一支手機錄的 30 分鐘影片動輒好幾百 MB，直接送一定被打回。

**官方建議的做法：降到 16kHz 單聲道、轉成 FLAC。** 這樣做**不會影響辨識準確度**——Whisper 本來就只吃 16kHz，多出來的取樣率和第二個聲道，送上去也是被丟掉。

**Groq 官方文件給的指令（原封不動）：**

```powershell
ffmpeg -i "lesson01.mp4" -ar 16000 -ac 1 -map 0:a -c:a flac "clean.flac"
```

| 參數 | 意思 |
|------|------|
| `-ar 16000` | 取樣率降到 16kHz（Whisper 的原生規格） |
| `-ac 1` | 併成單聲道 |
| `-map 0:a` | **只要聲音**，把影像整個丟掉（影片檔的體積 95% 是影像） |
| `-c:a flac` | 轉成 FLAC（無損壓縮） |

**壓完看大小：**

```powershell
"{0:N1} MB" -f ((Get-Item "clean.flac").Length / 1MB)
```

```bash
# macOS / Linux
du -h clean.flac
```

**經驗值：16k 單聲道 FLAC 大約每分鐘 0.8～1 MB。**
所以 **25 MB ≈ 25～30 分鐘**。超過就要切段 → **跳到步驟四**。

> 🟡 **真的不想切檔的備案**：改用 mp3 低位元率，一小時大約壓到 14 MB。
> ```powershell
> ffmpeg -i "lesson01.mp4" -ar 16000 -ac 1 -map 0:a -c:a libmp3lame -b:a 32k "clean.mp3"
> ```
> 代價是有損壓縮，中文的準確度會**略微**下降。**本包主線仍走 FLAC ＋ 切段**，這個備案只在時間真的不夠時用。

## A2. 呼叫 API

**Windows（PowerShell，整段是一行，長是正常的）**

```powershell
curl.exe https://api.groq.com/openai/v1/audio/transcriptions -H "Authorization: Bearer $env:GROQ_API_KEY" -F "file=@clean.flac" -F "model=whisper-large-v3" -F "language=zh" -F "temperature=0" -F "response_format=verbose_json" -o "clean.json"
```

**macOS / Linux**

```bash
curl https://api.groq.com/openai/v1/audio/transcriptions \
  -H "Authorization: Bearer $GROQ_API_KEY" \
  -F "file=@clean.flac" \
  -F "model=whisper-large-v3" \
  -F "language=zh" \
  -F "temperature=0" \
  -F "response_format=verbose_json" \
  -o "clean.json"
```

| 參數 | 為什麼要 |
|------|---------|
| `model=whisper-large-v3` | 見前面「選哪個模型」 |
| `language=zh` | **一定要加**。官方明講：指定語言會**同時提升準確度和速度**。不加的話它要先花時間猜語言，中英夾雜的課堂錄音很容易猜錯 |
| `temperature=0` | 要它照聽到的寫，不要自由發揮 |
| `response_format=verbose_json` | **時間碼在這裡面**。用 `json` 或 `text` 拿不到時間碼 |
| `-o clean.json` | 存成檔案。不存的話一大堆 JSON 會直接噴在螢幕上 |

### 🚨 A3. 陷阱 ①：不要寫 `response_format=srt`

**OpenAI 的 Whisper API 可以直接回 `.srt`。Groq 不行。**

官方 API 文件寫得很明確——`response_format` 的 **Allowed values 只有 `json`、`text`、`verbose_json`**。

打 `srt` 或 `vtt` 會拿到 `400` 錯誤。而網路上 95% 的 Whisper 教學都是寫給 OpenAI 的，照抄過來就會撞這一關。

> 🔴 **請直接記這句話**：
> **Groq 只給你「時間碼 ＋ 文字」，`.srt` 這個檔案要你自己組。**
>
> 聽起來很麻煩，其實只是把 JSON 換個排版而已——下面那支小程式一次解決，而且以後長檔切段也是同一支。

## A4. 把 `verbose_json` 組成 `.srt`

在工作資料夾建立 `make_srt.py`（**這支程式不需要安裝任何套件，全部用 Python 內建的**）：

```python
# make_srt.py — 把 Groq 的 verbose_json 組成 .srt
# 用法：uv run python make_srt.py 輸出.srt 第1段.json 第2段.json ...
# 只有一段就只傳一個 json；多段會自動接上時間碼偏移（見步驟四）
import json, os, subprocess, sys

def fmt(t):
    """秒數 → SRT 的 00:00:00,000 格式"""
    ms = int(round(t * 1000))
    h, ms = divmod(ms, 3600000)
    m, ms = divmod(ms, 60000)
    s, ms = divmod(ms, 1000)
    return f"{h:02d}:{m:02d}:{s:02d},{ms:03d}"

def real_seconds(path):
    """用 ffprobe 量音訊檔的實際長度（秒）。量不到就回 None"""
    try:
        out = subprocess.run(
            ["ffprobe", "-v", "error", "-show_entries", "format=duration",
             "-of", "csv=p=0", path],
            capture_output=True, text=True, check=True).stdout.strip()
        return float(out)
    except Exception:
        return None

out_path, json_files = sys.argv[1], sys.argv[2:]
if not json_files:
    sys.exit("SRT_FAIL 沒有指定任何 json 檔")

offset, idx, blocks = 0.0, 1, []
for jf in json_files:
    with open(jf, encoding="utf-8") as f:
        data = json.load(f)
    for seg in data.get("segments", []):
        text = (seg.get("text") or "").strip()
        if not text:
            continue
        blocks.append(
            f"{idx}\n{fmt(seg['start'] + offset)} --> {fmt(seg['end'] + offset)}\n{text}\n")
        idx += 1
    # 🚨 陷阱 ④：偏移量用「這一段的實際長度」累加，不要用名目的 600 秒
    audio = os.path.splitext(jf)[0] + ".flac"
    dur = real_seconds(audio) if os.path.exists(audio) else None
    if dur is None:                       # ffprobe 不在？改用 API 回傳的長度
        dur = float(data.get("duration") or 0.0)
    offset += dur

with open(out_path, "w", encoding="utf-8") as f:
    f.write("\n".join(blocks))
print(f"SRT_OK segments={idx - 1} files={len(json_files)} out={out_path}")
```

**跑它：**

```powershell
uv run python make_srt.py "lesson01.srt" "clean.json"
```

（`uv` 來自 `00-env-setup`。沒有 `uv` 就用 `python make_srt.py ...`，這支程式只用內建模組，任何 Python 3 都跑得動。）

**成功的樣子**：印出 `SRT_OK segments=<某個數字> files=1 out=lesson01.srt`，而且 `segments` 不是 0。

## A5. 🚨 陷阱 ③：字幕變成簡體怎麼辦

**Whisper 的語言代碼只有「中文（`zh`）」，沒有「繁體中文」。** 它自己決定要吐繁體還是簡體，同一份檔案跑兩次結果還可能不一樣。

兩條路，**建議兩條都做**：

### 路線 A（一次搞定，但 Windows 有編碼坑）：用 `prompt` 參數暗示它

Whisper 的 `prompt` 參數是用來「示範風格」的（上限 224 token）。**你用繁體字寫這個提示，它輸出繁體的機率會大幅提高。**

先用**記事本**建立 `prompt.txt`（存檔時編碼選 **UTF-8**），內容一行：

```
以下是繁體中文的課堂錄音逐字稿。
```

然後在 A2 的指令**尾巴加一個參數**：

```
-F "prompt=<prompt.txt"
```

> 🚨 **為什麼要用檔案，不直接把中文寫在指令裡？**
> Windows PowerShell 5.1 把中文參數傳給 `curl.exe` 時**會用系統的 cp950 編碼**，送出去常常變亂碼——而且不會報錯，你只會覺得「這個 prompt 好像沒效」。
> `-F "欄位名=<檔名"` 是 curl 的語法，意思是「這個欄位的值從檔案讀」，**直接讀原始位元組，繞過整個編碼問題**。
> macOS / Linux 沒有這個問題，直接寫中文也可以。

> 📌 **這是社群通用做法，不是官方保證。** 有效但不是 100%。所以還要有路線 B。

### 路線 B（零風險保底）：讓 OpenCode 只改字、不准動時間碼

把產出的 `.srt` 丟給 OpenCode，說：

```
幫我把這個 .srt 檔的字幕文字全部改成繁體中文（台灣用語），
順便修掉明顯的錯字。

🔴 硬規定：
- 時間碼那幾行（00:00:00,000 --> 00:00:03,120）一個字都不准改
- 字幕的行數與編號不准增減
- 不要合併或拆開任何一段
```

> 🔴 **最後那三條一定要講，而且要講得像規定。**
> 不講的話，AI 很常「順手」幫你合併太短的句子、重新分段——結果字幕跟畫面**整份對不上**，而且看起來完全正常，你要放完整支影片才會發現。

**建議做法：路線 A 先做（省事），路線 B 當保底（順便修錯字）。**

## A6. ✅ A 線驗收：換一份新素材，同一句話能不能再跑一次

**這才是這一包的驗收，不是「跑出一個 srt」。**

| # | 做什麼 | 通過的樣子 |
|---|--------|-----------|
| 1 | 用**播放器**開啟原始影片，把 `.srt` 拖進去 | 字幕**跟嘴巴對得上**（不是「有字幕就好」） |
| 2 | 跳到影片**最後面**看 | 最後一句字幕的時間**接近影片總長**（差很多 = 時間碼歪了，看步驟四） |
| 3 | **換一支完全不同的錄音**，從 A1 重跑一次 | 一樣跑得完 |
| 4 | 🔴 **步驟六做完之後**：對 OpenCode 說「**幫我把 `<新檔名>` 轉成字幕**」 | **它自己做完全部，你沒有再貼任何指令** ← **這一項才是真的通過** |

第 4 項現在還做不到（Skill 還沒包）。**先記著它，這是你等一下要達成的目標。**

---

# 步驟四：長音檔怎麼切、時間碼怎麼接（🚨 這裡處理陷阱 ④）

**只有超過 25 MB（約 25～30 分鐘）才需要這一步。** 短檔請直接跳到步驟五。

## 官方建議 vs 本包做法

Groq 官方建議的是「切成**有重疊**的片段，各自轉，合併時處理重疊的部分」，並附了一份 Cookbook 範例。

**本包不走重疊那條**，理由很實際：重疊之後要判斷「這兩段重複的文字哪一份才對、接縫在哪」，這是整條流程裡最容易寫錯、也最難驗證的部分。對老師來說划不來。

> **本包做法：不重疊、切固定長度、用「每一段的實際長度」累加時間碼。**
> 代價是接縫處**可能斷在句子中間**（少一兩個字或斷句怪怪的）。
> 但這件事**步驟五的清洗會順手修掉**，而且比起「整份時間碼歪掉」，這個代價便宜太多。

## 4-1 切檔（先壓再切，順序不能反）

```powershell
# ① 先壓成 16k 單聲道 FLAC（跟 A1 一模一樣）
ffmpeg -i "lesson01.mp4" -ar 16000 -ac 1 -map 0:a -c:a flac "clean.flac"

# ② 再切成每段 10 分鐘（600 秒）
ffmpeg -i "clean.flac" -f segment -segment_time 600 -c:a flac "chunk_%03d.flac"
```

會產生 `chunk_000.flac`、`chunk_001.flac`、`chunk_002.flac`…

> **為什麼是 10 分鐘？**
> 10 分鐘的 16k 單聲道 FLAC 約 8～10 MB，離 25 MB 上限**有很大的安全邊際**。
> 切太小反而浪費（記得「最低計費 10 秒」那條），切太大會踩到上限。**10 分鐘是甜蜜點。**

> **為什麼要先壓再切？**
> 這樣每一段的規格完全一致，量長度、接時間碼都不會出意外。
> 先切再壓的話，每段會各自被 ffmpeg 處理一次，長度可能出現零點幾秒的差異。

## 4-2 逐段呼叫 API

**Windows（PowerShell）**

```powershell
Get-ChildItem "chunk_*.flac" | Sort-Object Name | ForEach-Object {
  $out = $_.BaseName + ".json"
  Write-Host "轉換中：$($_.Name)"
  curl.exe https://api.groq.com/openai/v1/audio/transcriptions -H "Authorization: Bearer $env:GROQ_API_KEY" -F "file=@$($_.Name)" -F "model=whisper-large-v3" -F "language=zh" -F "temperature=0" -F "response_format=verbose_json" -o $out
  Start-Sleep -Seconds 3
}
```

**macOS / Linux**

```bash
for f in chunk_*.flac; do
  echo "轉換中：$f"
  curl https://api.groq.com/openai/v1/audio/transcriptions \
    -H "Authorization: Bearer $GROQ_API_KEY" \
    -F "file=@$f" -F "model=whisper-large-v3" -F "language=zh" \
    -F "temperature=0" -F "response_format=verbose_json" \
    -o "${f%.flac}.json"
  sleep 3
done
```

> 💡 中間的 `Start-Sleep 3` / `sleep 3` 是刻意的：免費方案每分鐘 20 次，這樣跑絕對不會撞到上限。
> 一支 1 小時的錄音會切成 6 段，全部跑完大約 1 分鐘。

**檢查每一段都有回來：**

```powershell
Get-ChildItem "chunk_*.json" | Select-Object Name, Length
```

> 🔴 **有哪個 `.json` 大小只有幾百 bytes → 那一段失敗了，裡面裝的是錯誤訊息不是字幕。**
> 用記事本打開看它寫什麼，對照本文件最後的「常見卡關」。**不要就這樣往下做**，那一段的字幕會整段消失。

## 4-3 合併（🚨 這裡就是陷阱 ④）

### 為什麼不能直接接起來

**每一段轉回來的時間碼，都是從 `00:00:00` 開始的。**

也就是說第二段的第一句話，API 說它在 0 分 3 秒——但它**實際上**在整支影片的 10 分 3 秒。
直接把 6 段接起來，你會得到一份「前 10 分鐘有 6 份字幕、後面 50 分鐘全空」的檔案。

### 正確做法：加偏移量。但**不能用「10 分鐘」硬算**

> 🔴 **這是本包最細、也最多人做錯的一條：**
>
> `ffmpeg` 的 `-segment_time 600` 是「**大約** 600 秒」，不是「剛好 600 秒」——實際切點會落在音訊的封包邊界上。
> 每段差個零點幾秒，**六段累積起來就是好幾秒**。
> 字幕一開始對得很好，越到後面越飄——**而這種錯誤最難發現，因為前 10 分鐘完全正常。**
>
> **正解：用 `ffprobe` 量每一段的實際長度，一段一段累加。**
> `make_srt.py` 已經內建這個做法（`real_seconds()` 那個函式），你不用自己算。

**直接跑（把所有 json 按順序一次餵進去）：**

**Windows**

```powershell
$files = (Get-ChildItem "chunk_*.json" | Sort-Object Name).Name
uv run python make_srt.py "lesson01.srt" $files
```

**macOS / Linux**

```bash
uv run python make_srt.py "lesson01.srt" $(ls chunk_*.json | sort)
```

> 🔴 **順序絕對不能錯。** `chunk_010.json` 一定要排在 `chunk_002.json` 後面。
> 上面用了 `Sort-Object Name` / `sort`，而檔名是三位數補零的（`%03d`），所以字母排序就是正確順序。
> **如果你自己手動列檔名，請務必檢查順序**——順序一錯，整份字幕會亂到看不出規律。

> 📌 **`ffprobe` 真的裝不起來的備援**：`make_srt.py` 量不到長度時，會自動改用 API 回傳的 `duration` 欄位。
> 那個數字是 Groq 算的，**通常也很準**，只是不像 `ffprobe` 那樣是直接量檔案。能裝 `ffprobe` 還是裝。

## 4-4 驗證時間碼有沒有歪（🔴 這一步不要跳過）

**這是唯一能抓到時間碼漂移的方法：**

| # | 做什麼 | 通過的樣子 |
|---|--------|-----------|
| 1 | 看原始影片的**總長度** | 記下來，例如 `58:20` |
| 2 | 用記事本打開 `.srt`，**捲到最後一行** | 最後一個時間碼**很接近** `58:20`（差 5 秒內算正常） |
| 3 | 把 `.srt` 拖進播放器，跳到**影片最後 2 分鐘** | 字幕**還對得上嘴巴** |
| 4 | 跳到**每一個接縫處**（10 分、20 分、30 分…） | 那附近的字幕沒有明顯偏移 |

> 🔴 **只驗開頭是抓不到這個 bug 的。** 時間碼漂移的特徵就是「前面完全正常」。
> **一定要看最後面。**

**歪掉了怎麼辦**：

| 症狀 | 原因 |
|------|------|
| 最後一句只到 `10:00` 左右 | 只餵了一個 json 進去，或檔案順序被打亂 |
| 越後面偏差越大 | `ffprobe` 沒裝，而且 API 的 `duration` 也不準 → 補裝系統版 FFmpeg 再跑一次 `make_srt.py`（不用重轉，json 都還在） |
| 中間整段字幕消失 | 某一段的 `.json` 其實是錯誤訊息（回 4-2 檢查每個 json 的大小） |

---

# 步驟五：B 線 — 把逐字稿清洗成講義

## 5-1 先把 srt 變成純文字

字幕檔裡有編號和時間碼，那些不需要送去清洗（**還會白白吃掉寶貴的 token 額度**）。

```powershell
uv run python -c "import re,sys;t=open('lesson01.srt',encoding='utf-8').read();lines=[l for l in t.splitlines() if l.strip() and not l.strip().isdigit() and '-->' not in l];open('lesson01.txt','w',encoding='utf-8').write('\n'.join(lines))"
```

或者更簡單——**直接把 `.srt` 丟給 OpenCode 說「把時間碼和編號拿掉，只留文字，存成 lesson01.txt」**。

## 5-2 建立清洗程式

在同一個資料夾建立 `clean.py`（一樣**不需要安裝任何套件**）：

```python
# clean.py — 把逐字稿清洗成可讀講義（Groq Chat Completions）
# 用法：uv run python clean.py 逐字稿.txt 講義.md
import json, os, sys, time, urllib.error, urllib.request

API   = "https://api.groq.com/openai/v1/chat/completions"
MODEL = "llama-3.3-70b-versatile"   # 會改名！先跑 /v1/models 確認
LIMIT = 1200                        # 每段送幾個字。免費方案 TPM 很小，不要調大

KEY = os.environ.get("GROQ_API_KEY")
if not KEY:
    sys.exit("CLEAN_FAIL 環境變數 GROQ_API_KEY 沒設，回步驟二")

SYSTEM = (
    "你是台灣國中小老師的文字編輯助理。把語音辨識出來的逐字稿，"
    "整理成可以直接發給學生看的講義段落。規則："
    "① 一律輸出繁體中文，用台灣的慣用詞；"
    "② 刪掉贅詞、口頭禪、結巴、重複、以及講者自己更正掉的前半句；"
    "③ 補上標點符號，依語意分段；"
    "④ 不要新增原文沒有的內容，不要加自己的解釋；"
    "⑤ 聽不出來的地方原樣保留，並在後面加上 [?]；"
    "⑥ 不要寫開場白或結語，直接輸出整理後的內文。"
)

def split_text(raw, limit):
    """依行切段，盡量不切在句子中間"""
    parts, buf = [], ""
    def flush():
        nonlocal buf
        if buf.strip():
            parts.append(buf)
        buf = ""
    for line in raw.splitlines(keepends=True):
        while len(line) > limit:          # 整篇沒換行的長文，只好硬切
            flush()
            parts.append(line[:limit])
            line = line[limit:]
        if len(buf) + len(line) > limit:
            flush()
        buf += line
    flush()
    return parts

def call(text):
    body = json.dumps({
        "model": MODEL,
        "temperature": 0.2,
        "messages": [{"role": "system", "content": SYSTEM},
                     {"role": "user",   "content": text}],
    }).encode("utf-8")
    req = urllib.request.Request(API, data=body, headers={
        "Authorization": "Bearer " + KEY,
        "Content-Type": "application/json",
    })
    with urllib.request.urlopen(req, timeout=180) as r:
        return json.load(r)["choices"][0]["message"]["content"].strip()

raw   = open(sys.argv[1], encoding="utf-8").read()
parts = split_text(raw, LIMIT)
out   = []

for n, p in enumerate(parts, 1):
    print(f"清洗第 {n}/{len(parts)} 段…")
    for attempt in range(3):
        try:
            out.append(call(p))
            break
        except urllib.error.HTTPError as e:
            detail = e.read().decode("utf-8", "ignore")[:300]
            if e.code == 429 and attempt < 2:      # 額度滿了，等一下再試
                print("   額度滿了，等 35 秒再試")
                time.sleep(35)
                continue
            sys.exit(f"CLEAN_FAIL http={e.code} {detail}")
    time.sleep(2)                                   # 段與段之間刻意留間隔

open(sys.argv[2], "w", encoding="utf-8").write("\n\n".join(out))
print(f"CLEAN_OK pieces={len(parts)} out={sys.argv[2]}")
```

**跑它：**

```powershell
uv run python clean.py "lesson01.txt" "lesson01-講義.md"
```

**成功的樣子**：一段一段印出進度，最後印 `CLEAN_OK pieces=<數字> out=lesson01-講義.md`。

## 5-3 為什麼要分段、為什麼中間要等

| 設計 | 理由 |
|------|------|
| `LIMIT = 1200`（一段 1,200 字） | 免費方案 `llama-3.3-70b-versatile` **每分鐘只有 12,000 token**。一小時的逐字稿一次送必定 `429` |
| 段與段之間 `sleep 2` | 讓每分鐘的 token 用量攤平，不會集中在前 10 秒燒完 |
| 遇到 `429` 等 35 秒重試 | token 額度是**每分鐘**重置的，等一下就會恢復 |

> 🔴 **不要把 `LIMIT` 調大想跑快一點。** 調大只會讓你更快撞到 `429`，然後每次都要等 35 秒，**整體反而更慢**。

## 5-4 想要別的產出，改 `SYSTEM` 那段就好

`clean.py` 裡的 `SYSTEM` 字串就是「你要它做什麼」。改那一段，這支程式就變成另一個工具：

| 你想要 | `SYSTEM` 改成 |
|--------|--------------|
| 講義（預設） | 上面那段 |
| 課堂重點摘要 | 「把逐字稿整理成 5～8 條重點，每條一行，用繁體中文…」 |
| 學生自學版（口語轉書面） | 「把逐字稿改寫成適合國中生自己閱讀的說明文…」 |
| 抽出考題素材 | 「從逐字稿裡找出可以出題的概念，列成清單…」 |

> 💡 **這就是「把 API 變成工具」的意思**——同一支程式、同一把金鑰，換一段話就換一個工具。

## 5-5 ✅ B 線驗收：換一份新素材，同一句話能不能再跑一次

| # | 做什麼 | 通過的樣子 |
|---|--------|-----------|
| 1 | 打開 `lesson01-講義.md` | 是**通順的繁體中文段落**，不是一長串沒標點的字 |
| 2 | 跟原逐字稿對照 | 意思沒被改掉、**沒有多出原文沒講過的內容** |
| 3 | 找一段你記得自己講錯又更正的地方 | 更正前那半句**被刪掉了** |
| 4 | **換一份完全不同的逐字稿**，重跑一次 | 一樣跑得完 |
| 5 | 🔴 **步驟六做完之後**：對 OpenCode 說「**幫我把 `<新檔名>` 整理成講義**」 | **它自己做完，你沒有再貼指令** ← **這一項才是真的通過** |

> ⚠️ 第 2 項要真的看。語言模型偶爾會「幫你補充」——**補出來的東西看起來很專業，但你根本沒講過**。
> 發講義前**自己讀一遍**，這件事不能外包。

---

# 步驟六：把整條流程包成 Skill（🔴 這是作品⑤ 的正題）

前面五步都是「跑一次」。**跑一次的東西下個月一定忘記。**

現在把它變成一個技能：以後**在任何資料夾、對 OpenCode 說一句人話**，它就會自己做完。

## 6-1 建立技能資料夾

**Windows**

```powershell
New-Item -ItemType Directory -Force "$HOME\.config\opencode\skills\audio-notes"
```

**macOS / Linux**

```bash
mkdir -p ~/.config/opencode/skills/audio-notes
```

> ⚠️ 資料夾名稱要和技能的 `name` **一模一樣**，檔名要是**全大寫的 `SKILL.md`**，OpenCode 才掃得到。（規矩同 `08-workflow-skills`。）

## 6-2 把兩支小程式搬進去

```powershell
Copy-Item "$HOME\Documents\groq-subtitle\make_srt.py" "$HOME\.config\opencode\skills\audio-notes\"
Copy-Item "$HOME\Documents\groq-subtitle\clean.py"    "$HOME\.config\opencode\skills\audio-notes\"
```

> 🔴 **搬之前先確認這兩支檔案裡沒有金鑰。** 它們讀的是 `os.environ.get("GROQ_API_KEY")`，本來就沒有——**但如果你剛剛「為了測試方便」把金鑰貼死在裡面，現在就是把它拿掉的時候。**

## 6-3 建立 `SKILL.md`

把下面整段內容原封不動寫入 `~/.config/opencode/skills/audio-notes/SKILL.md`
（**不要**把最外層的 ```` ``` ```` 圍籬符號寫進檔案，檔案第一行必須是 `---`）：

````md
---
name: audio-notes
description: 把音訊或影片轉成字幕檔（.srt），以及把逐字稿清洗成可讀講義（.md）。當使用者說「轉字幕」、「上字幕」、「做逐字稿」、「這個錄音幫我轉文字」、「把逐字稿整理成講義」、「把這段錄音變成講義」時，請一定要使用此技能。走 Groq 的 Whisper 與文字模型，金鑰讀自環境變數 GROQ_API_KEY。
---

# 音訊 → 字幕 → 講義

## 🔴 執行前必問（不可跳過）

動手之前，先問使用者這一句，**等到回答再繼續**：

> 「這個音檔裡面**有沒有學生、家長的聲音**，或是輔導、個案相關的內容？
> 有的話這條流程不能用——語音會上傳到雲端。」

- 回答「有」或「不確定」 → **停止，不要呼叫任何 API**，把原因說清楚
- 回答「沒有，只有我自己的聲音」 → 繼續

順便提醒一次：**上傳前先聽檔案的最後 30 秒**，課堂錄音常常錄到下課後的對話。

## 金鑰

金鑰一律從環境變數 `GROQ_API_KEY` 讀取。

- **不准**把金鑰寫進本檔案、寫進任何 `.py`、寫進任何會被 commit 的檔案
- **不准**把金鑰印在終端機上、或複述給使用者看
- 環境變數不存在 → 停下來，請使用者依懶人包 `09-groq-api` 步驟二設定，**不要自己想辦法繞過**

## 模式 A：音訊 → 字幕（.srt）

1. **檢查工具**：`ffmpeg -version`、`ffprobe -version`。缺了就停下來告訴使用者（懶人包 `02-file-toolkit` B 段）
2. **檔名先換成英數字**（中文檔名在 Windows 命令列可能亂碼），全部跑完再改回來
3. **壓縮**：
   `ffmpeg -i <來源> -ar 16000 -ac 1 -map 0:a -c:a flac clean.flac`
4. **看大小**。25 MB 以內 → 跳到第 6 步；超過 → 第 5 步
5. **切段**（每段 600 秒）：
   `ffmpeg -i clean.flac -f segment -segment_time 600 -c:a flac chunk_%03d.flac`
6. **逐段呼叫 API**，每次之間停 3 秒：
   端點 `https://api.groq.com/openai/v1/audio/transcriptions`
   參數 `model=whisper-large-v3`、`language=zh`、`temperature=0`、`response_format=verbose_json`
   輸出存成與音訊同名的 `.json`
   - 🚨 **`response_format` 不可以填 `srt` 或 `vtt`**，Groq 不支援，會回 400
7. **檢查每個 `.json`**：檔案太小（幾百 bytes）代表那一段失敗了，是錯誤訊息不是字幕。**先處理再往下**
8. **組 srt**（json 一定要照檔名排序傳入）：
   `uv run python <本技能資料夾>/make_srt.py <輸出>.srt chunk_000.json chunk_001.json ...`
9. **繁體化**：把產出的 srt 逐句改成繁體中文並修錯字。
   🔴 **時間碼那幾行一個字都不准改，行數與編號不准增減，不准合併或拆開任何一段。**
10. **回報**：段數、輸出路徑，並提醒使用者「請拖進播放器確認**最後 2 分鐘**的字幕還對得上」

## 模式 B：逐字稿 → 講義（.md）

1. 來源是 `.srt` → 先去掉編號與時間碼，只留文字，存成 `.txt`
2. 執行：`uv run python <本技能資料夾>/clean.py <逐字稿>.txt <輸出>.md`
3. 拿到 `CLEAN_OK` 才算成功
4. 回報時**一定要加這句**：「語言模型偶爾會補充原文沒有的內容，**發給學生前請自己讀一遍**」

## 常見狀況

| 狀況 | 處理 |
|------|------|
| `400` 且訊息提到 `response_format` | 用了 `srt`／`vtt`，改成 `verbose_json` |
| `413` 或檔案太大 | 沒壓縮，或壓完仍超過 25 MB → 回去切段 |
| `429` | 額度滿了，等 35 秒再試，**不要換模型硬闖** |
| `401` / `invalid_api_key` | `GROQ_API_KEY` 沒設或設錯 → 停下來請使用者處理 |
| 模型名稱不存在 | 跑 `https://api.groq.com/openai/v1/models` 看現在有哪些，不要亂猜 |
| 字幕越後面越對不上 | `ffprobe` 沒裝，`make_srt.py` 量不到每段實際長度。補裝 FFmpeg 後**重跑 make_srt.py 就好，不用重轉** |

## 不該做的事

- ❌ 使用者沒回答隱私那一題就開始上傳
- ❌ 把金鑰寫進任何檔案或印出來
- ❌ 修改 srt 的時間碼
- ❌ 為了「快一點」把清洗的分段字數調大（只會更快撞 429）
- ❌ 一次送幾十個很短的小檔（最低計費 10 秒，額度會白白燒光）

## 注意事項

- 所有訊息使用**繁體中文**
- Windows 用 PowerShell，串指令用 `;` 不要用 `&&`
- Windows 呼叫 curl 要打 `curl.exe`（有 `.exe`）
````

## 6-4 允許這個技能

打開（或建立）`~/.config/opencode/opencode.json`，確認 `permission.skill` 裡有這一項：

```json
{
  "permission": {
    "skill": {
      "audio-notes": "allow"
    }
  }
}
```

> ⚠️ **如果 `opencode.json` 已經有內容（前面幾包加的 MCP 設定、`08-workflow-skills` 加的三個技能），不要整份覆蓋**，只要把 `audio-notes` 這一行併進既有的 `permission.skill` 區塊就好。

## 6-5 重啟 OpenCode

**OpenCode 只有啟動時才會掃描技能目錄。** 剛剛新增的資料夾，要**完全關掉再重開**才會被認得（關視窗不算，要真的結束程序）。

## 6-6 ✅ 最終驗收：這一包成不成功，看這裡

| # | 做什麼 | 通過的樣子 |
|---|--------|-----------|
| 1 | 對 OpenCode 說「**列出你現在可用的技能**」 | 清單裡有 `audio-notes` |
| 2 | 拿一份**全新的、沒跑過的**錄音，說「**幫我把 `<檔名>` 轉成字幕**」 | 它自己壓縮、呼叫 API、組 srt、繁體化，**你沒有貼任何指令** |
| 3 | 檢查它有沒有先問隱私那一題 | **有問** ← 沒問的話回去看 `SKILL.md` 是不是貼漏了 |
| 4 | 拿一份**全新的**逐字稿，說「**幫我把 `<檔名>` 整理成講義**」 | 它自己做完 |
| 5 | 打開 `SKILL.md`、`make_srt.py`、`clean.py` 搜尋 `gsk_` | 🔴 **三個檔案裡都搜不到** ← 搜到就是失敗，回步驟二 |

> ## 🔴 第 2 項和第 4 項就是這一包的分水嶺。
>
> 做得到 = 你有了一個**工具**。做不到 = 你只是**跑過一次**。
>
> 差別在下個月：有工具的人會再用，跑過一次的人早就忘了。

---

## 常見卡關

### 金鑰與帳號

| 症狀 | 原因 / 解法 |
|------|------------|
| `KEY_MISSING` | 環境變數沒生效。**重開終端機**（連 OpenCode 也要完全關掉重開） |
| `401` / `invalid_api_key` | 金鑰複製時少一截，或前後多了空白。回 Console 重新產一把 |
| 金鑰畫面關掉了才發現沒複製 | **看不回來，只能刪掉重產。** 這是設計，不是壞掉 |
| 註冊時要求信用卡 | **你走進升級流程了。** 按上一頁退出來，免費方案不需要卡 |
| 金鑰不小心 push 上 GitHub | **刪檔案沒用。立刻回 Console 刪掉那把金鑰、重產一把。** 然後更新環境變數 |

### A 線（轉字幕）

| 症狀 | 原因 / 解法 |
|------|------------|
| `400` 提到 `response_format` | 🚨 **陷阱 ①**。填了 `srt`／`vtt`，Groq 不支援。改 `verbose_json`，用 `make_srt.py` 組 |
| `413` / 檔案太大 | 🚨 **陷阱 ②**。免費方案單檔 25 MB。回 A1 壓縮，還是太大就切段 |
| 轉出來是簡體 | 🚨 **陷阱 ③**。看 A5 兩條路線 |
| `.srt` 拖進播放器沒反應 | 檔名要和影片**同名**（`lesson01.mp4` ↔ `lesson01.srt`），或播放器裡手動載入 |
| 字幕全部集中在前 10 分鐘 | 🚨 **陷阱 ④**。只餵了一個 json，或檔案順序亂了。回 4-3 |
| 越後面偏差越大 | `ffprobe` 沒裝。補裝 FFmpeg，**重跑 `make_srt.py` 就好，不用重轉**（json 都還在） |
| 中間有一整段沒字幕 | 那一段的 `.json` 是錯誤訊息。回 4-2 檢查每個 json 的大小 |
| `SRT_OK segments=0` | API 回的 json 裡沒有 `segments`——多半是 `response_format` 沒填 `verbose_json` |
| 字幕內容整段重複 | 音檔裡有長時間靜音，Whisper 的已知行為。剪掉靜音再轉 |
| `ffmpeg` 說找不到檔案，但檔案就在 | 中文檔名 ／ 路徑有空白。**先改成英數字檔名**，或整個路徑用雙引號包起來 |
| 一句一個檔跑，額度突然沒了 | **最低計費 10 秒**。改成切 10 分鐘大段 |

### B 線（文字清洗）

| 症狀 | 原因 / 解法 |
|------|------------|
| `429` | 每分鐘 token 額度滿了。程式會自己等 35 秒重試；一直發生就把 `LIMIT` 調**小**（例如 800） |
| `CLEAN_FAIL http=404` | 模型改名了。跑 `/v1/models` 看現在叫什麼，改 `clean.py` 的 `MODEL` |
| 講義裡出現我沒講過的內容 | 語言模型「幫你補充」了。把 `SYSTEM` 的第 ④ 條講更重，或改用比較大的模型 |
| 輸出還是簡體 | `SYSTEM` 第 ① 條被忽略了。在使用者訊息**開頭再寫一次**「請用繁體中文（台灣）輸出」 |
| 跑很慢 | 正常。免費方案要分段＋等待。一小時的逐字稿約 10 段，大約 2～3 分鐘 |
| 專有名詞一直錯 | 語音辨識階段就聽錯了。把正確寫法列在 `SYSTEM` 裡，例如「本文的專有名詞：正三角形、外心、內心」 |

### Skill 與環境

| 症狀 | 原因 / 解法 |
|------|------------|
| 技能沒出現在清單 | ①檔名要**全大寫 `SKILL.md`** ②資料夾名要等於 `name` ③檔案第一行必須是 `---`（圍籬符號不要寫進去）④**沒重啟 OpenCode** |
| 技能叫得動但找不到 `.py` | `SKILL.md` 裡要用**技能資料夾的完整路徑**叫程式，不是相對路徑 |
| `uv` 說找不到 | 回 `00-env-setup`。或直接用 `python make_srt.py ...`，這兩支程式只用內建模組 |
| `ffprobe` 找不到 | 只裝了 yt-dlp 自帶的 FFmpeg。`winget install --id Gyan.FFmpeg -e`（沒管理員權限加 `--scope user`） |
| PowerShell 串指令語法錯誤 | PowerShell 5.1 **不支援 `&&`**，改用 `;` |
| `curl` 行為很奇怪 | Windows 要打 `curl.exe`（有 `.exe`），否則叫到 PowerShell 的別名 |
| 中文參數傳給 curl 變亂碼 | PowerShell 5.1 的編碼問題。改用 `-F "欄位=<檔名"` 從檔案讀（見 A5） |
| `opencode.json` 存檔後 OpenCode 起不來 | JSON 格式：大括號要成對、引號要用英文半形 `"`、Windows 路徑反斜線寫兩條 `\\` |

---

## ✅ 成功訊號（機器可判讀）

**執行完請完整輸出下面這個區塊**，讓後續的 agent 可以直接判讀。

```
=== OPENCODE_GROQ_API ===
result=PASS                  # PASS | PARTIAL | FAIL
os=windows                   # windows | macos | linux
privacy_confirmed=yes        # yes | no（使用者已確認素材不含學生/家長聲音）
key_set=yes                  # yes | no（環境變數 GROQ_API_KEY 存在）
key_in_files=no              # no | yes（SKILL.md 與 .py 內是否搜得到 gsk_）
api_ok=yes                   # yes | no（/v1/models 回傳正常）
ffmpeg=ok                    # ok | missing
ffprobe=ok                   # ok | missing（缺了會退用 API 的 duration）
model_stt=whisper-large-v3   # 實際使用的語音模型
model_llm=llama-3.3-70b-versatile   # 實際使用的文字模型
srt_single=OK                # OK | FAIL | SKIP（單檔轉字幕）
srt_chunked=OK               # OK | FAIL | SKIP（長檔切段合併）
srt_tail_aligned=yes         # yes | no（影片最後 2 分鐘字幕仍對得上）
clean_ok=OK                  # OK | FAIL | SKIP
skill_installed=yes          # yes | no（~/.config/opencode/skills/audio-notes/）
skill_listed=yes             # yes | no（重啟後 OpenCode 掃得到）
skill_rerun_srt=OK           # OK | FAIL（換新素材，一句話跑完 A 線）
skill_rerun_clean=OK         # OK | FAIL（換新素材，一句話跑完 B 線）
=== END ===
```

**判定規則（agent 請照這個判斷，不要自行認定）：**

| 結果 | 條件 |
|------|------|
| `PASS` | `skill_rerun_srt=OK` **且** `skill_rerun_clean=OK` **且** `key_in_files=no` |
| `PARTIAL` | A、B 兩線手動跑得起來，但技能沒包成／換素材沒跑通 |
| `FAIL` | `api_ok=no`，或 A、B 兩線都跑不起來 |

> ## 🔴 兩條會直接讓整體變成 `FAIL` 的：
>
> **`key_in_files=yes`** —— 金鑰出現在任何檔案裡，**不管其他項目多漂亮，一律 `FAIL`**。
> **`privacy_confirmed=no`** —— 沒跟使用者確認過素材內容就上傳，**一律 `FAIL`**，而且要在回報裡明確寫出來。
>
> 這兩條不准「先繼續、之後再補」。

---

## 完成回報格式

```md
## Groq API 工具化結果

### 隱私確認
- 已向使用者確認素材不含學生／家長／輔導內容：是 / 否
- 已提醒「上傳前聽最後 30 秒」：是 / 否
- Zero Data Retention 已開啟：是 / 否 / 未確認

### 金鑰
- 存放方式：環境變數 / 專案 .env
- `.gitignore` 已擋住 `.env`（`git check-ignore -v .env` 有輸出）：是 / 否 / 不適用
- SKILL.md 與 .py 內搜尋 `gsk_`：查無 / ⚠️ 有（立刻撤銷重產）
- 金鑰驗證（`/v1/models`）：PASS / FAIL

### A 線：轉字幕
- 使用模型：<實際用的>
- 來源檔長度：<分鐘> ／ 壓縮後大小：<MB>
- 是否切段：否 ／ 是（<N> 段，每段 600 秒）
- 產出：`<路徑>.srt`（<N> 句）
- 時間碼驗證（看**最後 2 分鐘**）：PASS / FAIL
- 繁體化方式：prompt 參數 / OpenCode 改字 / 兩者都做

### B 線：文字清洗
- 使用模型：<實際用的>
- 分段數：<N>
- 產出：`<路徑>.md`
- 已提醒使用者「發給學生前自己讀一遍」：是 / 否

### 工具化（本包正題）
- 技能位置：`~/.config/opencode/skills/audio-notes/`
- 隨附程式：`make_srt.py`、`clean.py`
- `opencode.json` 權限已加：是 / 否
- 已重啟 OpenCode：是 / 否
- 技能出現在清單：是 / 否
- 🔴 **換新素材、一句話跑完 A 線**：PASS / FAIL
- 🔴 **換新素材、一句話跑完 B 線**：PASS / FAIL

### 待處理
- <把 FAIL 的項目與原因列在這裡；全部 PASS 就寫「無」>
```

---

## 如果做壞了，怎麼重來

對 OpenCode 說：「**Groq 這一包做壞了，幫我清乾淨重來。**」

| 想重來的東西 | 做法 |
|-------------|------|
| 只是某一段轉壞了 | 刪掉那一段的 `.json`，重跑那一段就好。**其他段不用重轉** |
| 時間碼歪了 | 補裝 `ffprobe`，**重跑 `make_srt.py`**。API 不用重打，json 都還在 |
| 字幕整份要重來 | 刪掉 `chunk_*.json` 和 `.srt`，從 4-2 重跑 |
| 技能沒被掃到 | 檢查檔名是不是 `SKILL.md`（全大寫）、資料夾名是不是 `audio-notes`、第一行是不是 `---`。改完**完全關閉 OpenCode 再開** |
| 技能整個不要了 | 刪掉 `~/.config/opencode/skills/audio-notes/` 資料夾，並從 `opencode.json` 的 `permission.skill` 移除那一行 |
| **金鑰外洩** | 🔴 Console → API Keys → **刪掉那把、重產一把** → 更新環境變數。**這件事優先於其他所有事** |

> ⚠️ **重跑 API 之前先想一下額度**：免費方案每小時 7,200 音訊秒。
> 一支 1 小時的錄音重轉三次就用掉一半。**先確認是哪一步壞了，不要整條重來。**

---

## 下一步

> ## 🚀 下午：把這個工具變成網頁 → **`10-netlify`**
>
> 現在你有一個「只有你的電腦能用」的工具。
> 下午那一包會把它放上網路——**別的老師打開網址就能用，不用裝 OpenCode、不用申請金鑰。**
>
> 今天早上寫的 `clean.py`、學到的「金鑰放環境變數不放檔案」，下午會**原封不動用上**。
> （尤其是金鑰那條：網頁上線之後，金鑰放錯地方是**全世界都看得到**。這件事今天先練，下午就不會慌。）

其他方向：

- 素材從哪裡來（下載影片、抽音軌、抓現成字幕）→ **`02-file-toolkit`** 的 B 段
- 產出的講義要收進第二大腦 → **`04-obsidian`**
- 工具要備份、換電腦也帶著走 → **`07-github`**
- 想讓「轉字幕」也能被開工／收工流程接住 → **`08-workflow-skills`**

> 📌 **提醒一件事**：`~/.config/opencode/skills/` 底下的技能**不在任何專案的 repo 裡**。
> 換一台電腦，這個技能**不會跟著過去**。要帶著走，就把 `audio-notes` 這個資料夾也放進你的 GitHub（見 `07-github`）——**但記得，裡面永遠不要有金鑰。**
