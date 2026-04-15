---
name: review
description: Review your chapter implementation against the spec. Fetches latest spec from GitHub. Checks completeness, structure, and gotchas before you validate. Usage - /tbh:review or /tbh:review 3
allowed-tools: Read Grep Glob Bash WebFetch
argument-hint: <chapter-number> (optional, defaults to current chapter)
---

# TBH Review

Review the reader's implementation against the chapter spec. This is a pre-validation check — architectural review, not test execution.

## Fetch Preamble

1. **Read `.tbh/config.json`** for active book, chapter, language, `raw_base`, `spec_path`, `chapters_path`. If missing, tell reader to run `/tbh:setup` first.
2. **Determine chapter number.** Use argument if provided, otherwise `current_chapter`. Format as two digits.
3. **Fetch spec files from GitHub** (or use cache):
   ```bash
   curl -sf "<raw_base>/<spec_path>/ch<NN>/interface-spec.md" -o .tbh/cache/ch<NN>_interface-spec.md
   curl -sf "<raw_base>/<spec_path>/ch<NN>/prompt-template.md" -o .tbh/cache/ch<NN>_prompt-template.md
   curl -sf "<raw_base>/<chapters_path>/ch<NN>.md" -o .tbh/cache/ch<NN>_chapter.md
   ```

## Steps

4. **Read the fetched spec and chapter files.**

5. **Read the reader's implementation.** Scan the working directory for relevant source files.

6. **Produce a review checklist:**

```
## Chapter <N> Review: <Title>

### Interfaces
- ✓ Tool interface — name, description, parameters, execute()
- ✓ ToolResult — output, success, error fields
- ✗ ParameterSchema — missing (spec requires typed parameter definitions)
- ⚠ ToolRegistry — exists but missing match() method

### Behavior
- ✓ read_file returns content on success, error on missing file
- ✗ search_code — not implemented yet
- ⚠ No verification step after tool calls (spec requires ground-truth check)

### Structure
- ✓ Clean separation of tool interface from implementations
- ⚠ Consider: tools are in one file — spec suggests separate modules

### Anti-patterns to watch
- <reference chapter-specific anti-patterns from the chapter text>
```

7. **Be specific.** Reference spec sections. Quote the interface names. Don't be vague.

8. **End with a clear recommendation:**
   - "Ready to validate — run `/tbh:validate`"
   - Or "Address the ✗ items first, then validate."
