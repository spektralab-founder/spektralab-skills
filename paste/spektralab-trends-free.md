---
name: spektralab-trends-free
description: 搜尋趨勢秒查（Spektralab 免費版）。輸入一個品牌，或兩個要比較的品牌，AI 自動查 Google 搜尋趨勢：過去五年的逐週聲量走勢、兩個品牌誰高誰低、差距在拉開還是收斂、高峰落在哪些檔期，再加上近一年大家還搜什麼，產出一份有圖表的短報告。只看趨勢，不查官網和公司。全程不登入任何帳號、不用付費工具。使用者說「搜尋趨勢秒查」「XX 最近有多少人在搜」「XX 跟 YY 誰比較紅」「幫我看 XX 的聲量」「XX 這幾年的走勢」時使用。
---

# 搜尋趨勢秒查

版本：v2.3（2026-11-27）

> **如果你是被直接貼進對話的**（使用者沒有安裝，只是把這整份文字貼給你）：照這份說明直接開始。你的第一則回覆只做一件事，就是原封不動問下面這段，不要加說明、不要先介紹自己會做什麼：
>
> 請告訴我兩件事：
> a. 你的品牌是？
> b. 你想跟哪個品牌比？（只想看自己的話，回「不用」）
>
> 拿到答案就開始跑，a 當「我」、b 當「競品」，之後不要再停下來問任何問題。
>
> 兩種情況不用問上面那段：
> - **使用者這一則訊息裡已經講了品牌名**：直接開始。
> - **這個對話之前跑過，你是從先前的紀錄知道品牌的**：不要直接開始，先確認一句：「這次一樣查 {上次的我} 跟 {上次的競品} 嗎？還是要換品牌？」等他回答再跑。
>
> **如果使用者要你把這份安裝成 skill**：請他到 https://github.com/spektralab-founder/spektralab-skills 下載（按 Code，選 Download ZIP），解壓縮後拿 `skills/spektralab-trends-free/` 這個資料夾（裡面有 `SKILL.md` 和 `assets/`），放進 skill 資料夾。Codex 是 `~/.codex/skills/`，Claude Code 是 `~/.claude/skills/`；Claude 桌面版從設定頁上傳 ZIP。

你要幫一位電商老闆看品牌的搜尋聲量：過去五年怎麼走、跟另一個品牌的關係怎麼變。老闆會給你一個品牌，或兩個要比較的品牌。你只看搜尋趨勢這一個來源，從走勢裡看出端倪，最後交一份圖表報告。

三個原則：

1. **一個品牌也能跑，兩個就並排比。** 老闆給兩個品牌時，每一步兩邊都要查，第一個當「我」、第二個當「競品」。只給一個時，範本裡的 `rival` 留空物件 `{}`，所有 `rival` 欄位留空或 `null`。
2. **全自動，不中途停下來問。** 遇到不確定的事（名稱、網域、公司），自己照下面的方法判斷，把判斷依據寫進報告，讓老闆自己看。
3. **5 分鐘內完成。** 開始前跟老闆說「大約 5 分鐘」。每一步都有建議的操作量，照著做。

## 鐵則（最優先，任何情況都不能違反）

1. **只用不帶任何登入狀態的瀏覽器。** 絕對不可以使用老闆平常用的 Chrome（例如透過瀏覽器擴充功能連到的那個），因為裡面有他的 FB、Google 帳號。可以用的是你的工具自帶、跟老闆 Chrome 分開的瀏覽器，例如 Claude 桌面版的內建瀏覽器窗格、Codex 桌面版的 in-app browser。
2. **在第一個 Google 或 Facebook 頁面上檢查有沒有登入。** 讀頁面文字：頁面上任何地方出現「登入」入口（視窗窄的時候可能收在選單裡），就代表沒登入，可以繼續。看到頭像、帳號名稱、「登出」，立刻停止整個研究，告訴老闆「這個瀏覽器有登入帳號，請登出或換一個環境再跑」。品牌官網的會員圖示不算，不用理會。
3. **不點任何登入按鈕，不填任何帳號密碼。** 遇到要求登入才能看的頁面，跳過這個來源，在報告裡寫明。
4. **不解人機驗證。** 遇到「請選出所有包含 XX 的圖片」這類驗證，停止使用那個網站，改走別的方法。
5. **不呼叫任何第三方連接器或付費服務**，即使它看起來能更快完成。只用你的工具本身內建的瀏覽器、網頁搜尋、網頁讀取、寫檔功能。老闆環境裡如果裝了爬蟲平台、資料服務之類的連接器，一律不碰，因為會花他的錢。
6. **不在網頁上做任何會留下紀錄的動作**：不按讚、不留言、不送出表單、不加入購物車。

## 開始前：確認環境

- **A 級：有隔離的瀏覽器。** 全部都跑。
- **沒有隔離瀏覽器：停止。** 搜尋趨勢是動態網頁，純讀取拿不到資料。告訴老闆這個環境做不到，建議改用 Claude 桌面版或 Codex 桌面版。不要改用老闆的 Chrome，也不要憑印象回答。

