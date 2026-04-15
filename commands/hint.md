---
name: hint
description: Get a targeted hint when you're stuck on the current chapter. Fetches the spec from GitHub, reads your code, gives a nudge without giving away the answer.
allowed-tools: Read Grep Glob Bash WebFetch
argument-hint: <optional description of what you're stuck on>
---

# TBH Hint

Give the reader a helpful nudge without spoiling the build.

## Fetch Preamble

1. **Read `.tbh/config.json`** for active book, chapter, language, `raw_base`, `spec_path`. If missing, tell reader to run `/tbh:setup` first.
2. **Format chapter as two digits** (e.g., `03`).
3. **Fetch the chapter spec files from GitHub** (or use cache if available and < 24hr old):
   ```bash
   curl -sf "<raw_base>/<spec_path>/ch<NN>/prompt-template.md" -o .tbh/cache/ch<NN>_prompt-template.md
   curl -sf "<raw_base>/<spec_path>/ch<NN>/interface-spec.md" -o .tbh/cache/ch<NN>_interface-spec.md
   ```
   Fall back to `.tbh/cache/` if fetch fails.

## Steps

4. **Read the fetched spec files:**
   - `prompt-template.md` — what they should be building
   - `interface-spec.md` — the contracts they need to satisfy

5. **Read the reader's current code.** Look for their implementation files:
   - Glob for files matching common patterns in the working directory
   - Focus on files related to this chapter's build target

6. **Compare what exists vs what the spec requires.** Identify:
   - Missing interfaces or functions
   - Incomplete implementations
   - Structural mismatches with the spec

7. **Give a targeted hint.** The hint should:
   - Point to the specific gap (what's missing or wrong)
   - Reference the relevant spec section
   - NOT provide the solution code
   - Use guiding questions: "What happens when X is empty?" / "Look at how the spec defines Y's return type."

## Hint Levels

If the reader asks for more help after a hint, escalate gradually:

- **Level 1 (default):** "Look at section X of the interface spec — your function signature doesn't match."
- **Level 2 (asked again):** "The spec expects `search_code` to accept a `pattern: string` parameter. Your version takes no arguments."
- **Level 3 (still stuck):** Show a pseudocode skeleton of the missing piece — but still not the full implementation.

## Tone
- Encouraging, not condescending. "You're close" not "You missed the obvious thing."
- Reference the spec as the authority: "The spec says..." not "You should..."
- If user provides a description of what they're stuck on, focus the hint on that specific area.
