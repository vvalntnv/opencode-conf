---
name: knowledgebase-editor
description: Adding knowledge after exploring to the knowledgebase of the project
---
Here is a solid `.knowledgebase` skill you can drop into your agent/LLM system.

# Knowledgebase Skill

## Purpose

Each project may contain a `.knowledgebase/` folder used as a private project memory store.

This folder contains facts, decisions, discoveries, architecture notes, debugging lessons, API behavior, project conventions, and other useful knowledge learned while working on the project.

The LLM must use this folder as the project’s long-term memory.

Do not treat `.knowledgebase/` as source code. Treat it as personal project intelligence.

---

## When this skill is invoked

Before answering or making changes, first inspect `.knowledgebase/` for relevant existing knowledge.

Search it before making assumptions.

Prefer using existing knowledge over rediscovering the same thing again, because apparently humans enjoy paying models to relearn yesterday’s lesson.

Relevant files may include:

- `.knowledgebase/README.md`
- `.knowledgebase/architecture.md`
- `.knowledgebase/decisions.md`
- `.knowledgebase/debugging.md`
- `.knowledgebase/apis.md`
- `.knowledgebase/workflows.md`
- `.knowledgebase/conventions.md`
- `.knowledgebase/session-log.md`
- `.knowledgebase/open-questions.md`

If the folder does not exist, create it.

---

## Required behavior

### 1. Read before working

Before solving a task, check `.knowledgebase/` for relevant information.

Use search/grep/find tools when available.

Examples:

```bash
find .knowledgebase -type f
grep -Rni "auth\|token\|database\|deployment\|api" .knowledgebase
````

Do not read every file blindly if the folder is large. Search by topic first.

---

### 2. Add newly learned knowledge

Whenever the LLM discovers something useful, durable, and project-specific, it should add it to `.knowledgebase/`.

Only write knowledge if it is not already present.

Before adding a note:

1. Search for similar existing knowledge.
2. If the same idea already exists, do not duplicate it.
3. If the new information improves or corrects old knowledge, update the existing note.
4. If it is genuinely new, add it to the correct file.

---

## What counts as knowledge

Add knowledge when it is:

* A project-specific decision
* A discovered bug cause
* A fix that worked
* An architecture rule
* A naming convention
* A deployment lesson
* A database/schema insight
* A tool/provider/API behavior
* A recurring command or workflow
* A constraint that future agents must know
* A major change in project direction
* A non-obvious dependency between systems

Do **not** add:

* Random chat
* Temporary thoughts
* Obvious facts
* Failed guesses unless they prevent future wasted work
* Sensitive secrets, API keys, tokens, passwords, private credentials
* Large pasted logs unless summarized

---

## Knowledge format

Use short, structured notes.

Prefer this format:

```md
## YYYY-MM-DD - Short title

**Context:** What was being worked on.

**Discovery:** What was learned.

**Decision/Fix:** What should be done from now on.

**Evidence:** Optional command, error message, file path, or source.

**Status:** active | superseded | unresolved
```

Example:

```md
## 2026-04-28 - Anthropic thinking blocks and context editing

**Context:** Investigating how to trim old conversation history.

**Discovery:** Thinking blocks should not be blindly preserved forever. Older reasoning-heavy blocks can be removed or summarized when compacting, depending on provider rules.

**Decision/Fix:** Keep final user-visible outputs and tool-relevant results. Strip or summarize old thinking-heavy content unless required by provider constraints.

**Status:** active
```

---

## File organization

Use these files by default:

```txt
.knowledgebase/
  README.md
  architecture.md
  decisions.md
  debugging.md
  apis.md
  workflows.md
  conventions.md
  session-log.md
  open-questions.md
```

### `README.md`

Overview of what the knowledgebase contains.

### `architecture.md`

System structure, services, modules, responsibilities.

### `decisions.md`

Important project decisions and their reasons.

### `debugging.md`

Errors, causes, fixes, commands, troubleshooting lessons.

### `apis.md`

Provider behavior, external APIs, internal API contracts.

### `workflows.md`

Common commands, development flow, deployment flow.

### `conventions.md`

Naming, formatting, folder structure, coding style.

### `session-log.md`

Important session summaries only. Not every tiny action.

### `open-questions.md`

Things still unresolved or needing future investigation.

---

## End-of-session behavior

At the end of a work session, review what changed.

If something changed dramatically, write a short session note.

A dramatic change means:

* Architecture changed
* A major bug was solved
* A previous assumption was proven wrong
* A new tool/provider/library behavior was discovered
* Project direction changed
* A major decision was made
* A repeated workflow was established
* A blocker was found or removed

Use this format in `.knowledgebase/session-log.md`:

```md
## YYYY-MM-DD - Session summary

**Worked on:** Brief task summary.

**Major changes:** What changed significantly.

**New knowledge added:** Links or references to updated knowledgebase files.

**Open questions:** Anything unresolved.

**Next useful step:** The most logical continuation.
```

If nothing important changed, do not write a session note. No need to preserve “opened file, stared at problem, suffered spiritually.”

---

## Deduplication rules

Before adding knowledge, search first.

Use semantic judgment, not exact string matching only.

If an existing note says the same thing, do nothing.

If the new knowledge updates an old note, modify the old note and mark outdated parts as superseded.

Example:

```md
**Status:** superseded by 2026-04-28 note in `architecture.md`
```

Do not create five versions of the same lesson. That is how knowledgebases become digital swamps.

---

## Safety rules

Never store:

* Passwords
* API keys
* Private tokens
* SSH keys
* `.env` secrets
* Personal user data unless explicitly requested
* Credentials from logs
* Full proprietary source dumps

If sensitive information appears in a useful discovery, summarize it without the secret.

Bad:

```md
API key is sk-live-...
```

Good:

```md
The payment provider was failing because the live API key was missing from the production environment.
```

---

## Before writing files

When possible, make minimal edits.

Do not rewrite the entire knowledgebase unless explicitly asked.

Preserve existing notes.

Append or update the smallest relevant section.
