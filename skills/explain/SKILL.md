---
name: explain
description: Explain a concept from the current book in the context of what you've built so far. Fetches relevant chapter from GitHub. Usage - /tbh:explain agent-loop or /tbh:explain mcp
allowed-tools: Read Grep Glob Bash WebFetch
argument-hint: <concept or topic to explain>
---

# TBH Explain

Explain a concept from the book, grounded in what the reader has already built.

## Fetch Preamble

1. **Read `.tbh/config.json`** for active book, current chapter, `raw_base`, `chapters_path`. If missing, tell reader to run `/tbh:setup` first.
2. **Identify the concept** from the user's argument and map it to the relevant chapter number(s).
   Common concept-to-chapter mappings for the "agents" book:
   - agent-loop, observe-think-act → ch01
   - augmented-llm, system-prompt, structured-output → ch02
   - tools, mcp, tool-calling → ch03
   - skills → ch04
   - file-system, shell, sandboxing → ch05
   - memory, context → ch06
   - planning, reasoning → ch07
   - evaluation, guardrails → ch08
   - self-improvement → ch09
   - multi-agent, splitting → ch10
   - broadcast, discovery → ch11
   - peer-communication, a2a → ch12
   - swarm → ch13
   - production → ch14
   - ecosystem → ch15

3. **Fetch the relevant chapter(s) from GitHub:**
   ```bash
   curl -sf "<raw_base>/<chapters_path>/ch<NN>.md" -o .tbh/cache/ch<NN>_chapter.md
   ```

## Steps

4. **Read the fetched chapter content.** Search for the concept within it.

5. **Read the reader's implementation** to ground the explanation in their code.

6. **Explain the concept:**
   - Start with what it IS in one sentence
   - Connect it to what they've already built: "In your Ch 2 agent, you have X. This concept extends that by..."
   - Use a concrete example from the book or spec
   - Include a Mermaid diagram if the chapter has one for this concept
   - If the concept is from a future chapter, give a preview without spoilers: "You'll build this in Ch N — for now, just know that..."

## Tone
- Conversational, not academic. "Here's the thing..." not "This concept is defined as..."
- Always connect back to the reader's own code when possible.
- If the concept spans multiple chapters, show how it evolves: "In Ch 4 it's static, by Ch 9 the agent rewrites it, by Ch 11 agents share it."
