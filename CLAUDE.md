# OmniDoc 知識煉金庫 — 專案規則（給 AI 的開發指南）

> 這份文件是本專案的「單一規則來源」。任何 AI（Claude Code、Codex、Gemini…）在新增筆記、修改頁面之前，都必須先讀完這份文件並遵守。
> `AGENTS.md` 只是指向本文件的捷徑，規則一律以本文件為準。

---

## 1. 專案目的

OmniDoc 知識煉金庫是一個「把課堂講義變成學習筆記」的靜態網站。
開發者提供 PDF 講義、講義截圖或零散原文，AI 扮演「頂級學術研究助理」，把資料交叉比對、深度提煉，輸出成**結構固定、可直接複習**的 HTML 筆記頁，並掛到首頁的筆記庫。

網站要達成三件事：

1. **讀得懂**：每篇筆記都有核心摘要、依講義章節組織的深度解析、名詞庫與測驗。
2. **找得到**：首頁可以依課程篩選、全文搜尋。
3. **練得到**：測驗可以直接點選作答，名詞可以用閃卡複習（在本機版首頁）。

- 線上網站：https://chungweichang0918.github.io/NUTC-1111232077-omnidoc/
- 原始碼：https://github.com/chungweichang0918/NUTC-1111232077-omnidoc

### 使用的 AI 模型

| 用途 | 模型 / 工具 |
|---|---|
| 網站開發、規則撰寫、筆記校對 | Claude Opus 5.5（Claude Code） |
| 筆記自動生成（本機） | gemma3:12b（Ollama，執行於 Mac mini，經 Tailscale 連線） |

---

## 2. 目錄結構與命名規則

```
/
├── index.html                      首頁（筆記庫）＋內建範例筆記（AI Agent）＋本機 AI 筆記生成器
├── CLAUDE.md                       本文件：專案規則
├── AGENTS.md                       指向 CLAUDE.md 的捷徑（給其他 AI 工具）
├── README.md                       專案簡介（給人看的）
├── notes/                          已發佈的筆記頁
│   └── <課程代號>/
│       └── ch<兩位數章節>-<英文主題>.html
├── config.local.js                 本機 AI 連線設定（含個人位址，不上傳，已列在 .gitignore）
└── sources/                        原始講義 PDF（著作權屬於授課老師，不上傳，已列在 .gitignore）
```

### 命名規則（必須遵守）

| 項目 | 規則 | 範例 |
|---|---|---|
| 首頁 | 只有根目錄的 `index.html` 叫 index，**筆記頁不可以命名為 index.html** | `index.html` |
| 課程代號資料夾 | 英文小寫，單字用 `-` 連接 | `database`、`operating-system`、`generative-ai` |
| 筆記檔名 | `ch` + 兩位數章節 + `-` + 英文主題（小寫、`-` 連接） | `ch03-relational-database.html` |
| 非章節型筆記 | 直接用英文主題 | `ai-agents.html` |
| 筆記 HTML 內的課程名稱 | 用中文課程全名，全站一致 | `資料庫系統` |

已登記的課程：

| 課程代號 | 課程名稱 | 卡片圖示 |
|---|---|---|
| `database` | 資料庫系統 | 🗄️ |
| `generative-ai` | 生成式 AI | 🤖 |

新增課程時，先在這張表加一列，再建立資料夾。

---

## 3. 網頁整體風格（Notion 式極簡學術風）

- **配色**（CSS 變數，定義在 `:root`）：背景 `--bg: #FFFFFF`、文字 `--text: #2F3437`、次要文字 `--muted: #787774`、邊框 `--border: #E9E9E7`、強調色 `--accent: #2383E2`、提示框底色 `--callout: #F1F1EF`。標籤底色：`--tag-blue`、`--tag-green`、`--tag-yellow`、`--tag-red`、`--tag-purple`。
- **字體**：`system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Noto Sans TC", "Microsoft JhengHei", sans-serif`。
- **排版**：內文最大寬度 `850px`（`.container`）、行距 `1.7`、區塊之間留白充足；引用區塊（`blockquote`）有左側邊框；手機寬度（≤ 640px）不能出現水平捲軸，表格放在 `.table-wrap` 裡水平捲動。
- **單一檔案**：每個 HTML 頁面的 CSS 都寫在自己的 `<style>` 內，不建立獨立的 CSS 檔。**筆記頁的 `<style>` 直接複製 `index.html` 的 `<style>`**，確保全站外觀一致。
- **不使用外部框架**：不引入 Bootstrap、Tailwind 等；唯一允許的外部資源是本機生成器用的 PDF.js（cdnjs）。
- **語言**：全站使用繁體中文（台灣用語，例如「資料庫」「資料表」「欄位」），不可出現簡體字。

