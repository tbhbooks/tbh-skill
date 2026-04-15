---
name: next
description: Show what's next — your current progress and what chapter to tackle next. Fetches the next chapter's hook from GitHub.
allowed-tools: Read Glob Bash WebFetch
argument-hint: (no arguments)
---

# TBH Next

Show the reader what to do next based on their progress.

## Steps

1. **Read `.tbh/config.json`** and `.tbh/progress.json`. If missing, tell reader to run `/tbh:setup` first.

2. **Determine current state:**
   - Which chapters are completed
   - What the current chapter is
   - Whether validation tests exist and pass for the current chapter

3. **If current chapter is incomplete**, encourage finishing it:
   ```
   You're on Chapter <N>: <Title>

   <brief description of what you're building>

   Run /tbh:validate to check your progress.
   Run /tbh:hint if you're stuck.
   ```

4. **If current chapter is complete**, fetch and preview the next one:
   ```bash
   curl -sf "<raw_base>/<chapters_path>/ch<NN+1>.md" | head -40
   ```
   Extract the hook/teaser from the opening lines, then show:
   ```
   ✓ Chapter <N> complete!

   Next up — Chapter <N+1>: <Title>
   <one-line teaser from the chapter hook>

   What breaks: <the tension/problem from the next chapter's opening>

   Run /tbh:build-chapter <N+1> to start.
   ```

5. **If all chapters are complete:**
   ```
   You've completed all <N> chapters!

   You built <book build target> from scratch.

   What now:
   - Extend it further — your agent, your rules
   - Try another TBH book: /tbh:switch
   - Share what you built
   ```
