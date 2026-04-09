---
name: leespec-implement
description: "LeeSpec 實作執行（OpenSpec 對齊）— 依序執行已批准 proposal 的實作任務，每個任務完成後立即測試驗證。觸發詞：leespec implement、開始實作、apply change、execute spec、work through tasks。"
---

# LeeSpec Implement — 實作執行

依序執行已批准 spec proposal 的實作任務，遵循 read → execute → test → validate 循環。

**關鍵規則**：每個任務完成後立即測試。不跳過、不批次、不標記未完成的任務為已完成。

## Workflow

```
Implementation Progress:
- [ ] Step 1: Load and understand the proposal
- [ ] Step 2: Set up task tracking
- [ ] Step 3: Execute tasks sequentially
- [ ] Step 4: Test and validate each task
- [ ] Step 5: Document discoveries
- [ ] Step 6: Mark proposal as implementation-complete
```

### Step 1: Load and understand the proposal

讀取所有相關文件：

```bash
cat openspec/changes/{change-id}/proposal.md
cat openspec/changes/{change-id}/tasks.md
find openspec/changes/{change-id}/specs -name "*.md" -exec cat {} \;
```

**理解**：

- 為何需要此變更（from proposal.md）
- 預期結果是什麼
- 哪些 specs 會受影響
- 驗收標準是什麼（from scenarios）

### Step 2: Set up task tracking

從 tasks.md 載入所有任務，建立追蹤：

```
讀取 tasks.md → 提取編號清單 → 建立任務追蹤
第一個任務設為 in_progress，其餘為 pending
```

### Step 3: Execute tasks sequentially

**逐個執行，依序進行**：

```
For each task:
1. 標記為 in_progress
2. 執行工作
3. 測試工作
4. 驗證通過後才標記 completed
5. 進入下一個任務

絕對不要跳過或批次處理多個任務。
```

**任務執行格式**：

```markdown
## Task: {任務描述}

**What**: 此任務做什麼
**Implementation**: 程式碼變更、檔案編輯
**Verification**:
- [ ] 程式碼可編譯/執行
- [ ] 測試通過
- [ ] 符合需求 scenario
**Status**: ✓ Complete / ✗ Blocked / ⚠ Partial
```

### Step 4: Test and validate each task

每個任務完成後立即驗證：

**程式碼任務**：
```bash
# 執行相關測試
pytest tests/ -v --tb=short  # 或 npm test, cargo test 等

# Lint 檢查
ruff check src/  # 或 npm run lint 等

# Type check（如適用）
npm run type-check
```

**資料庫任務**：驗證 migration 執行成功、schema 正確

**API 任務**：用 curl 或 requests 測試 endpoint

**只有所有驗證通過後才標記任務完成。**

### Step 5: Document discoveries

實作過程中若發現 spec delta 需要調整：

1. **記錄發現** — 在 proposal.md 或筆記中記下
2. **不修改 spec delta** — 實作期間不動 delta 檔案
3. **實作完成後再考慮** — spec 調整在歸檔時處理

### Step 6: Mark proposal as implementation-complete

所有任務完成後：

```bash
echo "Implementation completed: $(date)" > openspec/changes/{change-id}/IMPLEMENTED
```

告知用戶：

```markdown
## Implementation Complete

**Change**: {change-id}
**Tasks completed**: {count}
**Tests**: All passing

**Next step**: 使用 `/leespec-archive` 歸檔此變更，將 spec delta 合併至 living specs。
```

## Best Practices

### Blocked Tasks
- 保持 in_progress（不標記 completed）
- 清楚記錄阻礙原因
- 建立新任務解決阻礙
- 立即通知用戶

### Task Dependencies
- 執行前驗證前置條件
- 依序處理：database → models → services → API → UI

### Incremental Testing
**正確**：Task 1 → Test → Complete → Task 2 → Test → Complete
**錯誤**：Task 1, 2, 3 → 全部實作 → 最後才測試 → Debug

### Common Task Patterns

**Database + API + UI**：
1. Database schema/migration
2. Data access layer (models)
3. Business logic (services)
4. API endpoints
5. UI integration
6. End-to-end tests

**Feature Flags**：
1. 在 flag 後面實作功能
2. 啟用 flag 測試
3. 部署時停用 flag
4. 逐步啟用

## Anti-Patterns

- 不要跳過個別任務的測試
- 不要在驗證前標記完成
- 不要忽視失敗的測試
- 不要在實作期間修改 living specs
- 不要亂序執行（會破壞相依性）

## LeeSpec 套件導覽

| 指令 | 用途 |
|------|------|
| `/leespec-design` | 設計階段 |
| `/leespec-propose` | 正式化為 spec proposal |
| `/leespec-implement` | 你在這裡 — 依序執行實作任務 |
| `/leespec-archive` | 歸檔已完成的變更 |
| `/leespec-context` | 查詢現有 specs / changes / archive |
