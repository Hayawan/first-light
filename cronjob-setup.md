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
  --message "Follow the logic in the 'first-light' skill to select a daily prompt from 'prompts/morning-prompts.md'. Once you have the final, formatted prompt text, you MUST send it by calling the message tool with these exact parameters: message(action='send', channel='USER_CHANNEL', to='USER_DESTINATION', message='<your formatted prompt text>')." \
  --announce \
  --best-effort-deliver
```

Substitute the user's values for all `USER_*` placeholders before running. Note that `USER_CHANNEL` and `USER_DESTINATION` belong inside the `--message` string — this is intentional. The isolated agent session has no ambient delivery context, so the target must be part of the task instruction itself.

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