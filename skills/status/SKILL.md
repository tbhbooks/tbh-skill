---
name: status
description: Show your current TBH book, chapter progress, and what to do next.
allowed-tools: Read Glob Bash WebFetch
argument-hint: (no arguments)
---

# TBH Status

Show the reader their current build-along status.

## Fetch Preamble

Before displaying status, ensure chapter titles are available:

1. **Read `.tbh/config.json`**. If missing, tell reader to run `/tbh:setup` first.
2. **Check `.tbh/cache/chapter_titles.json`** — if it exists and is less than 24 hours old, use it.
3. **If stale or missing**, fetch chapter titles by downloading the first 5 lines of each chapter file:
   ```bash
   for i in $(seq -w 0 <total_chapters>); do
     curl -sf "<raw_base>/<chapters_path>/ch${i}.md" | head -3
   done
   ```
   Extract the `# Chapter N: Title` line from each. Cache results to `.tbh/cache/chapter_titles.json`:
   ```json
   {"fetched_at": "<ISO>", "titles": {"0": "...", "1": "...", "2": "..."}}
   ```

## Steps

1. **Read `.tbh/progress.json`** for completion data.

2. **Display a progress overview:**

```
📖 <book title>
🔧 Language: <language>
📍 Current chapter: <N>

Progress:
  Ch 1  ✓ The One-Shot Trap and the Loop That Fixes It
  Ch 2  ✓ Your First Real Agent
  Ch 3  ◉ Tool Use + MCP  ← you are here
  Ch 4  ○ Skills: Teaching Your Agent What To Do
  Ch 5  ○ File System + Shell
  ...
  Ch 15 ○ The Agent Ecosystem

Run /tbh:build-chapter to continue Chapter <N>
Run /tbh:validate to check your work
```

3. Use these markers:
   - `✓` completed (in `chapters_completed`)
   - `◉` current chapter
   - `○` not started
