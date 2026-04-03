---
name: leespec-context
description: "LeeSpec 脈絡查詢 — 探索現有 specs、active changes、archive、designs。觸發詞：leespec context、what specs exist、show changes、list capabilities、project context、find specs。"
---

# LeeSpec Context — 脈絡查詢

探索專案的 specifications、active changes、archived changes 和 design documents。

**基本模式**：Search → Read → Summarize

## Discovery Commands

### 列出所有 Specifications

```bash
# 列出所有 spec 檔案
find spec/specs -name "spec.md" -type f

# 列出所有 capability 目錄
find spec/specs -mindepth 1 -maxdepth 1 -type d

# 每個 capability 的需求數
for cap in spec/specs/*/; do
    name=$(basename "$cap")
    count=$(grep -c "### Requirement:" "$cap/spec.md" 2>/dev/null || echo "0")
    echo "$name: $count requirements"
done
```

### 列出 Active Changes

```bash
# 列出進行中的 changes
find spec/changes -maxdepth 1 -type d -not -path "spec/changes" | sort

# 帶 proposal 摘要
for change in spec/changes/*/; do
    id=$(basename "$change")
    echo "=== $id ==="
    test -f "$change/IMPLEMENTED" && echo "  Status: Implemented" || echo "  Status: In Progress"
    head -n 20 "$change/proposal.md" 2>/dev/null | grep -A 3 "## Why"
done
```

### 列出 Archived Changes

```bash
ls -1 spec/archive/

# 最近歸檔（7 天內）
find spec/archive/ -maxdepth 1 -type d -mtime -7
```

### 列出 Design Documents

```bash
# 列出設計文件
ls -la spec/designs/ 2>/dev/null || echo "No designs directory"
```

### 搜尋需求（Keyword）

```bash
# 按關鍵字搜尋
grep -r -i "keyword" spec/specs/

# 搜尋特定需求
grep -r "### Requirement:" spec/specs/ | grep -i "keyword"

# 搜尋 scenario
grep -B 1 -A 10 -i "keyword" spec/specs/**/*.md | grep -A 10 "#### Scenario:"
```

## Common Queries

### "What specs exist?"

列出所有 capabilities 和需求數量，以表格呈現：

```markdown
## Existing Specifications

| Capability | Requirements |
|-----------|-------------|
| authentication | 8 |
| billing | 12 |
| ... | ... |

Total: X capabilities, Y requirements
```

### "What changes are active?"

列出進行中的 changes，含 Why 摘要和狀態：

```markdown
## Active Changes

### {change-id}
**Status**: In Progress / Implemented
**Why**: {摘要}
```

### "Show me the X spec"

讀取完整 spec 並摘要：需求清單、scenario 數量、最後修改時間。

### "Find specs about X"

搜尋關鍵字，回報哪些 specs 提及、相關的需求名稱。

### "What's in change X?"

顯示完整 change 內容：proposal + tasks + spec deltas。

## Dashboard View

綜合摘要：

```markdown
# Specification Dashboard

## Capabilities
Total: X capabilities
- capability-a: Y requirements
- capability-b: Z requirements

## Requirements
Total requirements: X
Total scenarios: Y

## Changes
Active: X
Archived: Y

## Designs
Total: X design documents

## Recent Activity
- ...
```

## Best Practices

- **先給總覽，再給細節** — 不要一開始就傾倒整個檔案
- **用 grep 高效搜尋** — 組合過濾條件精確查找
- **彙整資訊** — 摘要而非原始輸出
- **詢問用戶要深入哪個區域** — 不要假設用戶知道 capability 名稱

## LeeSpec 套件導覽

| 指令 | 用途 |
|------|------|
| `/leespec-design` | 設計階段 |
| `/leespec-propose` | 正式化為 spec proposal |
| `/leespec-implement` | 依序執行實作任務 |
| `/leespec-archive` | 歸檔已完成的變更 |
| `/leespec-context` | 你在這裡 — 查詢現有 specs / changes / archive |
