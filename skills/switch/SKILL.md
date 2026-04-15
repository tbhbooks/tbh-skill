---
name: switch
description: Switch which TBH book you're actively working on. Fetches latest book list from GitHub. Preserves progress for each book separately.
allowed-tools: Bash Read Write Glob WebFetch
argument-hint: (no arguments — shows current book and options)
---

# TBH Switch Book

You are helping a reader switch between TBH books.

## Steps

1. **Read `.tbh/config.json`** from the current working directory. If it doesn't exist, tell the reader to run `/tbh:setup` first.

2. **Show current state:**
   ```
   Active: <current book title> (Chapter <N>)
   ```

3. **Fetch the latest book registry from GitHub:**
   ```bash
   curl -sf https://raw.githubusercontent.com/tbhbooks/tbhbooks-skill/main/books/registry.json
   ```
   This ensures newly added books appear automatically. If fetch fails, use the registry cached in config.

4. **List all available books**, marking the active one.

5. **Ask which book to switch to.** Wait for their answer.

6. **Save current progress** — update `.tbh/progress.json` to preserve the current book's state.

7. **Update `.tbh/config.json`** with the new book's details (`raw_base`, `spec_path`, `chapters_path`, etc.). If the reader previously worked on this book, restore their last chapter position from progress.json.

8. **Update `last_registry_fetch`** timestamp in config.

9. **Confirm the switch:**
   ```
   ✓ Switched to: <new book title>
     Resuming at Chapter <N>
     Content source: <raw_base>
   ```

## Important
- Never lose progress. Each book's chapter state is tracked independently in `progress.json`.
- If the reader picks the already-active book, just confirm and show current status.
- New books added to the GitHub registry appear here automatically — no plugin update needed.
