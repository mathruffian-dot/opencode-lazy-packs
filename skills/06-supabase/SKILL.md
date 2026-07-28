---
name: opencode-supabase
description: 即時資料庫 — 用 Supabase 讓「多人同時寫入」的作品真的能用（文字雲、線上對戰、即時排行榜）。說「即時資料庫」「Supabase」「多人同時寫入」「文字雲要存資料」「即時排行榜」時載入。
---

# 即時資料庫（Supabase）

> 官方文件查證日期：**2026-07-27**
> 依據：<https://supabase.com/docs/guides/getting-started/mcp>、<https://supabase.com/docs/guides/database/postgres/row-level-security>、<https://supabase.com/changelog/45329-breaking-change-tables-not-exposed-to-data-and-graphql-api-automatically>、<https://supabase.com/docs/guides/getting-started/migrating-to-new-api-keys>、<https://supabase.com/docs/guides/platform/free-project-pausing>

---

## ⚠️ 先看這裡：這一包和 `05-sheets-gas` 差在哪

兩包都在「讓作品記得住事情」，但**適用場景完全不同**，選錯會很痛：

| | `05-sheets-gas`（Google 試算表） | **本包（Supabase）** |
|---|---|---|
| 適合 | **非即時**：報名表、作業繳交、點名紀錄、問卷 | **即時**：多人同時寫、同時看 |
| 典型作品 | 一份會慢慢長大的表 | **文字雲**、**線上對戰**、**即時排行榜**、課堂搶答 |
| 多人同時送出 | 會塞車、會漏、GAS 有配額 | 這就是它被設計來做的事 |
| 老師看資料 | 打開試算表 | Dashboard 的表格畫面（長得像 Excel） |
| 難度 | 較低 | 中，但**四個陷阱不知道就一定卡住** |

> 判斷句一句話：**「同一秒鐘會不會有兩個人以上在寫？」**
> 會 → 本包。不會 → 回 `05-sheets-gas`，不要為了用資料庫而用資料庫。

---

## 🚨 這一包的四個陷阱（先看完再動手）

這四條是本包最重要的內容。**網路上 2026 年 5 月以前的 Supabase 教學，全部會在第 ① 條斷掉。**

| # | 陷阱 | 症狀 | 一句話解法 |
|---|------|------|-----------|
| ① | **新專案預設不把表曝露給 Data API** | 前端一連就 `permission denied for table xxx` | 建專案時勾「Automatically expose new tables」，或建表 SQL 後面固定接 `grant` |
| ② | **RLS 預設開不開，看你用什麼方式建表** | 用 SQL 建的表 **RLS 預設是關的** → 門是開的 | **AI 幫你建表一律走 SQL**，所以永遠要自己補 `enable row level security` |
| ③ | **只有 INSERT policy 不夠** | 送出後噴紅字或畫面空白，老師以為壞了就重送 → 重複資料 | INSERT 和 SELECT 兩個 policy 都要建 |
| ④ | **API key 改名了** | 照舊教材去 Dashboard 找 `anon` / `service_role`，找不到 | 現在叫 `sb_publishable_...` / `sb_secret_...` |

下面每一步都會再點名它處理的是哪一條。

---

## 這個懶人包會幫你做什麼？

1. 建一個 **Supabase** 免費專案（**不用綁信用卡**）
2. 拿到**可以放在網頁裡**的那把金鑰
3. 用一段**貼一次就過三關**的 SQL，建好一張真的能用的表
4. 讓 GitHub Pages 上的網頁前端**寫得進去、讀得回來、即時更新**
5. 用 **GitHub Actions 每日排程**擋掉「閒置 7 天自動暫停」
6. （老師端進階，研習建議最後再看）把 Supabase MCP 接給 OpenCode

---

## 原理說明（30 秒）

```
學生的手機 ──┐
學生的手機 ──┼─→ GitHub Pages 上的網頁 ──→ Supabase（雲端資料庫）
學生的手機 ──┘         （前端）              ↑
                                        RLS 守門員
老師的 OpenCode ─────────────────────────────┘（進階，選用）
```

**要記住的只有一件事**：網頁前端拿的那把金鑰，**全世界都看得到**（按 F12 就看得到）。
所以資料安不安全，**100% 取決於資料庫自己的守門員 RLS**，跟金鑰藏得好不好完全無關。
這句話是本包所有安全設計的根。

---

## 免費方案（不用綁卡）

> ⚠️ 以下額度為 **2026-07-27** 查到的數字，**服務商隨時會調整，以官方頁面為準**。

| 項目 | 免費方案 | 對老師夠嗎 |
|------|---------|-----------|
| 資料庫容量 | 500 MB | ✅ 一整年的文字雲、投票遠遠用不到 |
| 同時啟用的專案數 | 2 個 | ⚠️ 只有 2 個，第 3 個要先暫停舊的 |
| Realtime 同時連線 | 200 個併發 | ✅ 一場研習 / 一個班級夠用 |
| 信用卡 | **不用綁** | ✅ **研習不要求任何人自費** |
| 閒置 | **7 天沒動作自動暫停** | ⚠️ 見下方「步驟六」 |

**關於暫停後還救不救得回來**：官方頁面存在**兩個互相矛盾的說法**（一處寫 90 天內可還原，另一處提到最長 1 年）。
本包一律用**保守版本**教：

> 🔴 **就當作只有 90 天。90 天內一定還原得回來，超過就不要賭。**
> 期末做的東西如果暑假要留著，暑假前就把重要資料匯出一份放 GitHub。

---

## 先備條件

