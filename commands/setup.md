---
name: setup
description: Initialize TBH build-along environment. Pick a book, set your language, fetch specs from GitHub. Run this first before any other /tbh command.
allowed-tools: Bash Read Write Grep Glob WebFetch
argument-hint: (no arguments — interactive setup)
---

# TBH Setup

You are helping a reader set up their TBH (The Builder's Handbook) build-along environment.

## Steps

1. **Fetch the book registry from GitHub:**
   ```bash
   curl -sf https://raw.githubusercontent.com/tbhbooks/tbh-skill/main/books/registry.json
   ```
   If fetch fails, fall back to the local `books/registry.json` bundled with this plugin.
   Parse the JSON to get the list of available books.

2. **List available books** for the reader. Show title, description, and status for each.

3. **Ask which book** they want to work on. Wait for their answer.

4. **Ask their preferred language** (default from the book's `default_language`). Let them choose anything (python, typescript, go, rust, etc.).

5. **Create `.tbh/` directory** in the current working directory, then create `.tbh/config.json`:
```json
{
  "active_book": "<book-id>",
  "book_title": "<title>",
  "raw_base": "<raw_base from registry>",
  "spec_path": "<spec_path from registry>",
  "chapters_path": "<chapters_path from registry>",
  "language": "<chosen-language>",
  "chapters_completed": [],
  "current_chapter": 1,
  "registry_url": "https://raw.githubusercontent.com/tbhbooks/tbh-skill/main/books/registry.json",
  "last_registry_fetch": "<ISO timestamp>"
}
```

6. **Create `.tbh/progress.json`**:
```json
{
  "book": "<book-id>",
  "started_at": "<ISO timestamp>",
  "chapters": {}
}
```

7. **Create `.tbh/cache/` directory** for cached chapter content.

8. **Test the connection** — fetch one file to verify the raw_base works:
   ```bash
   curl -sf "<raw_base>/<chapters_path>/ch01.md" | head -5
   ```
   If it fails, warn the reader but don't block setup. Content will be fetched when needed.

9. **Print a welcome message:**
```
✓ TBH environment initialized
  Book: <title>
  Language: <language>
  Current chapter: 1
  Content: fetched from GitHub (cached locally)

  Run /tbh:build-chapter to start Chapter 1
  Run /tbh:status to see your progress
```

## Important
- If `.tbh/config.json` already exists, warn the reader and ask if they want to reconfigure.
- Add `.tbh/` to the project's `.gitignore` if it exists and `.tbh` isn't already listed.
- No local clone of the book repo is needed. All content is fetched from GitHub on demand.
