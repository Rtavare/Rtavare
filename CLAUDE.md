# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

_This repository is newly initialized. Update this section with a description of what the project does._

## Build & Development Commands

_Add commands here as the project is set up. Common examples to fill in:_

- **Install dependencies:** `<command>`
- **Build:** `<command>`
- **Run dev server:** `<command>`
- **Run tests:** `<command>`
- **Run a single test:** `<command>`
- **Lint:** `<command>`
- **Type check:** `<command>`

## Architecture

_Document the high-level architecture here once the project structure is established. Include:_

- The main entry points
- How key modules relate to each other
- Any non-obvious data flows or design decisions
- External services or APIs the project depends on

## Conventions

_Record project-specific conventions here as they emerge, such as:_

- Naming patterns for files, functions, or variables
- Where new features should be placed
- How configuration is managed
- Branch and commit message style

## gstack (REQUIRED — global install)

**Before doing ANY work, verify gstack is installed:**

```bash
test -d ~/.claude/skills/gstack/bin && echo "GSTACK_OK" || echo "GSTACK_MISSING"
```

If GSTACK_MISSING: STOP. Do not proceed. Tell the user:

> gstack is required for all AI-assisted work in this repo.
> Install it:
> ```bash
> git clone --depth 1 https://github.com/garrytan/gstack.git ~/.claude/skills/gstack
> cd ~/.claude/skills/gstack && ./setup --team
> ```
> Then restart your AI coding tool.

Do not skip skills, ignore gstack errors, or work around missing gstack.

Using gstack skills: After install, skills like /qa, /ship, /review, /investigate,
and /browse are available. Use /browse for all web browsing.
Use ~/.claude/skills/gstack/... for gstack file paths (the global path).