- [ ] 已完成 **`00-env-setup`** 與 **`01-connect-model`**（沒接模型，OpenCode 不會動手）
- [ ] 已完成 **`07-github`** 的**第二階段**（有 GitHub 帳號、會 push）——網頁要掛在 GitHub Pages，防暫停排程也要用 GitHub
- [ ] 有網路
- [ ] 一個瀏覽器

驗證：

```powershell
gh auth status
```

看得到 `Logged in to github.com` 就過。看不到 → **回去做 `07-github`**，不要在這裡臨時處理。

---

# 步驟一：建帳號與專案（🚨 這裡處理陷阱 ①）

> 🖐️ 全程在瀏覽器手動做。**AI agent 不要代為註冊、不要代填密碼。**

### 1-1 註冊

開 <https://supabase.com> → **Start your project** → 建議選 **Continue with GitHub**（用你 `07-github` 那個帳號，之後網頁和資料庫是同一組身分，好管理）。

### 1-2 建立專案

點 **New Project**，四個欄位：

| 欄位 | 怎麼填 |
|------|--------|
| Project name | 英數字，例如 `class-live`（**不要中文、不要空白**） |
| Database Password | 系統會產一組，**直接用它產的、複製起來貼到你的密碼管理器**。這組密碼研習全程用不到，但弄丟很麻煩 |
| Region | 選 **Northeast Asia (Tokyo)** 或 **Southeast Asia (Singapore)**，離台灣最近 |
| **Automatically expose new tables and functions** | 🚨 **把它勾起來** |

### 1-3 🚨 陷阱 ①：那個勾勾是什麼

**2026-05-30 起，Supabase 新專案預設「不再自動把新建的表曝露給 Data API」。**

**今天建的專案已經在新預設之內。** 不勾這個，後面前端一連就會拿到：

```
permission denied for table wordcloud.
Grant the required privileges to the current role with:
GRANT SELECT ON public.wordcloud TO anon.
```

而你的網頁程式碼**完全沒有寫錯**——這是最折磨人的地方。

**兩條路，選一條（本包兩條都幫你做，雙保險）**：

- **A｜建專案時勾起來**（步驟 1-2 那個勾勾）
- **B｜每次建表的 SQL 後面固定接 `grant`**（步驟三的範本已經內建了）

> 💡 找不到那個勾勾也沒關係——**走 B 就好**，步驟三的 SQL 已經包含它。
> 反過來說：**只勾了 A 就以為安全，是不夠的**，因為你之後在別的地方建的表可能仍然沒被曝露。所以本包堅持 SQL 裡一定要有 `grant`。

### 1-4 驗證

專案建立要等 1～2 分鐘。左側選單看得到 **Table Editor**、**SQL Editor**、**Authentication** 就算好了。

---

# 步驟二：拿金鑰（🚨 這裡處理陷阱 ④）

### 2-1 🚨 陷阱 ④：金鑰換代了

`anon` 和 `service_role` 這兩個名字**正在被淘汰**。現在的名字是：

| 現在的名字 | 舊名字 | 白話 | 能放進網頁嗎 |
|-----------|--------|------|-------------|
| `sb_publishable_...` | `anon` | **可以放在網頁裡的** | ✅ **可以**，它就是設計來被看到的 |
| `sb_secret_...` | `service_role` | **絕對不能放的** | ❌ **絕對不行**，它會**繞過所有 RLS**，等於資料庫的萬能鑰匙 |

**研習講義請只用這兩句話講**，不要講 JWT、不要講 role：

> **Publishable key ＝可以放在網頁裡的。**
> **Secret key ＝絕對不能放。**

> ⚠️ 舊教材（含網路上大部分 Supabase 教學）會叫你去複製 `service_role key`。
> **本包完全不需要 secret key。** 只要有人叫你把 secret key 貼進網頁、貼進 GitHub、貼給 AI，那一定是舊做法，停下來。

### 2-2 去哪裡拿

Dashboard 左下角 **Project Settings**（齒輪）→ **API Keys**。

複製兩個東西：

1. **Project URL**（長得像 `https://abcdefghijk.supabase.co`）
2. **Publishable key**（`sb_publishable_` 開頭那一串）

> 如果畫面上還看得到 `anon` / `service_role`，通常是收在 **Legacy API keys** 分頁裡。
> **不要用那一區的東西**，用新的 `sb_publishable_...`。

### 2-3 驗證

在 PowerShell 貼這一行（把兩個 `<>` 換掉）：

```powershell
curl.exe "<你的Project URL>/rest/v1/" -H "apikey: <你的publishable key>"
```

- 回傳一段 JSON（就算內容看起來是空的 `{}` 或一堆 `openapi` 欄位）→ ✅ **金鑰是對的**
- 回 `{"message":"Invalid API key"}` → key 複製錯了或少了一截，重複製一次

---

# 步驟三：建表（🚨 這裡一次處理陷阱 ①②③）

### 3-1 🚨 陷阱 ②：這是本包最危險的一條

Supabase 官方文件白紙黑字寫的：

- 用 **Dashboard 的 Table Editor** 建的表 → **RLS 預設是開的**
- 用 **SQL / SQL Editor** 建的表 → **RLS 預設是關的**，要你自己開

### 🚨 而 AI Agent 幫你建表，一律走 SQL。

也就是說——

> **你對 OpenCode 說「幫我建一個成績表」，它建出來的那張表，門是開的。**
>
> 不是它偷懶，是 SQL 建表本來就這樣。而**沒有人會提醒你**：
> 前端照樣讀得到寫得進去，畫面一切正常，**你不會收到任何警告**。
> 你以為做完了，實際上那張表現在全世界都能讀、能改、能刪。

**所以本包的規矩是**：

