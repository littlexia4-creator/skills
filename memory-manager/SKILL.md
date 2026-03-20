---
name: memory-manager
description: Automatically detect when the user wants to remember, record, or store information, then append it to the memory/ directory organized by topic. Use when the user says things like "remember this", "note this down", "save this", "store this", "don't forget", or any statement that implies they want to retain information for later. Also trigger when they share facts, URLs, credentials, or knowledge they might need to reference again.
---

# Memory Manager

Automatically saves information to the memory directory organized by topic.

## Detection Patterns

Trigger this skill when the user:
- Explicitly says: "remember this", "note this down", "save this", "don't forget", "store this"
- Shares facts they might need later (birthdays, important dates, preferences)
- Provides URLs, tools, or resources they want to keep
- Mentions credentials, API keys, or sensitive info
- Describes events, meetings, or decisions worth logging
- Says anything implying "keep this for future reference"

## File Organization

**Always organize by topic in `memory/` directory. Never create daily date-based files like `2026-03-20.md`.**

Common topical files:
- `birthdays.md` — People's birthdays and ages
- `contacts.md` — People, their details, how you know them
- `credentials.md` — Passwords, API keys, tokens (sensitive)
- `knowledge.md` — General facts, tips, learned information
- `meetings.md` — Meeting notes, decisions, outcomes
- `preferences.md` — Personal preferences, settings
- `projects.md` — Project details, status, decisions
- `tools.md` — Useful tools, websites, resources
- `websites.md` — Bookmarks, references by category

**If no existing file fits, create a new topical file** with a descriptive name (e.g., `travel-plans.md`, `book-recommendations.md`).

## Workflow

1. **Detect intent** — User wants to remember something
2. **Determine topic** — Identify the topic/category of the information
3. **Search existing files** — **MANDATORY before creating any new file.** Use Grep to search all `memory/*.md` files for keywords related to the new information. Check both filenames and file contents. For example, if the user says "remember this Clash rule", search for "clash", "DOMAIN", "proxy", etc. across all existing files.
4. **Decide: merge or create**
   - If a relevant file exists (by name or content), **append to that file** — do NOT create a new one.
   - Only create a new topical file if no existing file covers the topic at all.
5. **Format entry** — Add date, context, organize clearly
6. **Append or create** — Add to existing file, or create new topical file as last resort
7. **Confirm** — Briefly tell user what was saved and which file it went to

## Entry Format

**REQUIRED: Every entry MUST end with a date in this exact format:**
```
<!-- Date: YYYY-MM-DD -->
```

This HTML comment format keeps the date visible in source but hidden when rendered.

### Detailed Entry Format
```markdown
- **Item**: Description
  - Source: Where this came from (if known)
  - Notes: Additional context
  
<!-- Date: 2026-03-20 -->
```

### Simple Entry Format
```markdown
- Description

<!-- Date: 2026-03-20 -->
```

### Table Row Format (for comparison tables)
```markdown
| Item | Value |
|------|-------|
| Name | Value |

<!-- Date: 2026-03-20 -->
```

**IMPORTANT:** The `<!-- Date: YYYY-MM-DD -->` line must be the last line of each entry or entry group.

## Examples

**Birthday:**
- User: "My sister's birthday is March 15, 1990"
- Action: Append to `memory/birthdays.md` with age calculation

**Tool/Website:**
- User: "This site is useful: https://example.com"
- Action: Append to `memory/tools.md` or `memory/websites.md` with description

**Credential:**
- User: "API key is sk-abc123"
- Action: Append to `memory/credentials.md` with service name and date

**Knowledge/Tip:**
- User: "Use pyenv to manage multiple versions of Python."
- Action: Search `memory/*.md` for "pyenv", "python", "version manager". If `memory/development.md` or similar exists, append there. Otherwise create `memory/python-setup.md`.

## Critical Rules

### NO date-based filenames
- **NEVER** name a file with a date like `2026-03-20.md`, `march-2026.md`, or `daily-log.md`.
- Filenames must describe the **topic**, not the date. Examples: `clash-rules.md`, `birthdays.md`, `docker.md`.

### ALWAYS search before creating
- **Before creating any new file**, you MUST search existing `memory/*.md` files for related content.
- Search by keywords from the new information (e.g., "clash", "proxy", "docker", "birthday").
- If ANY existing file covers the same topic, **merge the new entry into that file**.
- Only create a new file when zero existing files match the topic.

### Example: Search-then-merge
User says: "Remember this Clash config — `DOMAIN,alidocs.dingtalk.com,DIRECT`"
1. Search: `grep -r "clash\|Clash\|DOMAIN.*DIRECT\|proxy rule" memory/`
2. Found: `memory/clash-rules.md` exists → **append** the new rule there.
3. Do NOT create `clash-config-2026-03-31.md` or any new file.

## Other Notes

- Always use today's date for new entries
- If unsure which file to use, prefer `knowledge.md` or create a new topical file with a descriptive name
- Keep entries concise but include enough context to be useful later
- When appending, check for duplicates — if the exact same information exists, update rather than duplicate
- For credentials: include what service it's for, when it was added, and any expiration
- **Every entry MUST end with `<!-- Date: YYYY-MM-DD -->`**