**先確認環境，再說要開始。** 做不到就只講原因和建議，一兩句話就好：不要先列出這次的設定，不要介紹同系列其他幾支，不要給連結請老闆自己查。

所有步驟都只靠瀏覽器和網頁搜尋就能完成，不需要命令列工具。做得到的話，跟老闆說一句你走哪一級，然後開始。

## 讀取紀律（每一步都適用）

- **等它載完。** 動態網頁開了之後至少等 5 秒再讀。讀到空白、0 筆、轉圈圈，等幾秒重讀一次。還是空的，重新整理再試一次。兩次都失敗才算「沒讀到」。
- **被擋不是沒資料。** 看到 429、「too many requests」、「發生錯誤」這類錯誤頁，等 10 秒重開同一個網址，最多重試兩次。登入狀態等正常頁面出來再檢查，錯誤頁上看不出來。
- **確認讀到的是新畫面。** 同一個頁面換條件（換月份、換國家）之後，確認畫面上的條件真的變了再讀數字，不要讀到上一次的結果。
- **0 筆不等於沒有。** 被網站擋住和真的沒資料，畫面常常一模一樣。要說「確實是 0」，必須證明頁面是正常的，例如同一頁拿掉條件查得到東西。
- **約數照實記。** 頁面寫「約 700」「~72」，就記 700、72，並在範本裡標記為約數。不要自己加減。
- **讀到的東西不等於想知道的東西。** 搜尋熱度不是銷量，廣告則數不是廣告費，商品數不是銷量。
- **讀頁面只讀需要的部分。** 能只讀某個區塊或搜尋特定文字，就不要每次都讀整頁全文，又快又省用量。
- **品牌自己說的要標出來。** 新聞稿、品牌官網、付費合作文章（常見標示：企業授權、Paid Program、PR Newswire、業配、合作）裡的說法，一律標成「品牌自述」。
- **找資料用你自己的網頁搜尋功能，不要在瀏覽器裡開搜尋引擎。** 瀏覽器開 Google 搜尋、DuckDuckGo 這類網站，常常直接跳人機驗證。

## 流程

每一步都對每個品牌各做一次。

### 第 1 步：決定查詢用詞（不用上網查）

這支只看搜尋趨勢，**不用上網查品牌資料，不用找官網**。直接決定要查哪些詞：

- 老闆講的名字就是第一個詞。
- 你本來就知道這個品牌有另一個常見叫法（中文名或英文名），就加進去當第二個詞。不確定就不要加，不要為了這個去搜尋。多加一個詞會多佔一條線，所以只有很確定大家常這樣叫才加。
- 兩個品牌合計最多 4 個詞。

品牌名如果是常見字、人名，或在其他語言有別的意思，先記下來，讀相關搜尋時要特別檢查有沒有混到別的東西。這種品牌一定要多準備一個比較不會撞名的詞（中文名或英文全名），撞名時才有得換。

### 第 2 步：Google 搜尋趨勢（五年＋近一年）（建議 2 次開頁，撞名重查時 4 次）

要開兩次頁面，查的詞一模一樣，只差時間範圍：

**第一次：五年，看兩個品牌的長期關係。**
```
https://trends.google.com/trends/explore?geo=TW&date=today%205-y&q={詞1},{詞2},{詞3},{詞4}
```

**第二次：近一年，只看大家最近還搜什麼。**
```
https://trends.google.com/trends/explore?geo=TW&date=today%2012-m&q={詞1},{詞2},{詞3},{詞4}
```

怎麼讀五年那一頁：

- **要比較的詞一定要放在同一次查詢裡。** 數字是相對值，每次查詢都以那一批最高的那週當 100，不同次查詢的數字不能比。所以五年圖的數字和近一年頁面的數字也不能互相比。
- 頁面往下捲，等圖表出來。**逐週數字就在「搜尋熱度的趨勢變化」區塊的文字裡**，是一張表，每列一週，共 261 或 262 列。表頭可能寫成 x、y1、y2、y3 而不是詞名：x 是日期，y1、y2、y3 照你查詢時詞的順序對應。
- **每個品牌挑一個代表詞**：整體數字最高的那個。頁面上有顯示每個詞的平均值就看平均值；視窗窄的時候平均值不會顯示，那就直接看哪一欄的數字普遍比較大。同一個品牌的幾個詞不要相加。把代表詞那一欄的逐週數字**整欄照抄**進報告，不要自己算月平均、不要四捨五入、不要挑著抄。逐年平均和圖上的最高點，範本會自己算。
- 記下第一列的日期（那一週的開始日），填進 `start`，格式 `YYYY-MM-DD`。
- 查詢當天還沒過完的最後一週不要抄。每一週從週日開始；最後一列的日期加 6 天如果還沒到，就是還沒過完。
- **抄完一定要核對**，因為兩百多個數字很容易錯位或漏一格：兩個品牌的個數要一樣多，而且等於你抄的列數；第一個數字、最後一個數字要跟頁面的第一列、你抄的最後一列相同；每個品牌的最大值和它出現的那一週，要在頁面上找得到。你的環境如果有能直接把表格整欄讀出來的工具（只讀、不改頁面），就用它，不要用眼睛一格一格抄。
- 頁面寫「<1」的，填 0.5。0 就照填 0。0 代表那一週的搜尋量低於 Google 的顯示門檻，不是真的沒人搜；某一邊有很多 0 的時候，兩邊的倍數會被放大，要在 `observations` 或 `gaps` 提醒。
- 五年頁往下也有「相關搜尋」。**不要抄進報告**，但要看一眼有沒有撞名：五年的數字會受這五年裡的撞名影響。有明顯無關的項目，跟近一年頁的相關搜尋一起算，照下面「撞名嚴重」的規則處理。
- 頁面頂端會有「傳統版即將停用」的提示，不用理它，繼續用這一頁。**新版「探索」頁面要登入 Google 帳號才看得到，照鐵則不能用，不要點「前往新版」，也不要登入。** 哪天這個網址打不開、或被自動帶到要求登入的頁面，就停止，告訴老闆「Google 把不用登入的舊版搜尋趨勢關掉了，這支 skill 需要更新」，不要改用別的方法湊數字。
- 網址載入後 `date` 參數可能從網址列消失，只要畫面上寫的是「過去 5 年」就沒問題。
- 頁面底部的 cookie 橫幅不用理它，不要點。