> 🔴 **凡是叫 AI 建表，建完一定要自己確認 RLS 有開、policy 有建。**
> 這件事**不能外包給 AI 自己保證**——要你自己看步驟 3-4 的驗證畫面。

### 3-2 現場範本：貼一次，過三關

Dashboard 左側 **SQL Editor** → **New query** → 整段貼上 → 右下角 **Run**。

```sql
create table public.wordcloud (
  id bigint generated always as identity primary key,
  seat_no text,                 -- 只存座號，不存姓名
  word text not null,
  created_at timestamptz default now()
);

grant select, insert on public.wordcloud to anon;
grant usage, select on all sequences in schema public to anon;

alter table public.wordcloud enable row level security;

create policy "anyone can insert" on public.wordcloud
  for insert to anon with check (true);

create policy "anyone can read" on public.wordcloud
  for select to anon using (true);
```

這段 SQL 每一區在做什麼：

| 區塊 | 對應陷阱 | 白話 |
|------|---------|------|
| `create table` | — | 開一張表。`seat_no` 只放座號 |
| `grant ...` 兩行 | **①** | 「把這張表曝露給前端看得到」。**沒有這兩行就是 `permission denied`** |
| `enable row level security` | **②** | **把門裝上**。SQL 建表不會自己裝 |
| `for insert` policy | **③** | 允許寫入 |
| `for select` policy | **③** | 允許讀取。**少了這條就會出事，見 3-3** |

> ⚠️ **這組 policy 是「研習當場、只放假資料」等級的開放設定，不是正式班務可用的設定。**
> `with check (true)` / `using (true)` 的意思是「任何人都可以寫、任何人都可以讀全部」。
> 拿它來收真實成績、真實名單，等同於公開。正式使用請先看本檔最後的「安全紅線」。

### 3-3 🚨 陷阱 ③：為什麼一定要有 SELECT policy

**很多人以為「我只是要讓學生送資料進來，給個 INSERT policy 就好」。錯。**

只給 INSERT 不給 SELECT，老師會踩到兩種狀況，**兩種看起來都像壞掉**：

**狀況 A｜前端程式碼寫成 `.insert(...).select()`**（AI 幫你寫的前端十之八九是這樣，Dashboard 的範例也是這樣）
Supabase 寫入後會**回頭讀一次剛剛那筆**，這一讀就撞到沒有 SELECT policy：

```
new row violates row-level security policy for table "wordcloud"   (code 42501)
```

**狀況 B｜前端只寫 `.insert(...)` 不接 `.select()`**
寫入沒報錯，但接下來「把大家的詞撈回來畫文字雲」的那次讀取拿到**空陣列**，畫面什麼都沒出現。

**兩種狀況，老師看到的都是「它壞了」。而老師的直覺動作是——再送一次。**
於是同一句話送三遍、五遍，資料表裡全是重複資料。

> 🔴 **請直接背這條規則**：
> **不要用「畫面有沒有出現東西」判斷資料進去了沒有。**
> 要確認就去 Dashboard 的 **Table Editor** 打開那張表看——**那裡看到的才算數。**
>
> 而只要你照 3-2 的範本貼（INSERT ＋ SELECT 兩個 policy 都有），這整件事根本不會發生。

### 3-4 驗證（🔴 這一步不要跳過）

**驗證 1：RLS 真的開了嗎**
Dashboard → **Table Editor** → 點 `wordcloud` 表。
表格上方要看到 **RLS enabled**（綠色）。
看到 **RLS disabled / Unrestricted**（紅色警告）→ 表示 `alter table ... enable row level security` 沒跑到，回 SQL Editor 重跑那一行。

**驗證 2：policy 真的有兩條嗎**
Dashboard → **Authentication** → **Policies** → 找到 `wordcloud`。
**要看到兩條**：一條 INSERT、一條 SELECT。只有一條 → 回去重跑缺的那一條。

**驗證 3：純文字驗證（給 agent 判讀，也可以自己貼）**
在 SQL Editor 跑：

```sql
select relrowsecurity from pg_class where relname = 'wordcloud';
select policyname, cmd from pg_policies where tablename = 'wordcloud';
select has_table_privilege('anon', 'public.wordcloud', 'select') as can_read,
       has_table_privilege('anon', 'public.wordcloud', 'insert') as can_write;
```

全部通過的樣子：

- 第一句 → `true`（RLS 開著）
- 第二句 → **兩列**，`cmd` 分別是 `INSERT` 和 `SELECT`
- 第三句 → `can_read = true`、`can_write = true`（陷阱 ① 過關）

### 3-5 要「即時更新」的話，還要多一行

前面做完，前端已經能寫、能讀。但「**別人送出的詞，我這邊畫面自己跳出來**」是另一件事，要另外開：

```sql
alter publication supabase_realtime add table public.wordcloud;
```

沒跑這行的症狀：**自己送自己看得到（因為是自己重新讀的），但別人送你不會動**，要手動重新整理。
文字雲、線上對戰、即時排行榜**都需要這一行**。

驗證：

```sql
select tablename from pg_publication_tables where pubname = 'supabase_realtime';
```

清單裡看得到 `wordcloud` = 過。

---

# 步驟四：網頁前端串接

前端用官方的 `@supabase/supabase-js`（v2）。**可以直接用 CDN 的 ESM 版本 import，不用 npm、不用打包**，所以放在 GitHub Pages 上就能跑。

### 4-1 最小可動範例

