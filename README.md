# The Builder's Handbook (TBH): Build-Along Companion for Claude

A Claude Code plugin that guides you through building real systems alongside [The Builder's Handbook](https://github.com/tbhbooks) book series.

tbh, you should build it yourself — and this plugin makes sure you can.

## Install

```bash
# In Claude Code: Execute following two commands
/plugin marketplace add tbhbooks/tbh-skill 
/plugin install tbh@the-builders-handbook
```

Or load locally for development:
```bash
claude --plugin-dir ./tbh/tbhbooks-skill
```

## Quick Start

```
/tbh:setup            # Pick a book, set your language
/tbh:build-chapter    # Start building the current chapter
/tbh:validate         # Run tests against your implementation
/tbh:hint             # Get a nudge when you're stuck
/tbh:next             # See what's next
```

## All Commands

| Command | What it does |
|---------|-------------|
| `/tbh:setup` | Pick a book, set language, initialize environment |
| `/tbh:switch` | Switch between books (preserves progress) |
| `/tbh:status` | Show active book + chapter progress |
| `/tbh:build-chapter [N]` | Start/continue a chapter's build |
| `/tbh:validate [N]` | Run chapter validation tests |
| `/tbh:hint [topic]` | Targeted hint without spoilers |
| `/tbh:review [N]` | Review implementation against spec |
| `/tbh:explain <concept>` | Explain a concept grounded in your code |
| `/tbh:next` | What to do next based on progress |

## Available Books

Books are fetched from the [registry](https://github.com/tbhbooks/tbh-skill/blob/main/books/registry.json) — new books appear automatically, no plugin update needed.

| Book | Title | Status |
|------|-------|--------|
| `agents` | The Builder's Handbook: Agents That Think and Self-Improve | Draft Ready |

## How It Works

```
┌─────────────────────────────────────────────────┐
│  GitHub (source of truth)                       │
│                                                 │
│  tbhbooks/tbh-skill/books/registry.json         │
│    → lists all available books                  │
│                                                 │
│  tbhbooks/<book-repo>/spec/ch01/...             │
│    → chapter specs, interfaces, tests           │
│  tbhbooks/<book-repo>/chapters/ch01.md          │
│    → chapter content                            │
└──────────────────┬──────────────────────────────┘
                   │ curl (on each skill run)
                   ▼
┌─────────────────────────────────────────────────┐
│  Reader's project                               │
│                                                 │
│  .tbh/config.json      ← active book + state    │
│  .tbh/progress.json    ← per-book completion    │
│  .tbh/cache/           ← fetched content cache  │
│    ch03_interface-spec.md                       │
│    ch03_prompt-template.md                      │
│    validation/test_ch03.py                      │
└─────────────────────────────────────────────────┘
```

1. `/tbh:setup` fetches the book registry from GitHub, lets you pick a book, creates `.tbh/` state
2. Every skill fetches the files it needs from GitHub raw URLs, caches them locally
3. If offline, skills fall back to cached content
4. Progress is tracked per-book — switch freely without losing state
5. New books or chapter updates on GitHub are picked up automatically

## Adding a New Book

Add an entry to `books/registry.json`:

```json
{
  "id": "my-new-book",
  "title": "The Builder's Handbook: My New Book",
  "description": "...",
  "status": "draft",
  "total_chapters": 10,
  "default_language": "python",
  "repo": "https://github.com/tbhbooks/my-new-book",
  "raw_base": "https://raw.githubusercontent.com/tbhbooks/my-new-book/main",
  "spec_path": "spec",
  "chapters_path": "chapters"
}
```

That's it. Every reader's next `/tbh:setup` or `/tbh:switch` will see it.

## Philosophy

- **Guide, don't solve.** The plugin helps you build — it doesn't build for you.
- **Spec is the source of truth.** Every hint, review, and validation points back to the chapter spec.
- **Progressive hints.** First nudge is directional, second is specific, third shows pseudocode skeleton.
- **Language-agnostic.** You pick your language. Specs describe interfaces, not implementations.
- **Always fresh.** Content is fetched from GitHub — you always get the latest specs and tests.
