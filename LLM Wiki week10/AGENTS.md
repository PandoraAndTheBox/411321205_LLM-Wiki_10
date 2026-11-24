# AGENTS.md — AI Agent Security 論文／文獻 LLM Wiki 操作手冊

本文件定義這個個人／讀書會知識庫的架構、寫作規範與 Agent 標準作業流程（Schema）。本知識庫參考 Karpathy 的 LLM Wiki 模式設計，核心目標是**累積跨文獻的結構化理解，而非每次重新閱讀原始資料**。

---

## 1. 知識庫定位與核心原則

- **用途**：課程與讀書會的知識累積（主題目前聚焦於 AI Agent Security，亦可隨閱讀擴展）。
- **語言規範**：
  - 內文以**繁體中文**撰寫。
  - 專有名詞、攻擊手法、防禦架構與技術術語保留英文（例如 *Prompt Injection*、*Defense in Depth*、*Least Privilege*）。
  - 數學符號或量化公式使用標準 LaTeX（`$...$` 或 `$$...$$`）。
- **連結規範**：
  - 嚴格使用**標準 Markdown 相對路徑連結**（例如 `[Prompt Injection](../threats/prompt-injection.md)`），**禁止使用** Obsidian `[[wikilinks]]`。
- **中立與去雜訊原則**：
  - 原始網頁文章常包含網站導覽列、頁尾、CTA 與廠商產品推銷（例如商業平台廣告或特定 SaaS 功能介紹）。Ingest 時必須**完全過濾導覽列雜訊與商業推銷**，只萃取中立的技術機制、威脅原理、架構設計原則與實證案例。
- **誠實與邊界原則**：
  - 絕不編造原始資料中沒有提到的數據、實驗結果或引用來源。
  - 若原始檔案內容殘缺或語意不清，必須在結果摘要中明確標註。

---

## 2. 目錄結構與各層職責

```text
.
├── AGENTS.md              # 本規範文件（Schema）
├── index.md               # 全站內容目錄（按分類列出所有頁面與一句話摘要）
├── log.md                 # 唯讀追加的時間軸紀錄（append-only）
├── raw/                   # 原始資料層（只讀不改）
└── wiki/                  # Agent 維護的知識庫本體
    ├── sources/           # 單篇來源導讀頁（精簡導讀 + 核心論點索引）
    ├── threats/           # 攻擊向量、漏洞類型與威脅模式
    ├── defenses/          # 防禦架構、設計模式與緩解原則
    ├── concepts/          # 基礎概念、系統元件與信任邊界定義
    └── synthesis/         # 跨來源比較、演進脈絡與讀書會綜合討論
```

### 各資料夾詳細定位
- **`raw/`（不可變層）**：
  - 存放使用者放入的 `.md` / `.txt` 原始文獻。**Agent 只能讀取，絕對不可修改、重新命名或刪除 `raw/` 內的任何檔案。**
- **`wiki/sources/`（單篇來源導讀）**：
  - 採**精簡導讀 + 核心論點索引**格式。只記錄該篇的來源資訊、一句話核心主張、關鍵貢獻，以及「拆解後的連結索引」（連往對應的 `threats/`、`defenses/`、`concepts/`）。**不在來源頁重複抄寫已在主題頁展開的細節。**
- **`wiki/threats/`（威脅與攻擊）**：
  - 以具體威脅或攻擊向量為單位（例如 Prompt Injection、Memory Poisoning、Tool Misuse & Privilege Escalation），記錄定義、成因、攻擊面、代表性案例與相關防禦連結。
- **`wiki/defenses/`（防禦與架構原則）**：
  - 停留在**觀念與架構原則層級**（不寫實作 checklist 或程式碼設定教學），聚焦於「解決什麼威脅、核心機制為何、系統層級定位與權衡／限制」。
- **`wiki/concepts/`（基礎概念）**：
  - 跨多篇文獻反覆出現的基礎元件或抽象概念（例如 Persistent Memory、Trust Boundary、Agentic Attack Surface）。
- **`wiki/synthesis/`（綜合比較與讀書會筆記）**：
  - 跨來源的對比表格、不同標準／架構之間的互補或衝突分析、讀書會討論問題，以及從 Query 沉澱下來的高價值綜合分析。

---

## 3. 檔名與 YAML Frontmatter 規範

- **檔名規則**：所有 `wiki/` 底下的檔案一律使用 **小寫英文 + 連字號（kebab-case）**，避免空白、中文與特殊符號（例如 `owasp-llm01-2025-prompt-injection.md`、`memory-poisoning.md`）。
- **YAML Frontmatter**：每頁開頭必須包含精簡的 YAML metadata：