```html
<script type="module">
  import { createClient } from 'https://esm.sh/@supabase/supabase-js@2'

  // ⬇️ 這兩行本來就會被所有人看到，這是設計，不是漏洞（見最後一節）
  const supabase = createClient(
    'https://<你的專案id>.supabase.co',
    'sb_publishable_<你的publishable key>'
  )

  // ① 送出一個詞
  async function sendWord(seatNo, word) {
    const { error } = await supabase
      .from('wordcloud')
      .insert({ seat_no: seatNo, word })
    if (error) { alert('送出失敗：' + error.message); return false }
    return true
  }

  // ② 讀回全部（畫文字雲用）
  async function loadWords() {
    const { data, error } = await supabase
      .from('wordcloud')
      .select('seat_no, word, created_at')
      .order('created_at', { ascending: false })
    if (error) { console.error(error); return [] }
    return data
  }

  // ③ 即時：別人一送出，我這邊自己更新（需要步驟 3-5 那一行）
  supabase
    .channel('wordcloud-live')
    .on('postgres_changes',
        { event: 'INSERT', schema: 'public', table: 'wordcloud' },
        payload => {
          console.log('有人送了新的詞：', payload.new.word)
          // 這裡放你重畫文字雲的函式
        })
    .subscribe()
</script>
```

### 4-2 交給 OpenCode 做整頁的講法

不用自己寫，把下面這段貼給 OpenCode（**把 `<>` 換成你自己的**）：

```
幫我做一個文字雲網頁，掛在 GitHub Pages 上。

資料庫用 Supabase：
- Project URL：<你的 Project URL>
- Publishable key：<你的 sb_publishable_...>
- 資料表：public.wordcloud，欄位 seat_no（座號，文字）、word（詞彙，文字）、created_at

功能：
1. 一個輸入框讓學生輸入「座號」和「一個詞」，按送出寫進資料庫
2. 一個按鈕切換顯示 / 隱藏文字雲
3. 用 Realtime 訂閱，別人送出的詞我這邊要自動出現，不用重新整理
4. 頁面上放這個網址的 QR Code，讓學生掃了就能進來

技術要求：
- 用 @supabase/supabase-js v2，走 CDN 的 ESM import，不要用 npm 打包
- 不要在程式碼裡放任何 secret key
- 不要收集姓名，只收座號

做完幫我 commit 並 push，然後告訴我 GitHub Pages 的網址。
```

> ⚠️ **要提醒 agent 的一句話**：如果它「順手」幫你在 Supabase 建了新的表，
> **請它一併把步驟 3-2 那組 `grant` ＋ `enable row level security` ＋ 兩條 policy 補上**——
> 這正是陷阱 ②：**AI 建表走 SQL，RLS 預設是關的。**

### 4-3 驗證（每一項都要看到）

| # | 做什麼 | 通過的樣子 |
|---|--------|-----------|
| 1 | 在網頁送出一個詞 | 沒有紅字錯誤 |
| 2 | 開 Dashboard 的 **Table Editor** → `wordcloud` | **那一列真的在裡面** ← 這才是唯一算數的證據 |
| 3 | **關掉瀏覽器再打開**網頁 | 剛剛的詞還在（資料是存在雲端的，不是存在瀏覽器裡）|
| 4 | 用**手機**（換一個裝置、換一個網路）打開同一個網址送一個詞 | 電腦畫面**不用重新整理就自己出現** ← Realtime 通了 |
| 5 | 按 **F12** → Console | 沒有 `permission denied` 或 `42501` |

第 4 項是這一包真正的分水嶺——**做到這裡，才算做出「即時」資料庫。**

---

# 步驟五：老師端怎麼看資料（不用另外做登入網頁）

Dashboard → **Table Editor** → 點你的表。

**它長得就像 Excel**：可以直接排序、篩選、改一格、刪一列、匯出 CSV。
研習現場請直接把這個畫面投影出來，老師會馬上懂「資料庫」是什麼——**就是一張永遠在線上、全班可以同時寫的試算表**。

匯出：表格右上角 → **Export** → **Download as CSV**。

> 💡 這一步之所以重要：很多人以為做資料庫一定要再做一個「老師管理後台」。
> **不用。** Dashboard 就是後台，而且是官方維護的。省下來的時間拿去做學生端的體驗。

---

# 步驟六：防暫停（GitHub Actions 每日 ping）

免費專案**閒置 7 天會自動暫停**。暫停之後學生掃 QR Code 會失敗，而且**只有你手動去 Dashboard 按還原才會回來**。
寒暑假一放就中招，開學前一天發現最崩潰。

### 6-1 為什麼用 GitHub Actions，不用 agent 排程

| 做法 | 問題 |
|------|------|
| 用 agent 的排程功能每週 ping | **電腦要開機、要登入、agent 要活著**。老師的電腦放假是關機的，等於沒設 |
| 手動記得去點一下 | 一定會忘 |
| **GitHub Actions cron** | ✅ **免費、跑在雲端、電腦關機照跑**。而且 `07-github` 已經有帳號了，直接接上 |

### 6-2 建立 workflow

在你放網頁的那個 repo 裡，建立檔案 `.github/workflows/supabase-keep-alive.yml`：

```yaml
name: Supabase Keep Alive

on:
  schedule:
    # UTC 21:00 = 台灣時間隔天早上 05:00，每天一次
    - cron: "0 21 * * *"
  workflow_dispatch:        # 讓你可以手動按一次來測試

jobs:
  ping:
    runs-on: ubuntu-latest
    steps:
      - name: Ping Supabase REST API
        run: |
          curl --fail --silent --show-error \
            "${SUPABASE_URL}/rest/v1/wordcloud?select=id&limit=1" \
            -H "apikey: ${SUPABASE_KEY}" \
            -H "Authorization: Bearer ${SUPABASE_KEY}"
          echo "PING_OK"
        env:
          SUPABASE_URL: ${{ secrets.SUPABASE_URL }}
          SUPABASE_KEY: ${{ secrets.SUPABASE_PUBLISHABLE_KEY }}
```

