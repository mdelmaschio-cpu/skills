# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A collection of agent skills (slash commands) for Claude Code and other coding agents. Skills are markdown-based instruction sets, optionally bundled with scripts and reference docs.

## Skill Buckets

Skills live under `skills/` organized into buckets:

| Bucket | Purpose | Appears in README + plugin.json? |
|---|---|---|
| `engineering/` | Daily code work | Yes |
| `productivity/` | Non-code workflow tools | Yes |
| `misc/` | Kept around but rarely used | Yes |
| `personal/` | Tied to owner's personal setup | No |
| `deprecated/` | No longer used | No |

## Structural Invariants

These must be maintained whenever a skill is added, moved, or removed:

1. Every skill in `engineering/`, `productivity/`, or `misc/` must have:
   - An entry in the top-level `README.md` (skill name linked to its `SKILL.md`)
   - An entry in `.claude-plugin/plugin.json`
2. Each bucket folder's `README.md` must list every skill in that bucket with a one-line description, skill name linked to its `SKILL.md`.
3. Skills in `personal/` and `deprecated/` must **not** appear in either the top-level `README.md` or `plugin.json`.

## Skill File Structure

```
skill-name/
├── SKILL.md           # Required — main instructions, must stay under ~100 lines
├── REFERENCE.md       # Optional — detailed docs split out when SKILL.md exceeds 100 lines
└── scripts/           # Optional — deterministic helper scripts
```

`SKILL.md` must have a YAML frontmatter block:

```md
---
name: skill-name
description: One or two sentences. First: what it does. Second: "Use when [specific triggers]." Max 1024 chars.
---
```

The description is the only thing an agent sees when deciding which skill to load — make it specific enough to distinguish from other skills.

## Scripts

```bash
# Link all skills into ~/.claude/skills for local Claude CLI use
./scripts/link-skills.sh

# List all SKILL.md paths in the repo
./scripts/list-skills.sh
```

## Installing Skills (consumer-facing)

```bash
npx skills@latest add mattpocock/skills
```

## Domain Language (`CONTEXT.md`)

`CONTEXT.md` defines the canonical vocabulary for this repo. Key terms:

- **Issue tracker** — the tool hosting issues (GitHub Issues, Linear, local `.scratch/`). Not "backlog manager" or "backlog backend".
- **Issue** — a single tracked unit of work. Not "ticket" unless quoting an external system.
- **Triage role** — a state-machine label on an Issue (e.g. `needs-triage`, `ready-for-afk`).

## Skill Dependency Pattern

Engineering skills divide into two dependency tiers on `/setup-matt-pocock-skills`:

- **Hard dependency** (`to-issues`, `to-prd`, `triage`) — include an explicit prompt: _"run `/setup-matt-pocock-skills` if not already done."_ Without per-repo config these produce wrong output.
- **Soft dependency** (`diagnose`, `tdd`, `improve-codebase-architecture`, `zoom-out`) — reference "the project's domain glossary" and "ADRs in the area you're touching" in vague prose only. They degrade gracefully without the config.

See `docs/adr/0001-explicit-setup-pointer-only-for-hard-dependencies.md` for rationale.

## ADRs

Architecture decisions live in `docs/adr/`. File new ADRs there when making structural choices about skill design conventions.