---

## 4. 筆記頁強制結構

每一篇筆記（不論是 AI 對話產生，還是本機生成器產生後發佈）都必須依序包含下面 5 個區塊，並使用指定的標籤與 class。
**範本**：`notes/database/ch03-relational-database.html` 就是符合規則的完整範例，新增筆記時以它為範本複製。

### 4.0 頁面外框

```html
<body>
<div class="topbar"><div class="container">
  <a class="brand" href="../../index.html">⚗️ OmniDoc <span>知識煉金庫</span></a>
  <nav class="topnav"><a href="../../index.html#library">筆記庫</a><a href="#g-s7">名詞庫</a><a href="#g-s8">測驗</a></nav>
</div></div>
<main><article class="container" style="padding-top:48px">
  …下面 5 個區塊…
</article></main>
<footer><div class="container">OmniDoc 知識煉金庫 · 課程名稱 · <a class="back-top" href="../../index.html#library">← 回筆記庫</a></div></footer>
<script>…互動測驗（複製範本頁最下方的腳本）…</script>
</body>
```

### 4.1 Title & Meta（標題與來源）

```html
<header>
  <div class="breadcrumb"><a href="../../index.html#library">知識煉金庫</a> / 資料庫系統 / CH3</div>
  <h1>📒 資料庫系統 CH3：關聯式資料庫</h1>
  <div class="props">
    <div class="k">建立日期</div><div class="v">YYYY-MM-DD</div>
    <div class="k">課程</div><div class="v"><span class="tag purple">資料庫系統</span></div>
    <div class="k">生成方式</div><div class="v">使用的模型與流程，例如「OmniDoc 本機 AI 生成器（gemma3:12b）＋人工校對」</div>
    <div class="k">資料來源</div><div class="v"><ol class="sources"><li>ch03.pdf</li></ol></div>
  </div>
</header>
```

### 4.2 Executive Summary（核心摘要）

3–5 句話總結最核心的觀念，涵蓋資料從頭到尾的重點：

```html
<div class="callout"><div class="callout-icon">💡</div><div class="callout-body">
  <p class="callout-title">核心摘要</p><p>……</p>
</div></div>
```

摘要之後放目錄：`<nav class="toc"><p class="toc-title">目錄</p><ol class="plain"><li><a href="#g-s1">1. …</a></li>…</ol></nav>`（`ol.plain` 不自動編號，因為 h2 本身已有編號）。

### 4.3 Deep Dive（深度解析）— 筆記主體

- 全部放在 `<section class="deep-dive">` 裡。
- **分節依講義本身的章節結構**：講義有 3-1、3-2、3-2-1… 這類小節編號時，每個二層章節（3-1、3-2…）一節；底下小節超過 3 個就平均拆成「（一）（二）」兩節。
- `<h2 id="g-s1">1. 節標題</h2>`：id 依序為 `g-s1`、`g-s2`…（名詞庫、測驗接在最後）。
- `<h3>1.1 概念名稱（3-2-1）</h3>`：編號用「節.小節」，括號內只能放**講義中實際存在**的小節編號；不可以用圖表編號（「3-13 外鍵示意圖」是圖號）或頁碼當標題。
- 每個重點說明「是什麼、為什麼、如何運作、例子」，大量使用 `<ul><li>` 與 `<strong>` 粗體關鍵字，**絕對不可一句帶過**。
- 有對比概念（A 與 B、不同類型、優缺點）時**必須**用含 `<thead>`、`<tbody>` 的 `<table>`，並包在 `<div class="table-wrap">` 裡；每一列欄位數要和表頭一致。
- SQL 放在 `<pre><code>`，而且要能實際執行：先建立被參考的父資料表、先新增父表資料。
- 每節最後可以有 `<h3>N.x 補充重點</h3>`，條列講義中有、但正文沒寫到的內容，每條結尾標註頁碼「（第 18 頁）」。