### 6-3 設定兩個 Secret

GitHub 網頁 → 你的 repo → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**，建兩個：

| Name | Secret |
|------|--------|
| `SUPABASE_URL` | 你的 Project URL（`https://xxx.supabase.co`，**結尾不要有斜線**）|
| `SUPABASE_PUBLISHABLE_KEY` | 你的 `sb_publishable_...` |

> 💡 publishable key 本來就可以公開，直接寫死在 YAML 裡其實也不會出事。
> 用 Secret 的理由是**好維護**：以後換 key 只改一個地方，不用去翻檔案。
> **但 `sb_secret_...` 無論如何都不要放進來**——連 Secret 都不要，這支排程根本用不到它。

### 6-4 驗證

1. repo → **Actions** 分頁 → 左側點 **Supabase Keep Alive** → 右邊 **Run workflow** → 綠色勾勾
2. 展開執行紀錄，要看到 `PING_OK`
3. 紅色叉叉的話展開看錯誤：
   - `401` / `Invalid API key` → Secret 貼錯或少一截
   - `permission denied` → 🚨 **陷阱 ①**，回步驟 3-2 補跑那兩行 `grant`
   - `404` → 表名打錯，或 URL 結尾多了斜線

### 6-5 ⚠️ GitHub Actions 自己也會睡著

**這是很多人設完就忘、半年後才發現的坑：**

> GitHub 的排程 workflow，在 repo **連續 60 天沒有任何動作**之後會被**自動停用**。
> 也就是說：你設了防暫停排程，結果排程自己先被停掉，Supabase 接著被暫停。

怎麼辦（擇一）：

- **最簡單**：把這個 repo 就用在你**平常會改的**那個網頁專案上（有 push 就算活動）
- GitHub 停用排程時**會寄信給你**，收到信就去 Actions 頁面按一下 **Enable workflow**
- 每學期開學前，順手到 Actions 手動 **Run workflow** 一次

> 這條和 Supabase 沒關係，是 GitHub 的規則。但兩個加起來才是完整的解法，所以寫在這裡。

---

# 步驟七（進階／選用）：把 Supabase MCP 接給 OpenCode

## ⚠️ 研習現場建議：先不要裝

> 🔴 **研習主線到步驟六就結束了。純用網頁 ＋ publishable key，閉環已經完整。**
>
> MCP 請當作「**老師端進階**」，放在最後 5 分鐘由講師示範就好。理由：
> 這條路要走**瀏覽器 OAuth 登入**，而在**學校網路**（擋外連、擋跳轉）和**多 Google 帳號**（瀏覽器登入了三個帳號、彈出來的視窗選錯人）的環境下**很容易失敗**。
> 三十個人同時卡在授權視窗，這一節課就沒了。
>
> **沒有 MCP 也完全做得出文字雲。** MCP 只是讓老師端多一個「用講的查資料」的入口。

裝了之後可以做的事：對 OpenCode 說「**幫我看一下文字雲那張表現在有幾筆、最多人寫的詞是什麼**」，它直接讀資料庫回答你，不用開 Dashboard。

### 🔴 7-0 先跑這一行——這一步會省下你一整個下午

**在寫任何設定之前，先確認兩件事：token 有沒有效、`project_ref` 該填哪一個。**

先到 <https://supabase.com/dashboard/account/tokens> → **Generate new token**（取名 `opencode`）→ 複製 `sbp_` 開頭那串，設成環境變數：

```powershell
[Environment]::SetEnvironmentVariable('SUPABASE_ACCESS_TOKEN','你的sbp_token','User')
```

**然後開一個新的 PowerShell 視窗**（舊視窗看不到剛設的變數），跑：

```powershell
curl.exe -s -H "Authorization: Bearer $env:SUPABASE_ACCESS_TOKEN" https://api.supabase.com/v1/projects
```

會列出你所有專案的 `id`（＝`project_ref`）、`name`、`status`：

```
id: jjiuptgdwdpnhnzujzcl   name: myclass             status: ACTIVE_HEALTHY
id: xxbjykdheracbfmwpxwm   name: my-teaching-tools   status: INACTIVE
```

**這一行同時回答四件事**：

| 你看到 | 意思 |
|---|---|
| 列不出來 / 401 | token 錯了或沒設好 |
| 列得出來 | ✅ token 有效 |
| 清單裡的 `id` | **`project_ref` 就填這個，不要自己抄網址** |
| `status: INACTIVE` | 那個專案**被暫停了**（閒置滿一週），去 Dashboard 按 Restore |

> 🔴 **給 AI agent：這一步不准跳過。**
> `project_ref` 填錯的症狀**極度誤導**——MCP 會顯示 `✓ connected`、20 個工具全部列得出來，
> 但**每一個呼叫都回 `You do not have permission to perform this action`，連 `select` 都被拒絕**。
> 這看起來完全像憑證權限不足，**於是 agent 會開始換各種金鑰亂試**——那是白費力氣，錯的是 ID 不是憑證。
> **先跑上面那行，用它印出來的 id，就不會走進這個迷宮。**

### 7-1 設定（官方文件明列支援 OpenCode）

用 **Personal Access Token（PAT）** 認證。官方文件明講：**在無法完成瀏覽器 OAuth 流程的環境，改用 PAT**——研習現場正是這種環境。