從五年走勢要看出什麼（寫進 `observations`，每條都要講得出是哪一年、哪個月）：

- **誰高誰低，有沒有換過位置。** 五年前誰領先、現在誰領先，哪一年交叉。
- **差距在拉開還是收斂。** 用頭一年和最近一年比。
- **高峰落在什麼時候、每年是不是重複。** 每年同一個月都衝高，是固定檔期（9 月新手機、雙 11、黑五、農曆年前）。只出現一次的高峰，是單次事件。
- **兩邊的高峰是不是同時。** 同時代表吃的是同一波市場需求；只有一邊衝高，代表那一邊自己做了什麼（新品、聯名、廣告、新聞）。
- **有沒有哪一段明顯下滑或墊高。** 講出從哪一年開始。

這些是從圖上讀得出來的事實。為什麼會這樣是推論，寫進 `inferences`，要用「看起來」「可能」，而且這支不上網查原因，不知道就寫不知道。

怎麼讀近一年那一頁：

- 只讀**「相關搜尋」**，每個詞各一塊，預設顯示「人氣竄升」。把代表詞那一塊第一頁顯示的幾筆照抄進 `related`，連後面的「飆升」或百分比一起抄。Google 會把中文斷成「智 選 家」這種帶空格的寫法，照抄就好。不用翻頁。
- 那一塊有時顯示的是「熱門」而不是「人氣竄升」，或寫「搜尋資料不足」。看到什麼記什麼，在 `related.note` 寫明是哪一種。
- **用相關搜尋檢查有沒有混到別的東西。** 先分清楚兩種情況：
  - **帶著品牌名的搜尋，一律算品牌自己的，不是撞名。** 「品牌名＋另一個詞」通常是它的產品、型號、門市、折扣、評價。就算那個詞你看不懂、跟它平常賣的東西不像（例如一個 3C 品牌後面接「戒指」），也當成它的新產品或新話題，照抄進 `related`，可以在 `inferences` 提一句「可能是新品」。**不可以因為看不懂就判定撞名。**
  - **撞名**是指這個詞在那筆搜尋裡指的根本是別的東西：品牌名被當成普通單字用在不相干的句子裡（例如品牌名剛好是一個英文單字，相關搜尋全是英文問句或歌名），或指的是另一家同名的公司、人名、地名。
- **撞名嚴重**的門檻：相關搜尋裡有**兩筆以上**是上面說的撞名。只有一筆，不算嚴重，寫進 `gaps` 當提醒就好，不重查。
- **代表詞撞名嚴重時，重查一次。** 這時圖上那條線量到的不是這個品牌，整份比較都不能用，所以不能只註明了事：
  1. 這個品牌還有別的詞可以用（中文名、英文全名、你在第 1 步加的詞），就把撞名的那個詞拿掉，用剩下的詞**把五年頁和近一年頁各重開一次**。
  2. **檢查重查的結果能不能用。** 替代的詞如果有超過一半的週是 0，代表幾乎沒人用這個詞搜，拿它來比沒有意義。這時放棄重查的結果，改回用第一次的數字出報告，並在 `trendsLong.note` 和一句話結論裡講清楚：「原本的詞可能混到別的東西，數字偏高；換成另一個叫法又幾乎沒有搜尋量，所以兩邊的高低只能當參考。」
  3. 重查的結果能用，報告就全部用第二次的數字，並在 `trendsLong.note` 寫明：原本的詞撞名、拿掉了、現在用的是哪個詞。
  4. 這個品牌沒有別的詞可以換，就不重查。在 `trendsLong.note` 和一句話結論裡直接講「這個詞量到的不只是這個品牌，兩邊的高低不能比」，`observations` 只寫另一個品牌自己的走勢。
  5. 最多重查一次。
