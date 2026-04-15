---
name: validate
description: Run validation tests for your current chapter implementation. Fetches tests from GitHub and checks your code against the spec's test suite. Usage - /tbh:validate or /tbh:validate 3
allowed-tools: Bash Read Grep Glob WebFetch Write
argument-hint: <chapter-number> (optional, defaults to current chapter)
---

# TBH Validate

Run the chapter's validation tests against the reader's implementation.

## Fetch Preamble

1. **Read `.tbh/config.json`** for active book, chapter, `raw_base`, and `spec_path`. If missing, tell reader to run `/tbh:setup` first.
2. **Determine chapter number.** Use argument if provided, otherwise `current_chapter`. Format as two digits.
3. **Fetch the test file from GitHub:**
   ```bash
   mkdir -p .tbh/cache/validation
   curl -sf "<raw_base>/<spec_path>/ch<NN>/validation/test_ch<NN>.py" -o .tbh/cache/validation/test_ch<NN>.py
   ```
   If fetch fails, use cached version. If no cache, tell reader: "No validation tests available for this chapter yet."

## Steps

4. **Read the fetched test file** to understand what it expects (command to run, expected output format).

5. **Run the tests:**
   ```bash
   cd <reader's project directory>
   python -m pytest .tbh/cache/validation/test_ch<NN>.py -v 2>&1 || python .tbh/cache/validation/test_ch<NN>.py 2>&1
   ```

6. **Report results clearly:**

   If all pass:
   ```
   ✓ Chapter <N> — All tests passing!

   Run /tbh:next to see what's ahead.
   ```
   Update `chapters_completed` in config and progress.

   If some fail:
   ```
   Chapter <N> Validation:
     ✓ test_tool_interface_exists
     ✓ test_read_file_tool
     ✗ test_search_code_tool — pattern parameter missing
     ✗ test_tool_registry_match — returned 0 results

   2/4 passing

   Hint: Read the interface-spec for search_code's parameter schema.
   Run /tbh:hint for more guidance.
   ```

7. **Never reveal test implementation details** that would give away the answer. Summarize what failed, not how to fix it.

## Star Prompt (Chapter 1 completion, per book)

After Chapter 1 passes all tests, check `.tbh/progress.json` for a per-book star tracking field.

The `progress.json` structure for this:
```json
{
  "book": "agents",
  "started_at": "...",
  "chapters": {},
  "stars_prompted": {
    "agents": true,
    "llm-inference": true
  }
}
```

**Logic:** Read the active book ID from config. Check if `stars_prompted.<active_book>` is `true` in `progress.json`.

If it is NOT `true` (or `stars_prompted` doesn't exist yet):

1. **Show a message after the success report:**
   ```
   ✓ Chapter 1 complete — you just built your first agent loop!

   tbh, if this is helping you learn, a star goes a long way:
   👉 <repo URL from config>

   Or if you have the GitHub CLI:
     gh repo star <org/repo-name>
   ```

2. **Update `.tbh/progress.json`** — set `stars_prompted.<active_book>` to `true`.

If already `true` for this book, skip entirely. This means:
- Each book gets one star prompt (after its Ch 1)
- Switching to a new book and completing its Ch 1 triggers a fresh prompt for that book's repo
- No book ever prompts twice