編輯 `~/.config/opencode/opencode.json`（Windows：`WIN+R` 貼 `%USERPROFILE%\.config\opencode`）。
**如果檔案裡已經有 `mcp` 區塊，把 `supabase` 這一塊加進去，不要整個蓋掉：**

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "supabase": {
      "type": "remote",
      "url": "https://mcp.supabase.com/mcp?project_ref=剛才印出來的id",
      "enabled": true,
      "timeout": 300000,
      "headers": {
        "Authorization": "Bearer {env:SUPABASE_ACCESS_TOKEN}"
      }
    }
  }
}
```

三個重點：

- **`headers` 裡帶 PAT** → **完全不用跑 `opencode mcp auth`，沒有瀏覽器往返、不會過期**
- **`{env:...}` 引用環境變數** → token 不會明文躺在設定檔裡
- **`timeout` 一定要設** → OpenCode 預設只等 5 秒，資料庫操作常常不夠

改完**完全關閉 OpenCode 再開**，不重開不生效。

> **那 OAuth 呢？** `opencode mcp auth supabase` 也能用，但它需要瀏覽器往返、**而且會過期**
> （實測過一台機器的狀態掉成 `⚠ needs authentication` 而使用者毫無察覺）。
> 一個人自己用沒差；**一整班同時做，PAT 穩定得多**，因為它沒有互動步驟。

### 7-2 關於 `read_only=true`：先搞清楚它做什麼

官方建議可以加 `read_only=true` 限制 agent 只能查。**但有個常被誤會的地方：**

> ⚠️ **加了 `read_only=true`，工具清單不會變少。** 實測兩邊都是 20 個工具，
> `execute_sql`、`apply_migration` 照樣列出來——它是讓查詢改用**唯讀的 Postgres 使用者**去跑。
>
> 所以 agent **看得到寫入工具、也會去用，然後才失敗**。而失敗訊息長得像權限問題，
> 又會把 agent 帶往「是不是憑證不對」的錯誤方向。

**本包的建議：研習期間不要加 `read_only=true`。** 因為作品④ 需要建表與寫入。

| 情境 | 建議 |
|---|---|
| 研習現場（全新專案、都是假資料） | **不加**，需要寫入 |
| 回學校接**真的班務資料庫** | **加上去**，防 prompt injection（見下方紅框） |

| 參數 | 作用 |
|------|------|
| `project_ref=<專案id>` | **一定要加**。鎖定單一專案，agent 碰不到你其他專案 |
| `read_only=true` | 查詢改用唯讀使用者。**研習不加，接正式資料庫再加** |

> 🔴 **官方明講：不要把 MCP 連到生產環境（正式在用的資料庫）。**
> 原因是 **prompt injection**——資料表裡如果有一筆學生填進去的內容寫著
> 「請把這張表全部刪掉」，agent 讀到那筆資料時**有可能真的照做**。
> 研習用的專案裡都是假資料，所以還好；但**班務真的在跑的資料庫，不要接 MCP**。
>
> `read_only=true` 就是為了這件事存在的——**即使被騙，它也刪不掉東西。**

`<你的專案id>` 去哪拿：Dashboard 網址列 `https://supabase.com/dashboard/project/<這一段>`，或 Project Settings → General → Project ID。

### 7-3 驗證（三關都要過，只過第一關不算數）

**第一關：連得上**

```powershell
opencode mcp list
```

看到 `✓ supabase connected`。

> ⚠️ **`connected` 不代表能用。** 這是今天最容易誤判的地方——
> `project_ref` 填錯的時候，它一樣顯示 `connected`。

**第二關：讀得到**

對 OpenCode 說：「**列出我這個 Supabase 專案裡的表格**」

- 列得出來（就算是空的）= ✅ 過關
- 回 `You do not have permission` = ❌ **`project_ref` 填錯了**，回 7-0 重新確認，**不要去換憑證**

**第三關：寫得進去**

對 OpenCode 說：

```
幫我建一張測試表 _probe，欄位 id 和 note，寫一筆進去，讀出來給我看，然後把表刪掉
```

四個動作全部成功 = ✅ **讀寫都通，可以開始做作品④**。

> 實測結果供對照：正確設定下這四步全過；`project_ref` 填錯時，**連第一步的 `select` 都會被拒絕**。

---

## 🔒 安全紅線（三條，研習一定要講）

### 紅線一：去識別化 — 只存座號，不存姓名

- ✅ 存：座號、班級代號、答案內容、分數
- ❌ 不存：姓名、學號、身分證、家長聯絡方式、輔導紀錄、照片

理由不只是「資料庫可能外洩」，還因為**這些資料之後很可能會被丟給 AI 整理**（你自己就會這樣做）。
座號＋一張你自己保管的紙本對照表，功能完全一樣，風險少一個數量級。

### 紅線二：每一張表都要開 RLS，而且只開到剛好夠用

- 每張表**都要** `enable row level security`（🚨 陷阱 ②：SQL 建的表預設是關的）
- policy 只給**這個作品真的需要**的動作。文字雲需要 INSERT ＋ SELECT，**就不要給 UPDATE 和 DELETE**——沒給，就沒有人能改別人的詞、沒有人能清空整張表
- 本包步驟 3-2 的範本刻意**只 grant `select, insert`**，就是這個道理

### 紅線三：Secret key 永不進前端、永不進 GitHub

- `sb_secret_...` **只有伺服器端會用到**。本包全程用不到它
- 不要貼進網頁、不要貼進 GitHub（連私有 repo 都不要）、不要貼進聊天視窗給 AI
- **萬一貼出去了**：立刻回 Dashboard → API Keys **把那把 key 撤銷（revoke）並重新產生**。
  刪掉檔案、刪掉訊息**沒有用**，key 已經在別人手上了 —— **換掉它才是唯一有效的處置。**

---

## 💡 為什麼「金鑰出現在網頁裡」不是漏洞（這段請講給老師聽）

老師第一次按 F12 看到自己的 key 出現在原始碼裡，一定會嚇一跳：「這樣不是被看光了？」

