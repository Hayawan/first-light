---
title: First Light: Morning Prompt Delivery
description: Skill for selecting and delivering a daily morning writing prompt with daily anchor questions
version: 1.0
---

# Morning Prompt Skill

> **Prerequisites**: Requires the morning-prompt cronjob to be configured.
> If it isn't, see `cronjob-setup.md` before proceeding.

## Purpose

You deliver a daily morning writing prompt from the prompt collection at `~/.openclaw/workspace/prompts/morning-prompts.md`, preceded by two fixed Daily Anchor questions that appear every day regardless of which prompt is selected.

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

**Start here — keep these short:**

1. What's your goal for today?
   _(1–2 sentences. What would make today a success?)_

2. What are 3 things you'd like to accomplish today?
   _(Concrete tasks or actions, not aspirational statements.)_

---

**[Category Name]** — Prompt #[N]

[The prompt text]

Take 10–15 minutes with this one. No pressure to be brilliant — just be honest.
```

6. Immediately after sending, create a journal entry at:

   `journal/daily/YYYY-MM-DD.md` (using today's date)

   with this structure:

````markdown
# YYYY-MM-DD

## Daily Anchors

**Goal for today:**
_(no response yet)_

**3 things to accomplish:**
_(no response yet)_

## Morning Prompt

**Category**: [Category Name]
**Prompt #[N]**: [The prompt text]

## Response

_(no response yet)_
````

   If the file already exists for today, append the Daily Anchors and Morning Prompt sections rather than overwriting.

## On user reply

When the user responds to the morning message, their reply will typically contain answers to both the anchor questions and the freeform prompt. Log them separately:

1. Parse the reply into two parts:
   - **Anchor answers** — the goal and 3 accomplishments (usually at the top, short)
   - **Prompt response** — the longer freeform writing

2. Update the journal entry, replacing the placeholder lines:

````markdown
## Daily Anchors

**Goal for today:**
[Their stated goal, verbatim]

**3 things to accomplish:**
1. [Item 1]
2. [Item 2]
3. [Item 3]

## Morning Prompt

**Category**: [Category Name]
**Prompt #[N]**: [The prompt text]

## Response

[Their freeform writing, reproduced faithfully]

---
*Logged [HH:MM]*
````

3. Engage briefly with what they wrote — one or two sentences responding to the prompt content, not the anchors. The anchors are for tracking, not discussion, unless the user brings them up.

**Parsing guidance**: If the user's reply doesn't clearly separate anchors from prompt, use context to make your best call. Short items at the top are likely anchors; the longer paragraph(s) are the prompt response. When ambiguous, log everything under Response and leave a `<!-- parser note: anchors unclear -->` comment in the journal.

## Guidelines

- Keep your added commentary brief — one sentence max after the prompt closes
- Vary the sign-off occasionally to keep it fresh
- Do not hardcode any channel or model — always read from config
- Never summarize or paraphrase the user's writing when logging — their words exactly