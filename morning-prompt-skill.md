---
title: Morning Prompt Delivery
description: Skill for selecting and delivering a random morning writing prompt
version: 1.0
---

# Morning Prompt Skill

## Purpose

You deliver a daily morning writing prompt from the prompt collection at `~/.openclaw/workspace/prompts/morning-prompts.md`.

## Behavior

When triggered by the morning cronjob:

1. Read the file `prompts/morning-prompts.md` from the workspace
2. Parse the three categories: Intention Setting, Creative Writing, Productivity
3. Select a category using this weighted rotation:
   - Monday/Wednesday/Friday: Productivity (60%) or Intention Setting (40%)
   - Tuesday/Thursday: Creative Writing (60%) or Intention Setting (40%)
   - Saturday: Creative Writing (70%) or Intention Setting (30%)
   - Sunday: Intention Setting (80%) or Creative Writing (20%)
4. Randomly select one prompt from that category — scan `journal/daily/` for the last 7 entries to avoid repeats (read the **Prompt #[N]** lines); this works even when memory is off
5. Deliver via the configured notification channel with this format:

```
☀️ Good morning!

**[Category Name]** — Prompt #[N]

[The prompt text]

Take 10 minutes with this one. No pressure to be brilliant — just be honest.
```

6. Immediately after sending, create a journal entry at:

   `journal/daily/YYYY-MM-DD.md` (using today's date)

   with this structure:

````markdown
# YYYY-MM-DD

## Morning Prompt

**Category**: [Category Name]
**Prompt #[N]**: [The prompt text]

## Response

_(no response yet)_
````

   If the file already exists for today (e.g. created by another skill), append the Morning Prompt section rather than overwriting.

## On user reply

If the user responds to the prompt in conversation:

1. Engage thoughtfully with what they wrote — one or two sentences, no more unless they invite more
2. Append their response to the journal entry, replacing the `_(no response yet)_` line:

````markdown
## Response

[Their response, reproduced faithfully]

---
*Logged [HH:MM]*
````

   Do not summarize or paraphrase — log their words exactly as written.

## Guidelines

- Keep your added commentary brief — one sentence max
- Vary the sign-on/off occasionally to keep it fresh
- Do not hardcode any channel or model — always read from config