**是被看光了。而且這是設計，不是漏洞。**

`sb_publishable_...` 的中文意思就是「**可以公開的**」。它的角色不是「密碼」，是「**這棟樓的地址**」——
知道地址不代表可以進去，因為門口有警衛。**那個警衛就是 RLS。**

所以真正的重點是：

> ## 🔴 不設 RLS，等於把資料庫的門拆掉，放在馬路上。
>
> 而且門牌（金鑰）就貼在你的網頁原始碼上，任何人按 F12 就看得到。

**具體會發生什麼事**（不是嚇你，是這幾件事都不需要任何技術能力）：

| 後果 | 白話 |
|------|------|
| **全班資料被撈走** | 任何人可以一次把整張表下載回去——所有座號、所有答案、所有分數 |
| **分數被改** | 學生可以把自己那一列的分數改成 100，你的表看起來完全正常 |
| **整張表被清空** | 一個指令，全班一整個學期的紀錄消失，**而且沒有資源回收桶** |
| **額度被灌爆** | 有人寫個程式一直往裡面塞資料，把 500MB 塞滿，你的作品直接掛掉 |
| **而且——你不會收到通知** | 🔴 **以上四件事發生時，Supabase 不會寄信給你、Dashboard 不會跳警告。** 你會在下一節課打開網頁時才發現 |

最後一列才是關鍵：**沒有 RLS 的資料庫，不是「比較危險」，是「出事了你也不知道」。**

所以本包的 SQL 範本才會**堅持把 `enable row level security` 和 policy 寫在同一段裡**——
分開寫，就一定會有人只跑前半段。

---

## 常見坑

| 症狀 | 真正的原因 / 解法 |
|------|------------------|
| **`✓ connected` 但每個動作都回 `You do not have permission`，連 `select` 都不行** | 🔴 **`project_ref` 填錯了，不是憑證問題。**<br>這是最誤導人的一個坑——連得上、工具全部列得出來、但一動就死。<br>**不要去換金鑰**，回 7-0 跑那行 `curl` 對一下 id |
| 加了 `read_only=true` 之後，agent 還是去呼叫寫入工具然後失敗 | 正常。`read_only` **不會讓工具消失**（實測兩邊都 20 個），只是查詢改用唯讀使用者。研習期間不要加（見 7-2） |
| 專案連不上、Dashboard 顯示 paused | 免費專案閒置滿一週被自動暫停。按 **Restore** 就回來，資料完整保留。7-0 那行 `curl` 會直接顯示 `status: INACTIVE` |
| MCP 一直逾時 | `timeout` 沒設。OpenCode 預設只等 5 秒，要加 `"timeout": 300000` |
| 設定檔改完沒反應 | 沒有**完全關閉**再重開 OpenCode |


| 症狀 | 原因 / 解法 |
|------|------------|
| `permission denied for table xxx` | 🚨 **陷阱 ①**。回步驟 3-2 補跑那兩行 `grant`。**這是 2026-05-30 之後建的新專案最常見的第一個錯誤** |
| `new row violates row-level security policy`（42501） | 🚨 **陷阱 ③**。少了 SELECT policy。補 3-2 的第二條 policy。**補之前先去 Table Editor 看有沒有已經寫進去的重複資料** |
| Table Editor 顯示 `Unrestricted` 紅字 | 🚨 **陷阱 ②**。RLS 沒開，跑 `alter table public.<表名> enable row level security;` |
| Dashboard 找不到 `anon` / `service_role` | 🚨 **陷阱 ④**。改用 `sb_publishable_...`；舊的收在 Legacy API keys 分頁 |
| 自己送得出去，但別人送的我看不到 | 少了步驟 3-5 的 `alter publication supabase_realtime add table ...` |
| 送出後畫面沒反應，也沒有錯誤 | 先去 **Table Editor 看資料在不在**。在 → 是前端顯示的問題；不在 → 看 F12 Console 的紅字對照本表 |
| 學生送了兩三次，表裡出現重複資料 | 陷阱 ③ 的典型後果。修好 policy 後，在 Table Editor 手動刪掉重複列即可 |
| 專案顯示 **Paused** | 閒置超過 7 天。Dashboard 點 **Restore**，資料還在。然後回去做步驟六 |
| 排程設了還是被暫停 | 看步驟 **6-5**：GitHub Actions 在 repo 閒置 60 天後會自己停用 |
| 建不了第 3 個專案 | 免費方案同時只能 2 個 active。先去 Dashboard 把不用的專案 Pause 掉 |
| `curl.exe` 在 PowerShell 行為很怪 | Windows 要打 `curl.exe`（有 `.exe`），不然會叫到 PowerShell 內建的別名 |
| 想串多個指令卻語法錯誤 | PowerShell 5.1 **不支援 `&&`**，改用 `;` |
| `opencode mcp auth supabase` 瀏覽器沒反應 | 學校網路擋掉了。用手機熱點測一次；還是不行就**放棄 MCP**，步驟一～六不受影響 |
| OAuth 授權完 OpenCode 還是看不到工具 | **完全關閉** OpenCode 再重開（關視窗不算）。再用 `opencode mcp debug supabase` 看原因 |
| `opencode.json` 存檔後 OpenCode 起不來 | JSON 格式：最後一項後面不能有逗號、引號要用英文半形 `"` |

---

## ✅ 成功訊號（機器可判讀）

依序執行，agent 依規則判定：

```sql
-- 在 Supabase SQL Editor 執行
select relrowsecurity from pg_class where relname = '<你的表名>';
select policyname, cmd from pg_policies where tablename = '<你的表名>';
select has_table_privilege('anon', 'public.<你的表名>', 'select') as can_read,
       has_table_privilege('anon', 'public.<你的表名>', 'insert') as can_write;
select tablename from pg_publication_tables where pubname = 'supabase_realtime';
```

