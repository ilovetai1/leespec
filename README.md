# LeeSpec — 規格驅動開發，為 Claude Code 打造

> [English](README.en.md)

LeeSpec 是一組 [Claude Code](https://claude.ai/code) 自訂技能（slash commands），實現結構化的**規格驅動開發流程**。從最初的想法到設計、正式提案、實作、歸檔，確保每個變更都有完整的規格、審查和追溯紀錄。

## 致謝

LeeSpec 站在巨人的肩膀上，結合了以下兩個優秀的開源 Claude Code skills：

- **[OpenSpec](https://github.com/Fission-AI/OpenSpec)** — Spec-driven development（規格驅動開發）框架，提供了 living specs、spec delta、EARS 格式需求等核心概念
- **[Superpowers](https://github.com/obra/superpowers)** — Agentic skills 框架與軟體開發方法論，提供了腦力激盪、逐段協作、品質把關等設計流程的靈感

感謝這兩個專案的作者與社群，讓我們能在此基礎上打造 LeeSpec。

## 流程總覽

```
  設計  →  提案  →  實作  →  歸檔
   💡       📋       🔨       📦
```

| 階段 | 指令 | 說明 |
|------|------|------|
| **設計** | `/leespec-design` | 協作式腦力激盪，支援 Quick（精簡）和 Full（完整逐段）模式 |
| **提案** | `/leespec-propose` | 將設計正式化為結構化 spec proposal，含 EARS 格式需求 |
| **實作** | `/leespec-implement` | 依序執行任務，每個任務完成後立即測試驗證 |
| **歸檔** | `/leespec-archive` | 將 spec delta 合併至 living specs，歸檔已完成的變更 |
| **查詢** | `/leespec-context` | 探索現有 specs、進行中的 changes 和歸檔歷史 |

## 運作方式

### 1. 設計 (`/leespec-design`)

從一個想法開始。選擇 **Quick mode** 處理小改動，或 **Full mode** 處理複雜功能。設計階段會引導你完成探索、方案選擇、逐段撰寫和品質審查 — 全部在寫任何程式碼之前。

### 2. 提案 (`/leespec-propose`)

將通過審查的設計轉化為正式 spec proposal，產出三份文件：
- **`proposal.md`** — 為何需要此變更、變更內容、影響評估
- **`tasks.md`** — 按順序排列的可測試實作任務（通常 5–15 個）
- **`spec-delta.md`** — EARS 格式的需求變更（ADDED / MODIFIED / REMOVED）附帶 scenario

### 3. 實作 (`/leespec-implement`)

嚴格遵循 **讀取 → 執行 → 測試 → 驗證** 循環，逐一執行任務。測試未通過前，任務不會標記為完成。

### 4. 歸檔 (`/leespec-archive`)

實作和部署完成後，將 spec delta 合併至 living spec 檔案，並將 change 資料夾移至 archive。Archive 是不可變的歷史紀錄。

### 5. 脈絡查詢 (`/leespec-context`)

隨時查詢規格的現狀 — 列出 capabilities、搜尋需求、檢視進行中的 changes，或取得總覽儀表板。

## 專案目錄結構（在你的目標專案中）

LeeSpec 預期並管理以下目錄佈局：

```
spec/
├── specs/              # Living specifications（真實來源）
│   └── {capability}/
│       └── spec.md
├── changes/            # 進行中的 change proposals
│   └── {change-id}/
│       ├── proposal.md
│       ├── tasks.md
│       ├── IMPLEMENTED     # 完成標記檔
│       └── specs/
│           └── {capability}/
│               └── spec-delta.md
├── archive/            # 已完成的 changes（不可變）
│   └── {date}-{change-id}/
└── designs/            # 設計文件
    └── YYYY-MM-DD-{topic}-design.md
```

## EARS 需求格式

LeeSpec 使用 [EARS (Easy Approach to Requirements Syntax)](https://alistairmavin.com/ears/) 模式撰寫需求：

```markdown
### Requirement: 需求名稱
WHEN 觸發條件，
the system SHALL 預期行為。

#### Scenario: 正常路徑
GIVEN 前置條件
WHEN 動作
THEN 預期結果
```

觸發關鍵字：**WHEN**（事件驅動）、**IF**（狀態驅動）、**WHERE**（功能特定）、**WHILE**（持續性）

## 安裝

將 skill 檔案複製到 Claude Code 的 skills 目錄：

```bash
# 複製所有 skill 檔案
cp leespec-*.md ~/.claude/skills/

# 或使用 symlink 方便更新
ln -s $(pwd)/leespec-*.md ~/.claude/skills/
```

然後在 Claude Code 中使用任何 `/leespec-*` 指令即可開始。

## 語言

Skill prompts 以**繁體中文**撰寫，技術術語保留英文。Claude Code 會依據你的偏好語言與你互動。

## 授權

MIT