### 4.4 Terminology（專有名詞庫）

```html
<section class="terminology"><h2 id="g-s7">📖 專有名詞庫</h2>
  <dl class="terms">
    <dt>主鍵<span class="en">Primary Key</span></dt><dd>用來識別記錄的唯一性，不可重複及空值。</dd>
  </dl>
</section>
```

- 至少 10 個名詞；講義小節標題的名詞（例如簡單屬性、參考完整性規則）**一定要收**。
- 中文名詞與英文原文放在同一個 `<dt>`，英文放 `<span class="en">`；同一名詞不可出現兩次。
- 每個定義只能依據講義中「這個名詞自己的」說明，**不可把別的名詞的定義套過來**。

### 4.5 Self-Test（複習測驗）

```html
<section class="self-test"><h2 id="g-s8">📝 複習測驗</h2><div class="quiz">
  <div class="q">
    <p class="q-head">題目……</p>
    <ul><li>(A) …</li><li>(B) …</li><li>(C) …</li><li>(D) …</li></ul>   <!-- 選擇題才有 -->
    <p class="q-hint">提示：……</p>
    <details><summary>展開參考答案</summary><div class="answer"><p>正確答案：(B) …</p><p>解析：……</p></div></details>
  </div>
</div></section>
```

- 預設 5 題（至少 3 題），題型混合：概念解釋、比較辨析、情境應用；題目分散涵蓋前、中、後段章節。
- **選擇題必須在題目下方列出 (A)～(D) 四個選項**，不可以只寫「下列何者正確？」卻沒有選項；選項不可附「(錯誤：…)」說明。
- 參考答案寫「正確答案：(X) …」＋解析（說明其他選項錯在哪）；各題正確答案要分散在 A～D，不可全部同一個代號。
- 互動作答由頁面最下方的腳本處理（點選項會顯示對錯並展開答案），題目本身維持純 HTML。

---

## 5. 內容正確性規則

1. **以講義為準**：講義的定義、條件、規則、範例表格、SQL 都要寫進對應段落；不可捏造講義中沒有的事實。
2. **補充要標註**：講義沒有、但為了填補邏輯斷層而加入的背景知識，句尾標註「（補充）」。
3. **圖片內容要謹慎**：講義截圖轉錄可能有錯字（例如把「編號」辨識成「偏號」），以講義文字中「」標示的用詞為準；不可根據截圖推測欄位設定，也不要描述軟體介面（工具列、狀態列、視窗）。
4. **不用 Markdown**：HTML 中不可出現 `**粗體**`、反引號、`*` 條列，一律用 `<strong>`、`<code>`、`<ul><li>`。
5. **人工校對**：發佈前人工檢查名詞庫定義與測驗答案；刪除或修正沒有講義依據的內容，並在頁面「生成方式」註明「＋人工校對」。

---

## 6. 新增一篇筆記的標準流程（AI 必須照做）

1. **放資料**：把講義 PDF 放進本機的 `sources/<課程代號>/`（不會上傳）。
2. **產生筆記**，二擇一：
   - **A. 在 AI 對話中產生**：AI 讀取講義後，依第 4、5 節的結構與規則，直接寫出 `notes/<課程代號>/ch<NN>-<主題>.html`（以範本頁為基礎複製外框與 `<style>`）。
   - **B. 用本機生成器**：在本機打開 `index.html` →「＋ 上傳 PDF / 圖片」→ 選「詳細模式」生成 → 檢查後按「⬇️ 下載 HTML」，再由 AI 把下載檔轉成第 4.0 節的頁面格式（加上頂部導覽、麵包屑、課程、生成方式、頁尾與互動測驗腳本），放到 `notes/<課程代號>/`。
