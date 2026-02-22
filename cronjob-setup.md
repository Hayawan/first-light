# First Light: Morning Prompt Cronjob Setup

## Steps

### 1. Place the prompt file

```bash
mkdir -p ~/.openclaw/workspace/prompts
cp morning-prompts.md ~/.openclaw/workspace/prompts/morning-prompts.md
```

### 2. Install the skill

```bash
mkdir -p ~/.openclaw/workspace/skills/first-light
cp SKILL.md ~/.openclaw/workspace/skills/first-light/SKILL.md
```

### 3. Add the cronjob

Before writing the job entry, ask the user:

- **Schedule**: "What time should I send your morning prompt? (e.g. 7:00 AM — and what timezone are you in so I can convert to server time?)"
- **Model**: "Which model should run this job? It's lightweight, so a fast/cheap model is ideal. Name any model your instance has access to."
- **Delivery**: "Which channel should I deliver to, and what's the destination ID?
  - Matrix: room ID (e.g. `!abc123:your.server`)
  - Telegram: chat ID (e.g. `123456789`)
  - Discord: channel ID or webhook URL
  - Other: the destination value your channel config expects"

Use `exec` (or `bash`) to register the job — do not call the `cron` tool directly, as it can deadlock with the gateway:

```bash
exec: openclaw cron add \
  --name "first-light" \
  --cron "0 7 * * *" \
  --tz "USER_TIMEZONE" \
  --session isolated \
  --wake-mode now \
  --model "USER_MODEL" \
  --message "It's morning prompt time. Read the file at prompts/morning-prompts.md in the workspace. Pick a random prompt from an appropriate category for today (check the day of the week and follow the weighting in your first-light skill). Send it via the configured notification channel in a clean, encouraging format. Keep your commentary to one sentence." \
  --announce \
  --channel USER_CHANNEL \
  --to "USER_DESTINATION" \
  --best-effort-deliver
```

Substitute the user's values for all `USER_*` placeholders before running.

**`--wake-mode now`** — ensures the job fires immediately if the gateway was offline at the scheduled time, rather than skipping until the next occurrence.

### 4. Restart the gateway

```bash
sudo systemctl restart openclaw
```

### 5. Verify

```bash
exec: openclaw cron list
exec: openclaw cron run first-light
```

Confirm the test run delivered to the user's configured channel before closing out.