# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository type

Obsidian knowledge vault — not a code project. There is no build, lint, or test command; all content is Korean-language Markdown (`.md`) and Obsidian canvas (`.canvas`) files at the repo root, tracked in Git.

The vault collects notes on web development, web security, and "Vibe Coding" (AI-assisted development with Claude Code, AI Studio, Firebase, Antigravity). Notes are written for the author's own reference and cross-linked into hub notes that live outside this folder.

## File and filename conventions

- File names are the note's Korean title, with spaces and punctuation preserved (e.g. `crossorigin 속성과 CORS 메커니즘의 이해.md`, `Git Revert는 왜 충돌을 일으킬까.md`). Do not slugify, kebab-case, or rename on save.
- Filename **is** the canonical title — Obsidian uses it for `[[wikilinks]]`. Renaming a file breaks inbound links from other vault folders.
- Paths contain spaces and Korean characters; always quote them in shell commands.

## Frontmatter schema

Every note starts with YAML frontmatter. Keep the keys below (even when empty) so Obsidian Dataview queries continue to work:

```yaml
---
date: YYYY-MM-DD            # absolute date, not relative
tags:                       # mix of English and Korean, kebab/underscore as-is
  - CORS
  - 웹보안
type: knowledge             # nearly always "knowledge"
contexts:
  - "[[🏷️Vibe Coding]]"    # hub note — emoji is part of the link target
index:
  - "[[🎶Web Development]]"
  - "[[🔆인공지능]]"
YouTube:
scr_url:
scr:
---
```

Notes:
- `author`, `people`, `Lilys AI`, `with AI`, and `byAI` keys were removed from all notes on 2026-04-20. Do not reintroduce them — AI-attribution metadata is no longer tracked in frontmatter.
- Some newer notes (e.g. `crossorigin 속성과 CORS 메커니즘의 이해.md`) use a slimmer frontmatter with `category:` and `summary:` instead of the full schema. Follow the shape of the most recent neighbor rather than forcing one style.
- The `[[🏷️...]]`, `[[🎶...]]`, `[[🔆...]]`, `[[✨...]]` emoji-prefixed links resolve to hub notes elsewhere in the Obsidian vault, not to files in this folder — leaving them as-is is correct even though they look like broken links from inside this directory.

## Content conventions

- Notes are Korean prose aimed at a self-learner; keep tone explanatory, not bullet-listy.
- Structural patterns seen across the vault: numbered `## STEP N.` or `## N.` sections, comparison tables, and Obsidian callouts (`> [!info]`, `> [!quote]`, `> [!warning]`). Reuse these rather than introducing new Markdown extensions.
- Inline `[[wikilinks]]` to other notes in the vault are expected; do not convert them to relative Markdown links.
- Code fences use standard language tags (```js, ```ts, ```bash, etc.).

## Git workflow

- Commit messages in the log are terse Korean (e.g. `노트 생성`, `docs: 바이부 노트 추가`). Match that style when committing on the user's behalf.
- The repo has no hooks, CI, or lint pipeline — nothing will catch frontmatter typos, so double-check YAML before committing.