3. **掛到首頁**：在 `index.html` 的 `<div class="cards" id="cards">` 裡、`＋ 上傳 PDF / 圖片` 按鈕之前，依第 7 節格式新增一張卡片。首頁的統計數字、課程篩選、搜尋會自動讀取卡片上的資料，不需要改程式。
4. **自我檢查**（全部打勾才算完成）：
   - [ ] 5 個區塊齊全，h2 的 id 連續、目錄連結都能跳轉
   - [ ] 所有選擇題都有 (A)～(D) 選項，答案分散
   - [ ] 沒有簡體字、沒有 Markdown 符號、沒有 `index.html` 以外的 index 檔名
   - [ ] 手機寬度沒有水平捲軸
   - [ ] 頁面中沒有任何個人資訊（IP 位址、API Key、電子郵件）
5. **提交**：commit 訊息格式為「新增筆記：<課程名稱> CH<N> <主題>」或「修正：<說明>」，推上 `main` 分支後 GitHub Pages 會自動更新。
6. **回報**：完成後以一句話回報新增的檔案路徑與首頁卡片位置；在新筆記檔最下方以 HTML 註解標記「已登記到首頁卡片」。

---

## 7. 首頁卡片格式

```html
<a class="card pub" href="notes/database/ch03-relational-database.html" data-id="pub-database-ch03"
   data-course="資料庫系統" data-topics="6" data-terms="24" data-quiz="10"
   data-keywords="這篇筆記的重要名詞，以空白分隔，供首頁搜尋使用">
  <div class="card-icon" aria-hidden="true">🗄️</div>
  <div class="tags"><span class="tag purple">資料庫系統</span><span class="tag yellow">本機 AI 生成</span><span class="tag green">已校對</span></div>
  <h3>資料庫系統 CH3：關聯式資料庫</h3>
  <p>一句話摘要（約 50 字）。</p>
  <div class="card-meta">建立日期 · 生成模型 · N 節 · N 個名詞 · N 題測驗</div>
</a>
```

| 屬性 | 說明 |
|---|---|
| `class="card pub"` | `pub` 代表已發佈的筆記頁，首頁統計會把它算進去 |
| `data-id` | `pub-<課程代號>-ch<NN>`，全站唯一 |
| `data-course` | 中文課程名稱，必須和第 2 節的課程表一致（首頁會自動產生篩選按鈕） |
| `data-topics` / `data-terms` / `data-quiz` | 深度解析節數、名詞數、測驗題數（用於首頁統計） |
| `data-keywords` | 搜尋用關鍵字：節標題與名詞庫中的名詞 |
| 標籤 | 第一個固定是紫色課程標籤；AI 生成的加黃色「本機 AI 生成」，人工校對後加綠色「已校對」 |

---

## 8. 修改 `index.html` 時的規則

- `index.html` 維持**單一檔案**（HTML、CSS、JavaScript 都在同一檔），不拆出外部檔案。
- 首頁內建的 AI Agent 範例筆記（`#note`）同樣遵守第 4 節的 5 區塊結構。
- 本機 AI 生成器的程式碼（上傳、讀圖、逐節撰寫、後處理）修改後，必須用模擬伺服器實際跑過一次完整生成再提交。
- 模型輸出的 HTML 一律經過 `sanitize()` 清理（只允許白名單標籤與 class），不可直接插入頁面。
- 生成器的個人設定只能放在 `config.local.js` 或瀏覽器的 localStorage，**不可寫死在 `index.html`**。

---

## 9. 隱私、安全與著作權

- `config.local.js`（含 Mac mini 的 Tailscale 位址）**永遠不上傳**；不在任何檔案中寫入 IP 位址、API Key、電子郵件。
- 講義 PDF 的著作權屬於授課老師，**不上傳到公開儲存庫**；網站只放整理後的筆記。
- 本機生成器只在本機（`file://`）使用；公開網站是 https，瀏覽器會擋住連到本機 AI 的 http 連線，這是預期行為。

---

## 10. AI 生成行為規則

- **高密度輸出**：不准敷衍！解析必須詳盡、有邏輯，填補資料間的邏輯斷層，確保筆記具備學術價值與份量。
- **禁止廢話**：被要求產生筆記時，直接寫出完整的 HTML 檔案，不要在檔案內容中夾雜「好的，這是您的筆記」等對話。
- **自動索引**：新增筆記後，一定要同時更新首頁卡片（第 7 節），不可只新增檔案。
- **先讀後改**：修改既有頁面前先讀取原檔，只改需要改的部分，保留既有結構與 id。
