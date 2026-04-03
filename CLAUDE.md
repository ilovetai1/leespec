# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What is LeeSpec?

LeeSpec is a set of Claude Code custom skills (slash commands) that implement a **specification-driven development workflow**. It guides users through a structured pipeline: Design → Propose → Implement → Archive, with a Context query tool for exploring existing specs.

## Skill Files

Each `.md` file in the root is a Claude Code skill definition with YAML frontmatter (`name`, `description`) and a full prompt body:

| File | Skill | Purpose |
|------|-------|---------|
| `leespec-design.md` | `/leespec-design` | Brainstorming + design collaboration (Quick/Full mode) |
| `leespec-propose.md` | `/leespec-propose` | Formalize design into structured proposal (proposal.md + tasks.md + spec-delta.md in EARS format) |
| `leespec-implement.md` | `/leespec-implement` | Execute approved tasks sequentially with test-after-each |
| `leespec-archive.md` | `/leespec-archive` | Merge spec deltas into living specs, move to archive |
| `leespec-context.md` | `/leespec-context` | Query existing specs, active changes, archives, designs |

## Key Concepts

- **Living Specs**: `spec/specs/{capability}/spec.md` — the source of truth for current system behavior
- **Spec Delta**: EARS-format requirement changes (ADDED/MODIFIED/REMOVED) with scenarios
- **Change Lifecycle**: `spec/changes/{id}/` → implemented → `spec/archive/{date}-{id}/`
- **EARS Format**: Requirements use SHALL + trigger keywords (WHEN/IF/WHERE/WHILE) with GIVEN/WHEN/THEN scenarios

## Editing Guidelines

- Skills are written primarily in Traditional Chinese (繁體中文) with English technical terms
- Each skill contains: workflow steps, best practices, anti-patterns, and a navigation table linking all 5 skills
- The HARD-GATE in `leespec-design.md` is critical — no code may be written before Phase 4 approval
- Frontmatter `description` field is used for skill matching/triggering — keep it accurate with trigger words
