---
name: leespec-propose
description: "LeeSpec 正式化提案（OpenSpec 對齊）— 建立結構化的 spec proposal，包含 proposal.md、design.md、tasks.md、specs/<domain>/spec.md（EARS 格式）。適用於將設計轉為正式規格提案。觸發詞：leespec propose、建立提案、create proposal、spec proposal。"
---

# LeeSpec Propose — 正式化 Spec 提案

將設計轉化為結構化的 spec proposal，產出四份 artifact（對齊 OpenSpec）：
1. **proposal.md** — Why / What Changes / Impact
2. **design.md** — 技術方案（非必要，視複雜度決定）
3. **tasks.md** — 可執行的實作任務清單
4. **specs/\<domain\>/spec.md** — EARS 格式的需求變更（ADDED/MODIFIED/REMOVED）

## Workflow

```
Proposal Progress:
- [ ] Step 1: Review existing specifications
- [ ] Step 2: Generate unique change ID
- [ ] Step 3: Scaffold directory structure
- [ ] Step 4: Draft proposal.md
- [ ] Step 4.5: Draft design.md (optional, for complex changes)
- [ ] Step 5: Create tasks.md
- [ ] Step 6: Write specs/<domain>/spec.md (EARS format delta)
- [ ] Step 7: Validate proposal structure
- [ ] Step 8: Present for user approval
```

### Step 1: Review existing specifications

了解現有狀態，避免衝突：

```bash
# 列出現有 specs
find openspec/specs -name "spec.md" -type f

# 列出進行中的 changes
find openspec/changes -maxdepth 1 -type d -not -path "openspec/changes" -not -path "openspec/changes/archive"

# 搜尋相關需求
grep -r "### Requirement:" openspec/specs/
```

若有進行中的 changes 含 `design.md`，一併讀取。

### Step 2: Generate unique change ID

選擇描述性、URL-safe 的 ID：

**格式**：`add-<feature>`、`fix-<issue>`、`update-<component>`、`remove-<feature>`

**驗證**：
```bash
ls openspec/changes/ | grep -i "<proposed-id>"
```

### Step 3: Scaffold directory structure

```bash
mkdir -p openspec/changes/{change-id}/specs/{capability-name}
```

### Step 4: Draft proposal.md

引用模板 `~/.claude/skills/leespec/templates/proposal.md`。

**必要 section**：
- **Why** — 驅動此變更的問題或機會
- **What Changes** — 修改項目列表
- **Impact** — 影響的 specs、程式碼、API、用戶、遷移需求、風險

語氣：清晰、精簡、聚焦決策。

### Step 4.5: Draft design.md（選用）

若變更涉及架構決策、技術方案選擇、或複雜的實作策略，建立 design.md。
引用模板 `~/.claude/skills/leespec/templates/design.md`。

**必要 section**：
- **Context** — 背景與限制
- **Approach** — 選擇的技術方案與替代方案比較
- **Technical Details** — 架構、資料模型、API 設計
- **Risks & Mitigations** — 風險與緩解措施

簡單變更（bug fix、config 調整）可跳過此步驟。

### Step 5: Create tasks.md

引用模板 `~/.claude/skills/leespec/templates/tasks.md`。

**規則**：
- 每個任務可獨立完成、可測試
- 含測試和驗證任務
- 依相依性排序（database → API → UI）
- 5-15 個任務為典型數量

### Step 6: Write specs/\<domain\>/spec.md (EARS format delta)

這是最關鍵的步驟。引用格式指南 `~/.claude/skills/leespec/EARS_FORMAT.md`，模板 `~/.claude/skills/leespec/templates/spec.md`。

**Delta 操作**：
- `## ADDED Requirements` — 新增功能
- `## MODIFIED Requirements` — 變更行為（含完整更新文字）
- `## REMOVED Requirements` — 廢棄功能

**需求結構**：
```markdown
### Requirement: 需求名稱
WHEN 觸發條件，
the system SHALL 行為和結果。

#### Scenario: 場景名稱
GIVEN 前置條件
AND 額外前置條件
WHEN 動作或觸發
THEN 預期結果
AND 額外結果
```

**觸發類型**：
- **WHEN**（事件驅動）：「When 用戶點擊儲存」
- **IF**（狀態驅動）：「If 購物車總額超過 $50」
- **WHERE**（功能特定）：「Where 用戶擁有管理員權限」
- **WHILE**（持續性）：「While 文件處於開啟狀態」

**關鍵原則**：
- 使用 **SHALL** 表示必要需求
- 每個需求 **必須有 scenario** 展示預期行為
- 包含 **正向和錯誤** scenario
- 避免實作細節 — 聚焦行為
- 確保需求可測試

### Step 7: Validate proposal structure

```
Structure Checklist:
- [ ] 目錄存在：openspec/changes/{change-id}/
- [ ] proposal.md 有 Why/What/Impact sections
- [ ] design.md 存在（若複雜變更）
- [ ] tasks.md 有階層式編號任務清單（X.Y 格式，5-15 項）
- [ ] specs/<domain>/spec.md 有操作標頭（ADDED/MODIFIED/REMOVED）
- [ ] 需求格式：### Requirement: <name>
- [ ] Scenario 格式：#### Scenario:（4 個 #）
```

**自動檢查**：
```bash
grep -c "## ADDED\|MODIFIED\|REMOVED" openspec/changes/{change-id}/specs/**/*.md
grep -n "#### Scenario:" openspec/changes/{change-id}/specs/**/*.md
grep -n "### Requirement:" openspec/changes/{change-id}/specs/**/*.md
```

### Step 8: Present for user approval

```markdown
## Proposal Summary

**Change ID**: {change-id}
**Scope**: {簡述}

**Files created**:
- openspec/changes/{change-id}/proposal.md
- openspec/changes/{change-id}/design.md（若適用）
- openspec/changes/{change-id}/tasks.md
- openspec/changes/{change-id}/specs/{domain}/spec.md

**Next step**: 審閱提案。批准後，使用 `/leespec-implement` 開始實作。
```

## Common Patterns

### 新功能提案
- 使用 `ADDED Requirements`
- 含正向 scenario 和錯誤處理 scenario

### 行為變更提案
- 使用 `MODIFIED Requirements`
- 含完整更新的需求文字
- 在 proposal.md 說明變更理由

### 廢棄功能提案
- 使用 `REMOVED Requirements`
- 在 proposal.md 說明廢棄原因
- tasks.md 含清理和遷移任務

## Anti-Patterns

- 不要跳過驗證步驟
- 不要建立提案前不看現有 specs
- 不要寫模糊的任務描述
- 不要需求沒有 scenario
- 不要忘記錯誤處理 scenario
- 不要在一個提案混合多個無關變更

## LeeSpec 套件導覽

| 指令 | 用途 |
|------|------|
| `/leespec-design` | 設計階段（從想法到設計文件） |
| `/leespec-propose` | 你在這裡 — 正式化為 spec proposal |
| `/leespec-implement` | 依序執行實作任務 |
| `/leespec-archive` | 歸檔已完成的變更 |
| `/leespec-context` | 查詢現有 specs / changes / archive |