```yaml
---
title: 頁面標題
type: source | threat | defense | concept | synthesis
tags: [tag1, tag2]
updated: YYYY-MM-DD
# 以下欄位僅 type: source 必填：
source_file: raw/原始檔名.txt
source_url: https://...
published: YYYY-MM-DD # 若原文無標註可填 unknown
---
```

- **間接引用處理規則**：
  - 當某篇來源引用了重要但 `raw/` 尚未收錄全文的論文或資安事件（例如 arXiv 論文或真實資安事件報告）時，**先在對應的 `threats/` 或 `concepts/` 頁面中作為案例簡述，並附上原文提供的外部 URL**。
  - **不要**為沒有全文的間接引用建立空的 stub 頁面；待日後 `raw/` 補入全文時，再正式建立 `wiki/sources/` 頁面並補齊雙向連結。

---

## 4. 標準作業流程（Workflows）

### A. Ingest（半自動／批次收錄）
當使用者要求處理 `raw/` 中的指定檔案或「所有尚未收錄的新檔案」時：
1. **盤點未收錄來源**：比對 `raw/` 檔案清單與 `index.md` / `wiki/sources/` 的 `source_file`，找出尚未處理的來源。
2. **過濾與萃取**：閱讀原始檔案，過濾網頁導覽列、頁尾與商業推銷內容，萃取出核心論點、威脅、防禦原則、概念與間接引用案例。
3. **建立來源導讀頁**：在 `wiki/sources/` 建立精簡導讀頁，列出核心論點並指向相關主題頁。
4. **更新或建立主題頁**：
   - 檢查 `wiki/threats/`、`wiki/defenses/`、`wiki/concepts/` 是否已有相關頁面；若有則**整合補充**（包含標註不同來源的互補觀點或衝突處），若無則新建頁面。
   - 視需要更新或新增 `wiki/synthesis/` 中的跨來源比較。
5. **維護雙向連結**：確認來源頁與主題頁之間、主題頁與主題頁之間的標準 Markdown 相對路徑連結皆正確有效。
6. **更新 `index.md` 與 `log.md`**：
   - 更新 `index.md` 的對應分類條目與一句話摘要。
   - 在 `log.md` 末尾追加一筆紀錄，格式為：`## [YYYY-MM-DD] ingest | <來源標題或批次名稱>`。
7. **輸出檢查清單**：完成所有寫入後，向使用者回報一份簡明清單，包含：
   - 新增的頁面清單
   - 更新的頁面清單
   - 本次發現的跨來源關聯、概念重疊或矛盾之處
   - 原始資料若有缺漏或需使用者確認的項目

### B. Query（知識庫問答與沉澱）
當使用者針對知識庫提問時：
1. **先查 `index.md`**：根據問題定位相關的 Wiki 頁面，再精讀這些頁面內容（必要時才回頭查對 `raw/` 細節）。
2. **附帶引用回答**：回答時必須以標準 Markdown 相對路徑附上資料來源頁面。
3. **沉澱高價值產出**：若該次回答產出了有價值的跨文獻比較、架構整合或讀書會討論結論，主動詢問使用者是否將其整理存為 `wiki/synthesis/` 新頁面，並同步更新 `index.md` 與 `log.md`。

### C. Lint（定期健康檢查）
當使用者要求檢查知識庫品質，或累積數次 Ingest 後：
1. 檢查是否存在**斷掉的相對路徑連結**（檔案不存在或路徑錯誤）。
2. 檢查是否存在**孤兒頁面**（沒有任何其他頁面連入的檔案）。
3. 檢查是否有**重複定義**或**前後矛盾未標註**的說法。
4. 檢查是否有被多篇來源提到、但尚未獨立成頁的重要概念／威脅／防禦。
5. 將檢查結果回報使用者，並在 `log.md` 追加一筆 `## [YYYY-MM-DD] lint | <檢查摘要>`。

### D. 簡報製作（Slide Graph Workflow）
當使用者要求將知識庫內容整理為簡報時，依循專案通用 Skill [slide-graph-workflow](.agents/skills/slide-graph-workflow/SKILL.md) 的狀態圖執行：
1. **大綱階段（Node 1 → HITL Gate 1）**：先產出結構化大綱，直接交由使用者確認後才往下推進。
2. **逐頁內容階段（Node 2 Parallel Fan-out → Node 2-Check → HITL Gate 2）**：依大綱平行撰寫各頁內容與圖表規格，完成跨頁去重、連貫性與事實檢查後，直接交由使用者確認。
3. **投影片渲染階段（Node 3 → Check 3）**：套用使用者指定之視覺風格輸出 HTML 簡報；若遇排版爆框，於 Node 3 內調整版面或拆分為兩頁，不回頭刪減已核准之文字內容。