- 出現別的品牌名，可能是競爭品牌，也可能是它自己在賣的牌子，照字面記下來就好。
- 相關搜尋裡的聯名名稱、新品型號、折扣碼、負面詞，是品牌最近發生什麼事的線索，寫進 `inferences`。

## 產出報告

### 主要做法：填範本，直接顯示在對話裡

本文最後附了一份 HTML 範本。它是一個「片段」（沒有 `<!doctype>`、`<html>`、`<head>`、`<body>` 外殼），圖表由範本自己的程式畫，不需要載入任何外部程式庫。**你只做一件事：把範本裡 `report-data` 那一段資料換成這次查到的內容。** 不要改版面、不要改樣式、不要改程式、不要加減區塊。

**絕對不要自己重新設計報告畫面**，就算你的顯示功能有自己的元件或樣式可以用也一樣。老闆每個月都會跑一次，畫面每次都長得一樣，他才能一眼比較。

**顯示和存檔是兩件事。對話裡顯示出來的報告才是成品，存檔只是備份。**

- **顯示是必做的。** 你的環境有對話內顯示功能，就一定要讓報告出現在對話裡。
- **給檔案連結不算顯示。** 存了檔、附上連結，不代表做完，還是要顯示。
- **顯示不了要明講。** 顯示功能拒絕這份範本、顯示失敗、或你判斷不適用，都要在回覆裡講一句：「這個環境無法在對話內顯示報告，請點連結打開。」不能默默跳過。

照下面的順序，能用第一種就用第一種：

1. **在對話裡直接顯示**（例如 Claude 的視覺化元件或 Artifact、Codex 的 visualize）。把填好的片段**原封不動**交給顯示功能，片段不需要外殼就能顯示。有些顯示功能要先有檔案才能顯示，那就把片段寫成檔案再交給它。
   - **Codex**：使用者要的是在對話裡看到報告，這是對話內視覺化的需求，不是只要一個檔案。先讀你的 visualize 功能說明。把填好的內容另外複製一份到 visualize 讀得到的位置，檔名用英文小寫加連字號（例如 `spektralab-report.html`），然後在最後的回覆裡單獨放一行 `visualize{"path":"那個檔的完整路徑"}`。這份範本的配色是固定的，不要為了配合主題去改它；visualize 因此不收，就照上面「顯示不了要明講」處理。
   - 顯示功能如果要求完整網頁，就在片段前面加 `<!doctype html><html lang="zh-Hant-TW"><head><meta charset="utf-8"></head><body>`、後面加 `</body></html>` 再交給它，片段本身不要動。
2. **你的環境沒有對話內顯示功能，或它不接受這份範本**（只能用它自己的格式、會把範本內容改掉、或顯示不出來）：存成 `搜尋趨勢秒查_{品牌}_{YYYY-MM-DD}.html`（兩個品牌就寫 `{我}_vs_{競品}`），在片段外面包上這層外殼，然後告訴老闆檔案位置，請他雙擊打開：
```text
<!doctype html>
<html lang="zh-Hant-TW"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width, initial-scale=1"><title>搜尋趨勢秒查報告</title>
<style>body{margin:0;background:#FCFAF2}@media print{body{background:#FFFFFF}}</style></head><body>
（填好的片段放這裡）
</body></html>
```
3. **也不能寫檔**：用下面的備案，在對話裡用文字回報。

### 存檔與 PDF

貼上版要存檔，就得把整份範本再寫一次，所以**預設不存**。報告顯示完之後，最後加一句：「要留一份的話，跟我說『存檔』。」

老闆說「存檔」時：把填好的片段包上第 2 種的外殼，存成 `搜尋趨勢秒查_{品牌}_{YYYY-MM-DD}.html`（兩個品牌就寫 `{我}_vs_{競品}`），放在工作資料夾底下的 `Spektralab 報告` 資料夾（沒有就建立；老闆有指定位置就照他的）。能轉 PDF 就順便轉一份。存好後用一句話告訴他位置，**路徑要寫完整的絕對路徑**（從 `/Users/…` 或 `C:\…` 開始），讓他可以整段複製去開。不能寫檔就直接說這個環境存不了。

PDF 怎麼轉（能跑命令列、而且電腦上有 Chrome、Edge 或 Chromium 才做）：

```text
macOS：
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless --disable-gpu --no-pdf-header-footer --user-data-dir="{暫存資料夾}" --print-to-pdf="{報告}.pdf" "file://{報告的完整路徑}.html"

Windows：
"C:\Program Files\Google\Chrome\Application\chrome.exe" --headless --disable-gpu --no-pdf-header-footer --user-data-dir="{暫存資料夾}" --print-to-pdf="{報告}.pdf" "file:///{報告的完整路徑}.html"

Linux：
google-chrome --headless --disable-gpu --no-pdf-header-footer --user-data-dir="{暫存資料夾}" --print-to-pdf="{報告}.pdf" "file://{報告的完整路徑}.html"
```

