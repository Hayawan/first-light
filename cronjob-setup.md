# Morning Prompt Cronjob Setup

## Steps

### 1. Place the prompt file

```bash
mkdir -p ~/.openclaw/workspace/prompts
cp morning-prompts.md ~/.openclaw/workspace/prompts/morning-prompts.md
```

### 2. Install the skill

```bash
mkdir -p ~/.openclaw/workspace/skills/morning-prompt
cp morning-prompt-skill.md ~/.openclaw/workspace/skills/morning-prompt/SKILL.md
```

### 3. Add the cronjob

Before writing the job entry, ask the user:

- **Schedule**: "What time should I send your morning prompt? (e.g. 7:00 AM — and what timezone are you in so I can convert to server time?)"
- **Model**: "Which model should run this job? It's lightweight, so a fast/cheap model is ideal. I'd suggest `google/gemini-flash-2.0` for lowest cost, or `anthropic/claude-haiku-4-5` if you prefer Anthropic. Or name any model your instance has access to."

Then add the following entry to `~/.openclaw/cron/jobs.json`, substituting the user's choices for `expression` and `model`:

```json
{
  "id": "morning-prompt",
  "name": "Morning Writing Prompt",
  "schedule": {
    "type": "cron",
    "expression": "0 7 * * *"
  },
  "enabled": true,
  "prompt": "It's morning prompt time. Read the file at prompts/morning-prompts.md in the workspace. Pick a random prompt from an appropriate category for today (check the day of the week and follow the weighting in your morning-prompt skill). Send it via the configured notification channel in a clean, encouraging format. Keep your commentary to one sentence.",
  "model": "google/gemini-flash-2.0",
  "announce": true
}
```

### 4. Restart the gateway

```bash
sudo systemctl restart openclaw
```

### 5. Verify

```bash
openclaw cron list
openclaw cron run morning-prompt
openclaw cron runs morning-prompt
```

Confirm the test run delivered to the user's notification channel before closing out.