| 檢查項 | 通過條件 |
|--------|---------|
| `SB_KEY` | 步驟 2-3 的 `curl` 回傳 JSON，**非** `Invalid API key` |
| `SB_EXPOSED` | `can_read` 與 `can_write` 皆為 `true`（陷阱 ①）|
| `SB_RLS_ON` | `relrowsecurity` 為 `true`（陷阱 ②）|
| `SB_POLICY_INSERT` | `pg_policies` 有一列 `cmd = 'INSERT'` |
| `SB_POLICY_SELECT` | `pg_policies` 有一列 `cmd = 'SELECT'`（陷阱 ③）|
| `SB_REALTIME` | `pg_publication_tables` 清單含該表（只在要即時更新時才需要）|
| `SB_WRITE_E2E` | 從網頁送出一筆後，Table Editor 中該列存在 |
| `SB_LIVE_E2E` | 第二台裝置送出後，第一台**未重新整理**即出現新資料 |
| `SB_KEEPALIVE` | GitHub Actions 手動 Run 一次為綠色，且紀錄含 `PING_OK` |
| `SB_MCP` | （選用）`opencode mcp list` 中 `supabase` 已授權 |

最後輸出這一段（沒做的填 `SKIP`，失敗填 `FAIL:<原因>`）：

```
SUPABASE_PACK_STATUS=OK
SB_KEY=OK
SB_EXPOSED=OK
SB_RLS_ON=OK
SB_POLICY_INSERT=OK
SB_POLICY_SELECT=OK
SB_REALTIME=OK
SB_WRITE_E2E=OK
SB_LIVE_E2E=OK
SB_KEEPALIVE=OK
SB_MCP=SKIP
```

> 🔴 **`SB_RLS_ON`、`SB_POLICY_SELECT`、`SB_EXPOSED` 任一為 `FAIL`，`SUPABASE_PACK_STATUS` 就必須是 `FAIL`。**
> 這三項就是四個陷阱的機器版本，**不准跳過、不准「先繼續之後再補」。**

---

## 完成回報格式

```md
## Supabase 即時資料庫連接結果

- Supabase 專案：`<專案名>`（region：<區域>）
- 建專案時「Automatically expose new tables」：有勾 / 沒勾 / 找不到該選項
- 金鑰類型：sb_publishable_（新式）/ anon（舊式，建議換新）
- 金鑰驗證（SB_KEY）：PASS / FAIL
- 資料表：`public.<表名>`（欄位：<列出>）

### 四個陷阱檢查
- ① Data API 曝露（SB_EXPOSED）：PASS / FAIL
- ② RLS 已啟用（SB_RLS_ON）：PASS / FAIL　← 建表方式：SQL / Table Editor
- ③ INSERT ＋ SELECT policy 皆存在（SB_POLICY_*）：PASS / FAIL
- ④ 使用新式金鑰、前端無 secret key：PASS / FAIL

### 端對端
- Realtime 已加入 publication（SB_REALTIME）：PASS / FAIL / SKIP
- 網頁寫入 → Table Editor 看得到（SB_WRITE_E2E）：PASS / FAIL
- 第二台裝置即時同步（SB_LIVE_E2E）：PASS / FAIL / SKIP
- GitHub Pages 網址：<url>

### 防暫停
- workflow 檔案：`.github/workflows/supabase-keep-alive.yml` 已建立 / 未建立
- 兩個 Secret 已設定：是 / 否
- 手動 Run 測試（SB_KEEPALIVE）：PASS / FAIL
- 已告知使用者「GitHub Actions 60 天無活動會自動停用」：是 / 否

### MCP（選用）
- 是否安裝：是 / 否（研習建議「否」）
- read_only 與 project_ref 參數：已加 / 未加

### 安全確認
- 資料表內只有座號、無姓名等個資：是 / 否
- 前端與 repo 中不含 sb_secret_：是 / 否
- 已向使用者說明「這組 policy 只適用研習假資料」：是 / 否

### 待處理
- <把 FAIL 的項目與原因列在這裡；全部 PASS 就寫「無」>
```

---

## 如果做壞了，怎麼重來

對 OpenCode 說：「**Supabase 這一包做壞了，幫我把表砍掉重建。**」

手動版（在 SQL Editor 跑）：

```sql
drop table if exists public.wordcloud;
```

> ⚠️ **`drop table` 會把資料一起刪掉，而且沒有資源回收桶。**
> 研習用的假資料無所謂；如果裡面已經有你要留的東西，**先去 Table Editor 匯出 CSV 再刪**。

刪完回**步驟三**重貼一次那段 SQL 即可。

**其他層級的重來**：

| 想重來的東西 | 做法 |
|-------------|------|
| 金鑰外洩了 | Dashboard → API Keys → 撤銷該把 key → 產一把新的 → 更新網頁與 GitHub Secret |
| MCP 連壞了 | `opencode mcp logout supabase`，再 `opencode mcp auth supabase` |
| 整個專案不要了 | Project Settings → General → 最下面刪除專案（會要你手動打出專案名稱才肯刪）|

---

## 下一步

- 作品要上線、要備份 → **`07-github`**
- 不需要「同時多人寫」的資料（報名、繳交、點名）→ 用 **`05-sheets-gas`**，比較單純
- 想在網頁上做語音／字幕素材 → **`02-file-toolkit`**

> 📌 想用 Firebase 而不是 Supabase？看 `extras/firebase`。
> 那是**替代方案**，不在研習主線，內容也未經 v2 重新查證。
