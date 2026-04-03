---
name: leespec-design
description: "LeeSpec 統一設計指令 — 合併腦力激盪與文件協作，引導用戶從想法到設計。支援 Quick/Full 模式。適用於新功能、架構變更、技術規格、提案等所有需要先設計再動手的工作。觸發詞：leespec design、設計、brainstorm、draft spec。"
---

# LeeSpec Design — 統一設計流程

將想法轉化為完整設計，透過自然對話進行協作。本指令合併了腦力激盪（brainstorming）與文件協作（doc-coauthoring）的最佳實踐。

<HARD-GATE>
在 Phase 4（Quality Gate）完成且用戶批准前，不得寫任何程式碼、建立專案、或執行任何實作動作。所有專案不論複雜度皆適用。
</HARD-GATE>

## Mode Selection — 進入時先詢問

進入本指令時，**第一件事**是詢問用戶：

> 你想用哪種模式？
> - **Quick mode**（精簡設計）— 適合小改動、config 修改、bug fix。幾個問題就搞定。
> - **Full mode**（完整流程）— 適合新功能、架構變更、跨模組改動。逐段設計 + 品質檢查。

等用戶選擇後再繼續。

---

## Quick Mode（精簡路徑）

### Step 1: 探索脈絡
- 快速查看相關檔案、最近 commits
- 若專案有 `spec/specs/`，瀏覽相關 living specs

### Step 2: 問 2-3 個關鍵問題
- 一次一題
- 理解目的、限制、成功標準

### Step 3: 提出設計
- 用幾句話描述設計方向
- 不寫正式文件

### Step 4: 用戶確認
- 確認後流程結束
- 告知用戶：可用 `/leespec-propose` 正式化為 spec proposal，或直接動手

---

## Full Mode（完整路徑）

### Phase 1: Discovery

**來源**：brainstorming 的探索 + 提問方法

1. **探索專案脈絡**
   - 查看檔案、文件、最近 commits
   - 若有 `spec/specs/`，瀏覽相關 living specs 和 `spec/changes/` 進行中的提案
   - 若有 `spec/designs/`，查看是否有相關設計文件

2. **判斷輸出類型**
   - Feature spec（程式功能相關設計）
   - Design doc（架構、效能、研究型設計）
   - 一般文件（提案、RFC、決策文件）

3. **Visual Companion（可選）**
   - 如果預期會有視覺相關的問題（UI mockup、架構圖、比較圖），提供一次性的同意詢問：
   > 接下來的設計可能會有一些用圖像或視覺比較更容易理解的內容。我可以透過瀏覽器展示 mockup 和圖表。要試試看嗎？（需要開啟本地 URL）
   - **此詢問必須是獨立訊息**，不與其他問題混合
   - 若用戶同意，閱讀 `~/.claude/skills/leespec/visual-companion.md` 的詳細指南
   - 後續每個問題各自判斷是否需要瀏覽器（視覺內容用瀏覽器，文字內容用終端機）

4. **逐一提問（One question at a time）**
   - 每次只問一個問題
   - 偏好 multiple choice 格式
   - 聚焦於：目的、限制條件、成功標準
   - 評估範圍：若需求涉及多個獨立子系統，先提議拆解再逐一設計

### Phase 2: Approach Selection

**來源**：brainstorming 的方案探索

1. **提出 2-3 個方案**
   - 每個方案附帶 trade-offs
   - 帶出推薦方案及推薦理由
   - 讓用戶選擇

### Phase 3: Section-by-Section Drafting

**來源**：doc-coauthoring 的逐段精煉

1. **決定文件結構**
   - 依輸出類型建議 3-5 個 section
   - 從不確定性最高的 section 開始
   - 用戶可調整結構

2. **每個 section 的迴圈**：
   1. **Clarifying questions** — 問 5-10 個關於此 section 該包含什麼的問題
   2. **Brainstorm** — 列出 5-20 個可能的內容點
   3. **Curate** — 用戶篩選（keep/remove/combine），例如 "Keep 1,4,7" "Remove 3"
   4. **Gap check** — 問是否有遺漏
   5. **Draft** — 撰寫此 section
   6. **Refine** — 根據用戶回饋迭代修改（用 `str_replace` 做局部修改，不重印全文）

3. **Visual Companion 可用於任何 section**
   - 判斷標準：用戶看到會比讀到更好理解嗎？

4. **品質自檢**
   - 連續 3 次迭代無實質改變時，問用戶是否有可刪除的多餘內容
   - 完成 80%+ sections 後，通讀全文檢查一致性、冗餘、空洞內容

### Phase 4: Quality Gate

**來源**：brainstorming 的 self-review + doc-coauthoring 的 reader testing

1. **Spec Self-Review**
   - 檢查 placeholder（TBD、TODO、未完成 section）
   - 檢查矛盾（section 之間是否衝突）
   - 檢查模糊（需求是否可被兩種方式解讀）
   - 檢查 scope（是否聚焦在單一可實作的範圍）
   - 發現問題直接修正

2. **Reader Testing（Sub-agent）**
   - 使用 sub-agent（無上下文）測試文件：
     - 預測 5-10 個讀者可能會問的問題
     - 用新的 Claude 實例（不帶本次對話上下文）回答這些問題
     - 檢查回答是否正確、是否有模糊或盲點
   - 引用 `~/.claude/skills/leespec/reviewer-prompt.md` 作為審查模板
   - 若發現問題，回到 Phase 3 修正相關 section

3. **寫入文件**
   - 將設計文件寫入 `spec/designs/YYYY-MM-DD-<topic>-design.md`
   - 若專案無 `spec/` 目錄，寫入 `docs/designs/YYYY-MM-DD-<topic>-design.md`

4. **用戶審閱**
   - 請用戶審閱最終文件
   - 若有修改要求，回到相關 section 修正

### Phase 5: Transition

1. **偵測專案結構**
   - 檢查是否有 `spec/specs/` 目錄

2. **有 openspec/leespec 結構**
   - 詢問：「要將設計正式化為 leespec proposal 嗎？（建立 proposal.md + tasks.md + spec-delta.md）」
   - Yes → 告知用戶使用 `/leespec-propose`
   - No → 建議使用 planning-with-files 或直接動手

3. **無 spec 結構**
   - 建議使用 planning-with-files 建立實作計畫，或直接動手

---

## Key Principles

- **One question at a time** — 不要一次問多個問題
- **Multiple choice preferred** — 能用選擇題就不用開放題
- **YAGNI ruthlessly** — 無情地移除不需要的功能
- **Explore alternatives** — 永遠提出 2-3 個方案
- **Incremental validation** — 逐步驗證，每段設計都獲得用戶認可
- **Design for isolation** — 拆解為小單元，各有明確用途和介面

## Anti-Pattern

「這太簡單了，不需要設計」 — 每個專案都要走流程。簡單的專案設計可以很短（幾句話），但必須提出並獲得批准。Quick mode 就是為此設計的。

## LeeSpec 套件導覽

| 指令 | 用途 |
|------|------|
| `/leespec-design` | 你在這裡 — 設計階段 |
| `/leespec-propose` | 正式化為 spec proposal（proposal + tasks + spec-delta） |
| `/leespec-implement` | 依序執行實作任務 |
| `/leespec-archive` | 歸檔已完成的變更 |
| `/leespec-context` | 查詢現有 specs / changes / archive |