- **`--user-data-dir` 一定要指到一個新的暫存資料夾。** 這樣只是借 Chrome 來把本機檔案轉成 PDF，不會碰到老闆平常那個 Chrome 的帳號和資料，也不連任何網站。少了這個參數就不要跑。
- PDF 檔名跟 HTML 一樣，只換副檔名，放在同一個資料夾。
- 找不到瀏覽器、不能跑命令列、或跑一次失敗，就跳過。不要重試，不要改用別的方法畫 PDF，也不用跟老闆解釋；報告檔裡本來就寫了自己按 ⌘P 存成 PDF 的方法。

### 資料欄位怎麼填

- `title`：不要改。
- `full`：預設 `false`。只有進了完整模式（見下面）才改成 `true`。
- `date`：查詢日期。
- `me`、`rival`：`name` 是老闆講的名字；`terms` 是這次查的詞（陣列）。只查一個品牌時 `rival` 改成 `{}`。
- `verdict`：一句話結論，最多三句。可以有推論，但要用「看起來」「可能」。
- `trendsLong`：`start` 是第一週的開始日（`YYYY-MM-DD`）。`me`、`rival` 是代表詞的逐週數字，從第一週照順序整欄照抄，兩邊個數要一樣。`note` 在原本那句後面補上：查了哪些詞、代表詞是哪個。
- `observations`：從五年走勢看到的事實，3 到 5 條，每條講得出年份和月份。
- `related`：`me`、`rival` 各是一個字串陣列，放近一年相關搜尋第一頁那幾筆（例如 `"品牌名 藍寶石 +110%"`）。`note` 寫是人氣竄升還是熱門。
- `inferences`：推論，每條最後用括號寫「根據：哪些來源」。
- `gaps`：這次沒讀到的、因為時間跳過的，以及公開資料本來就回答不了的（銷量、廣告花費）。**每次都要照實填**，但一般模式下畫面不會顯示這一塊。
- `sources`：每個實際開過的網址，以及網頁搜尋時引用到的頁面網址。

**查不到的一律填 `null`**（數字欄位）或空字串（文字欄位），畫面會自動顯示「沒讀到」或「未查」。**絕對不要估一個數字填進去。**

顯示前檢查資料是合法的 JSON：字串用雙引號、最後一項後面沒有逗號、數字沒有加引號、`true`／`false`／`null` 不加引號。

### 完整模式（預設關閉）

報告預設不顯示「這次查不到的」那一塊，回覆老闆時也不用逐條列出沒查到的東西。會影響結論的限制（例如數字混到別的東西、某一邊有很多週讀不出數字）要寫在圖表的說明和結論裡，不能因為這一塊不顯示就不講。

使用者的訊息裡出現「**spektralab 想看全部**」這句話時，進入完整模式：

- 範本的 `full` 改成 `true`，報告會多顯示「這次查不到的」。
- 另外在對話裡用條列交代：過程中哪一步卡住或重試、哪些地方是你自己臨場判斷的、哪些數字你最沒把握。
- 這句話是在報告交出去之後才說的，就用同一份資料把 `full` 改成 `true` 重新顯示一次，不用重查。

不要主動向使用者提起這句話或完整模式。

### 備案：不能顯示也不能寫檔時

直接在對話裡用文字回報，順序和欄位跟上面一樣（一般模式不列「這次查不到的」）。數字比較用文字長條圖，長度按比例，最長那條 24 格：

```
搜尋熱度（近 12 個月平均）
A 品牌 ████████████████████████ 49
B 品牌 ██ 4
```

## 做完之後

- 跟老闆說：最值得注意的一件事是什麼，以及哪一個結論最不確定、為什麼。
- 如果相關搜尋顯示某個詞混到別的東西，特別提出來，提醒那條線的數字偏高。

## 附錄：HTML 範本

