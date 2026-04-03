---
name: leespec-archive
description: "LeeSpec 歸檔 — 歸檔已完成的變更，將 spec delta 合併至 living spec 文件。歸檔代表實作和部署已完成。觸發詞：leespec archive、歸檔、archive change、merge specs、finalize spec。"
---

# LeeSpec Archive — 歸檔完成的變更

歸檔已完成的 spec proposal，將 spec delta 合併至 living spec 文件。

**關鍵規則**：歸檔代表實作和部署已完成。所有任務必須完成才能歸檔。Archive 是不可變的歷史紀錄。

## Workflow

```
Archive Progress:
- [ ] Step 1: Verify implementation is complete
- [ ] Step 2: Review spec deltas to merge
- [ ] Step 3: Create timestamped archive directory
- [ ] Step 4: Merge ADDED requirements
- [ ] Step 5: Merge MODIFIED requirements
- [ ] Step 6: Merge REMOVED requirements
- [ ] Step 7: Move change folder to archive
- [ ] Step 8: Validate living spec structure
```

### Step 1: Verify implementation is complete

```bash
# 檢查 IMPLEMENTED 標記
test -f spec/changes/{change-id}/IMPLEMENTED && echo "✓ Implemented" || echo "✗ Not implemented"

# 審閱任務
cat spec/changes/{change-id}/tasks.md

# 檢查 git 狀態
git status
```

**詢問用戶**：所有任務是否完成並測試？是否已部署？是否繼續歸檔？

### Step 2: Review spec deltas to merge

```bash
# 列出所有 delta 檔案
find spec/changes/{change-id}/specs -name "*.md" -type f

# 讀取每個 delta
for file in spec/changes/{change-id}/specs/**/*.md; do
    echo "=== $file ==="
    cat "$file"
done
```

**確認**：
- 哪些 capability 受影響
- 多少需求是 ADDED/MODIFIED/REMOVED
- Living specs 中的對應位置

### Step 3: Create timestamped archive directory

```bash
TIMESTAMP=$(date +%Y-%m-%d)
mkdir -p spec/archive/${TIMESTAMP}-{change-id}
```

### Step 4: Merge ADDED requirements

對每個 `## ADDED Requirements` section：
1. 定位目標 living spec 檔案
2. 將新需求附加到檔案末尾
3. 維持正確的 markdown 格式

**完整複製需求和所有 scenario，不修改內容。**

### Step 5: Merge MODIFIED requirements

對每個 `## MODIFIED Requirements` section：
1. 在 living spec 中找到既有需求
2. **完整替換**整個需求 block（包含所有 scenario）
3. 使用 delta 中的完整更新文字

**是替換，不是合併。舊版本會保留在 archive 中。**

### Step 6: Merge REMOVED requirements

對每個 `## REMOVED Requirements` section：
1. 在 living spec 中找到該需求
2. 刪除整個需求 block
3. 加入 deprecation 註解：`<!-- Removed YYYY-MM-DD: 原因 -->`

### Step 7: Move change folder to archive

合併完所有 delta 後：

```bash
# 先 commit 合併的 specs
git add spec/specs/
git commit -m "Merge spec deltas from {change-id}"

# 再搬移到 archive
mv spec/changes/{change-id} spec/archive/${TIMESTAMP}-{change-id}

# 驗證搬移成功
ls -la spec/archive/${TIMESTAMP}-{change-id}
```

**整個資料夾一次搬移，不要挑選個別檔案。**

### Step 8: Validate living spec structure

```bash
# 檢查需求格式
grep -n "### Requirement:" spec/specs/**/*.md

# 檢查 scenario 格式
grep -n "#### Scenario:" spec/specs/**/*.md

# 計算每個 spec 的需求數
for spec in spec/specs/**/spec.md; do
    count=$(grep -c "### Requirement:" "$spec")
    echo "$spec: $count requirements"
done
```

**手動檢查**：
- 每個修改過的 spec 檔案格式正確
- 無重複需求
- 需求邏輯流暢

## Merge Logic Reference

| 操作 | 動作 | 位置 | 格式 |
|------|------|------|------|
| **ADDED** | 附加到 living spec | 檔案末尾 | 完整複製需求 + scenario |
| **MODIFIED** | 替換既有需求 | 按名稱找到，替換整個 block | 使用 delta 中的完整文字（不合併，替換） |
| **REMOVED** | 刪除需求 + 加註解 | 按名稱找到 | 刪除 block + `<!-- Removed YYYY-MM-DD: reason -->` |
| **RENAMED** | 更新名稱 | 按舊名找到 | 只改 `### Requirement: NewName` 標頭 |

## Git Commit Strategy

建議分兩個 commit：
```bash
# Commit 1: 合併 delta
git add spec/specs/
git commit -m "Merge spec deltas from {change-id}

- Added: {列出新增的需求}
- Modified: {列出修改的需求}
- Removed: {列出移除的需求}"

# Commit 2: 歸檔
git add spec/archive/ spec/changes/
git commit -m "Archive {change-id} change"
```

## Anti-Patterns

- 不要歸檔未完成的實作
- 不要在部署前合併 delta
- 不要修改已歸檔的檔案（archive 是不可變歷史）
- 不要跳過合併後的驗證
- 不要忘記先 commit 合併的 specs 再搬移

## LeeSpec 套件導覽

| 指令 | 用途 |
|------|------|
| `/leespec-design` | 設計階段 |
| `/leespec-propose` | 正式化為 spec proposal |
| `/leespec-implement` | 依序執行實作任務 |
| `/leespec-archive` | 你在這裡 — 歸檔已完成的變更 |
| `/leespec-context` | 查詢現有 specs / changes / archive |
