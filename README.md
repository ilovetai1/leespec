# LeeSpec — Specification-Driven Development for Claude Code

LeeSpec is a set of [Claude Code](https://claude.ai/code) custom skills that implement a structured, specification-driven development workflow. It guides you from initial idea through design, formal proposal, implementation, and archival — ensuring every change is well-specified, reviewed, and traceable.

## The Pipeline

```
  Design  →  Propose  →  Implement  →  Archive
    💡          📋           🔨           📦
```

| Stage | Slash Command | What It Does |
|-------|--------------|--------------|
| **Design** | `/leespec-design` | Collaborative brainstorming with Quick (lightweight) and Full (section-by-section) modes |
| **Propose** | `/leespec-propose` | Formalize design into a structured spec proposal with EARS-format requirements |
| **Implement** | `/leespec-implement` | Execute tasks sequentially with test-after-each validation |
| **Archive** | `/leespec-archive` | Merge spec deltas into living specs and archive the completed change |
| **Context** | `/leespec-context` | Explore existing specs, active changes, and archived history |

## How It Works

### 1. Design (`/leespec-design`)

Start with an idea. Choose **Quick mode** for small changes or **Full mode** for complex features. The design phase walks you through discovery, approach selection, section-by-section drafting, and quality review — all before any code is written.

### 2. Propose (`/leespec-propose`)

Turn your approved design into a formal spec proposal with three artifacts:
- **`proposal.md`** — Why this change matters, what changes, and impact assessment
- **`tasks.md`** — Ordered, testable implementation tasks (5–15 typical)
- **`spec-delta.md`** — EARS-format requirements (ADDED / MODIFIED / REMOVED) with scenarios

### 3. Implement (`/leespec-implement`)

Execute tasks one by one following a strict **read → execute → test → validate** loop. No task is marked complete until its tests pass.

### 4. Archive (`/leespec-archive`)

After implementation and deployment, merge spec deltas into living spec files and move the change folder to the archive. Archives are immutable history.

### 5. Context (`/leespec-context`)

Query the current state of your specifications at any time — list capabilities, search requirements, view active changes, or get a dashboard overview.

## Project Structure (in your target project)

LeeSpec expects and manages this directory layout:

```
spec/
├── specs/              # Living specifications (source of truth)
│   └── {capability}/
│       └── spec.md
├── changes/            # Active change proposals
│   └── {change-id}/
│       ├── proposal.md
│       ├── tasks.md
│       ├── IMPLEMENTED     # Marker file
│       └── specs/
│           └── {capability}/
│               └── spec-delta.md
├── archive/            # Completed changes (immutable)
│   └── {date}-{change-id}/
└── designs/            # Design documents
    └── YYYY-MM-DD-{topic}-design.md
```

## EARS Requirement Format

LeeSpec uses the [EARS (Easy Approach to Requirements Syntax)](https://alistairmavin.com/ears/) pattern:

```markdown
### Requirement: Descriptive Name
WHEN trigger condition,
the system SHALL expected behavior.

#### Scenario: Happy Path
GIVEN precondition
WHEN action
THEN expected result
```

Trigger keywords: **WHEN** (event), **IF** (state), **WHERE** (feature-specific), **WHILE** (ongoing)

## Installation

Copy the skill files into your Claude Code skills directory:

```bash
# Copy all skill files
cp leespec-*.md ~/.claude/skills/

# Or symlink for easier updates
ln -s $(pwd)/leespec-*.md ~/.claude/skills/
```

Then use any `/leespec-*` command in Claude Code to get started.

## Language

Skill prompts are written in **Traditional Chinese (繁體中文)** with English technical terms. Claude Code will interact with you in your preferred language regardless.

## License

MIT