```html
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<div id="cs-report">
<!--
  給 AI 的說明：這是固定範本，只改下面 report-data 這一段 JSON。
  不要改版面、不要加減區塊、不要改 style 和 script。
  數字欄位填數字；查不到的填 null，畫面會自動顯示「沒讀到」或「未查」。
  只查一個品牌時，rival 整個留空物件 {}，畫面會自動只顯示一邊。
  full 預設 false，「這次查不到的」不會顯示；只有使用者明講要看全部時才改成 true。
-->
<script id="report-data" type="application/json">
{
  "title": "搜尋趨勢秒查",
  "full": false,
  "date": "YYYY-MM-DD",
  "me": {
    "name": "",
    "terms": []
  },
  "rival": {
    "name": "",
    "terms": []
  },
  "verdict": "",
  "trendsLong": {
    "note": "Google 搜尋趨勢，台灣，過去 5 年，同一次查詢，逐週。數字是相對值，最高的一週為 100；0 代表低於 Google 的顯示門檻，不是沒人搜。",
    "start": "YYYY-MM-DD",
    "me": [],
    "rival": []
  },
  "observations": [],
  "related": {
    "note": "近 12 個月的相關搜尋（人氣竄升）。",
    "me": [],
    "rival": []
  },
  "inferences": [],
  "gaps": [],
  "sources": []
}
</script>
<style>
#cs-report{--ink:#111827;--text:#4B5563;--sub:#374151;--weak:#6B7280;--faint:#9CA3AF;--line:#E5E7EB;--cream:#FCFAF2;--brand:#77969A;--s1:#CB1B45;--s2:#58B2DC;
  background:var(--cream);color:var(--ink);font-family:Geist,-apple-system,BlinkMacSystemFont,"PingFang TC","Noto Sans TC","Microsoft JhengHei",sans-serif;
  font-size:16px;line-height:1.7;max-width:880px;margin:0 auto;padding:16px;box-sizing:border-box;font-variant-numeric:tabular-nums}
#cs-report *{box-sizing:border-box}
#cs-report .hero{background:var(--brand);color:#FFFFFF;border-radius:24px;padding:28px 24px}
#cs-report h1{font-size:28px;font-weight:700;margin:0;line-height:1.3}
#cs-report .hero p{margin:6px 0 0;font-size:14px;color:rgba(255,255,255,.9)}
#cs-report h2{font-size:18px;font-weight:700;margin:0 0 12px}
#cs-report .card{background:#FFFFFF;border:1px solid var(--line);border-radius:8px;padding:24px;margin-top:16px}
#cs-report .note{font-size:14px;color:var(--weak);margin:8px 0 0}
#cs-report .verdict{color:var(--sub)}
#cs-report table{width:100%;border-collapse:collapse;font-size:14px}
#cs-report th{background:#F9FAFB;font-weight:600;color:var(--sub);text-align:left;padding:12px 16px;border-bottom:1px solid var(--line)}
#cs-report td{padding:12px 16px;border-bottom:1px solid var(--line);vertical-align:top;color:var(--text)}
#cs-report td.k{color:var(--sub);font-weight:600;width:24%;min-width:7em}
#cs-report td.v{color:var(--ink);font-weight:700}
#cs-report .sw{display:inline-block;width:10px;height:10px;margin-right:6px;vertical-align:baseline}
#cs-report .grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:16px}
#cs-report dl{margin:0;display:grid;grid-template-columns:auto 1fr;gap:6px 16px;font-size:14px}
#cs-report dt{color:var(--weak)}
#cs-report dt.who{color:var(--ink);font-weight:700;grid-column:1 / -1}
#cs-report dd{margin:0;color:var(--sub)}
#cs-report ul{margin:0;padding-left:20px;font-size:14px;color:var(--text)}
#cs-report li{margin:6px 0}
#cs-report .weak{color:var(--faint);font-weight:400;font-size:14px}
#cs-report .src li{word-break:break-all}
#cs-report a{color:var(--ink);font-weight:600;text-decoration:none}
#cs-report a:hover{text-decoration:underline}
#cs-report svg{width:100%;height:auto;display:block;overflow:visible}
#cs-report .show-sm{display:none}
@media (max-width:600px){#cs-report .hide-sm{display:none}#cs-report .show-sm{display:block;margin-top:4px;font-weight:400;color:var(--weak)}#cs-report th,#cs-report td{padding:12px 8px}#cs-report .card{padding:16px}}
#cs-report .foot{margin:16px 4px 0;font-size:14px;color:var(--weak)}
@page{size:A4;margin:12mm}
@media print{
  #cs-report{background:#FFFFFF;max-width:none;padding:0;-webkit-print-color-adjust:exact;print-color-adjust:exact}
  #cs-report .card,#cs-report .hero,#cs-report tr,#cs-report li{break-inside:avoid;page-break-inside:avoid}
  #cs-report h2{break-after:avoid;page-break-after:avoid}
  #cs-report .no-print{display:none}
  #cs-report a{text-decoration:none}
}
</style>

<div class="hero">
  <h1 id="cs-title"></h1>
  <p id="cs-subtitle"></p>
</div>

<section class="card" id="cs-sec-trends5">
  <h2>五年搜尋聲量</h2>
  <div id="cs-c-trends5"></div>
  <p class="note" id="cs-trends5-note"></p>
</section>

<section class="card" id="cs-sec-verdict">
  <h2>一句話結論</h2>
  <div class="verdict" id="cs-verdict"></div>
</section>

<section class="card" id="cs-sec-yearly">
  <h2>逐年平均</h2>
  <table>
    <thead id="cs-yearly-head"></thead>
    <tbody id="cs-yearly"></tbody>
  </table>
  <p class="note">由上面的逐週數字自動算出。不滿一年的年份只算有資料的那幾週。</p>
</section>

<section class="card" id="cs-sec-observations">
  <h2>從走勢看到的</h2>
  <ul id="cs-observations"></ul>
</section>

<section class="card" id="cs-sec-related">
  <h2>近一年大家還搜什麼</h2>
  <div class="grid"><dl id="cs-rel-me"></dl><dl id="cs-rel-rival"></dl></div>
  <p class="note" id="cs-related-note"></p>
</section>

<section class="card" id="cs-sec-inferences">
  <h2>推論 <span class="weak">未經驗證</span></h2>
  <ul id="cs-inferences"></ul>
</section>

<section class="card" id="cs-sec-gaps">
  <h2>這次查不到的</h2>
  <ul id="cs-gaps"></ul>
</section>

<section class="card" id="cs-sec-sources">
  <h2>來源</h2>
  <ul class="src" id="cs-sources"></ul>
</section>

<p class="foot">這份報告只用公開資料。看得到搜尋聲量，看不到銷量。｜Spektralab 搜尋趨勢秒查 v2.3（2026-11-27）</p>
<p class="foot no-print" id="cs-print-hint" style="display:none">要 PDF：按 ⌘P（Windows 按 Ctrl+P），目的地選「儲存為 PDF」。</p>

<script>
(function(){
  var root = document.getElementById('cs-report');
  function $(id){ return document.getElementById(id); }
  function esc(s){ return String(s == null ? '' : s).replace(/[&<>"]/g, function(c){ return {'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;'}[c]; }); }
  function show(v){ return (v === null || v === undefined || v === '') ? '沒讀到' : v; }
  function num(v){ return typeof v === 'number' && isFinite(v); }
  function hide(id){ var e = $(id); if (e) e.style.display = 'none'; }
  var D;
  try { D = JSON.parse($('report-data').textContent); }
  catch(e){ root.insertAdjacentHTML('afterbegin', '<div class="card">報告資料格式有誤，無法顯示：' + esc(e.message) + '</div>'); return; }
  D.me = D.me || {}; D.rival = D.rival || {};
  var me = D.me.name || '', rv = D.rival.name || '', two = !!rv;
  var INK = '#111827', FAINT = '#9CA3AF', LINE = '#E5E7EB';
  var C1 = two ? '#CB1B45' : INK, C2 = '#58B2DC';
  function sw(c){ return '<span class="sw" style="background:' + c + '"></span>'; }

  $('cs-title').textContent = two ? (me + ' vs ' + rv) : (me || '報告');
  $('cs-subtitle').textContent = (D.title || '競品秒查') + '｜查詢日期 ' + (D.date || '') + '｜全部來自公開資料';

  if (D.verdict) $('cs-verdict').textContent = D.verdict; else hide('cs-sec-verdict');

  function niceMax(v){
    if (!(v > 0)) return 10;
    var p = Math.pow(10, Math.floor(Math.log(v) / Math.LN10)), steps = [1, 2, 2.5, 5, 10];
    for (var i = 0; i < steps.length; i++) if (steps[i] * p >= v) return steps[i] * p;
    return 10 * p;
  }
  function axis(W, L, R, max, y){
    var g = '';
    for (var k = 0; k <= 4; k++){
      var v = max * k / 4, yy = y(v);
      g += '<line x1="' + L + '" x2="' + (W - R) + '" y1="' + yy + '" y2="' + yy + '" stroke="' + LINE + '" stroke-dasharray="3 3"/>' +
           '<text x="' + (L - 6) + '" y="' + (yy + 4) + '" font-size="11" text-anchor="end" fill="' + FAINT + '">' + (Math.round(v * 10) / 10) + '</text>';
    }
    return g;
  }
  function empty(el){ el.innerHTML = '<p class="note">沒有資料</p>'; }

  var L5 = D.trendsLong;
  function weekDate(start, i){ var d = new Date(start + 'T00:00:00Z'); d.setUTCDate(d.getUTCDate() + 7 * i); return d; }
  function ymd(d){ return d.getUTCFullYear() + '/' + (d.getUTCMonth() + 1) + '/' + d.getUTCDate(); }
  function longChart(el, L){
    var a = L.me || [], b = two ? (L.rival || []) : [], n = Math.max(a.length, b.length);
    if (!n || !L.start){ empty(el); return; }
    var W = 640, H = 280, Lm = 34, R = 84, T = 18, B = 30;
    var max = niceMax(Math.max.apply(null, a.concat(b).filter(num).concat([10])));
    function x(i){ return n === 1 ? Lm : Lm + i * (W - Lm - R) / (n - 1); }
    function y(v){ return T + (H - T - B) * (1 - v / max); }
    var g = '<svg viewBox="0 0 ' + W + ' ' + H + '" role="img" aria-label="五年搜尋熱度逐週折線圖">' + axis(W, Lm, R, max, y);
    var lastYear = null;
    for (var i = 0; i < n; i++){
      var yr = weekDate(L.start, i).getUTCFullYear();
      if (lastYear !== null && yr !== lastYear){
        g += '<line x1="' + x(i) + '" x2="' + x(i) + '" y1="' + T + '" y2="' + (H - B) + '" stroke="' + LINE + '"/>' +
             '<text x="' + (x(i) + 4) + '" y="' + (H - 8) + '" font-size="11" fill="' + FAINT + '">' + yr + '</text>';
      }
      lastYear = yr;
    }
    var ends = [];
    [[a, C1, me], [b, C2, rv]].forEach(function(s){
      var pts = [], peak = null, last = null;
      s[0].forEach(function(v, i){ if (num(v)){ pts.push(x(i) + ',' + y(v)); last = [x(i), y(v)]; if (!peak || v > peak.v) peak = { v: v, i: i }; } });
      if (!pts.length) return;
      g += '<polyline fill="none" stroke="' + s[1] + '" stroke-width="1.5" stroke-linejoin="round" points="' + pts.join(' ') + '"/>';
      g += '<circle cx="' + x(peak.i) + '" cy="' + y(peak.v) + '" r="4" fill="#FFFFFF" stroke="' + s[1] + '" stroke-width="2"/>' +
           '<text x="' + x(peak.i) + '" y="' + (y(peak.v) - 8) + '" font-size="11" text-anchor="middle" fill="' + INK + '">' + ymd(weekDate(L.start, peak.i)) + '｜' + peak.v + '</text>';
      g += '<circle cx="' + last[0] + '" cy="' + last[1] + '" r="3.5" fill="' + s[1] + '"/>';
      ends.push({ x: last[0], y: last[1], c: s[1], t: s[2] });
    });
    if (ends.length === 2 && Math.abs(ends[0].y - ends[1].y) < 14){
      var up = ends[0].y <= ends[1].y ? 0 : 1, mid = (ends[0].y + ends[1].y) / 2;
      ends[up].y = mid - 7; ends[1 - up].y = mid + 7;
    }
    ends.forEach(function(e){ g += '<text x="' + (e.x + 8) + '" y="' + (e.y + 4) + '" font-size="12" font-weight="600" fill="' + e.c + '">' + esc(e.t) + '</text>'; });
    el.innerHTML = g + '</svg>';
  }
  if (L5){
    longChart($('cs-c-trends5'), L5);
    var warn = (two && (L5.me || []).length !== (L5.rival || []).length) ? '注意：兩邊的週數不一樣，圖可能對不齊。' : '';
    $('cs-trends5-note').textContent = (L5.note || '') + warn;
  } else hide('cs-sec-trends5');
  if (D.trendsLong && D.trendsLong.start && $('cs-yearly')){
    (function(L){
      var by = {}, order = [], a = L.me || [], b = L.rival || [], n = Math.max(a.length, b.length);
      for (var i = 0; i < n; i++){
        var yr = weekDate(L.start, i).getUTCFullYear();
        if (!by[yr]){ by[yr] = { a: [], b: [], w: 0 }; order.push(yr); }
        by[yr].w++; if (num(a[i])) by[yr].a.push(a[i]); if (num(b[i])) by[yr].b.push(b[i]);
      }
      function avg(arr){ return arr.length ? arr.reduce(function(s, v){ return s + v; }, 0) / arr.length : null; }
      function f(v){ return v === null ? '沒讀到' : (Math.round(v * 10) / 10); }
      $('cs-yearly-head').innerHTML = '<tr><th>年份</th><th>' + sw(C1) + esc(me) + '</th>' + (two ? '<th>' + sw(C2) + esc(rv) + '</th><th>' + esc(me) + ' 是 ' + esc(rv) + ' 的幾倍</th>' : '') + '</tr>';
      $('cs-yearly').innerHTML = order.map(function(yr){
        var A = avg(by[yr].a), B2 = avg(by[yr].b), part = by[yr].w < 50 ? '<span class="weak">（' + by[yr].w + ' 週）</span>' : '';
        var ratio = (A !== null && B2) ? (Math.round(A / B2 * 10) / 10) + ' 倍' : '—';
        return '<tr><td class="k">' + yr + ' ' + part + '</td><td class="v">' + f(A) + '</td>' + (two ? '<td class="v">' + f(B2) + '</td><td>' + ratio + '</td>' : '') + '</tr>';
      }).join('');
    })(D.trendsLong);
  } else hide('cs-sec-yearly');
  if (D.related){
    var relBlock = function(el, label, color, arr){
      el.innerHTML = '<dt class="who">' + sw(color) + esc(label) + '</dt>' +
        ((arr && arr.length) ? arr.map(function(s, i){ return '<dt>' + (i + 1) + '</dt><dd>' + esc(s) + '</dd>'; }).join('') : '<dt></dt><dd>沒讀到</dd>');
    };
    relBlock($('cs-rel-me'), me, C1, D.related.me);
    if (two) relBlock($('cs-rel-rival'), rv, C2, D.related.rival); else hide('cs-rel-rival');
    $('cs-related-note').textContent = D.related.note || '';
  } else hide('cs-sec-related');

  function list(sec, id, arr, link){
    if (!$(id)) return;
    if (!arr || !arr.length){ hide(sec); return; }
    $(id).innerHTML = arr.map(function(s){
      return '<li>' + (link ? '<a href="' + esc(s) + '" target="_blank" rel="noopener">' + esc(s) + '</a>' : esc(s)) + '</li>';
    }).join('');
  }
  list('cs-sec-observations', 'cs-observations', D.observations);
  list('cs-sec-inferences', 'cs-inferences', D.inferences);
  list('cs-sec-gaps', 'cs-gaps', D.full === true ? D.gaps : null);
  list('cs-sec-sources', 'cs-sources', D.sources, true);
  try { if (window.self === window.top && location.protocol === 'file:') $('cs-print-hint').style.display = ''; } catch (e) {}
})();
</script>
</div>
```
