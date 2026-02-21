# Morning Prompt Skill — README

A daily writing prompt skill for OpenClaw (also compatible with Claude Code). Delivers one prompt each morning from a curated collection across three categories: Intention Setting, Creative Writing, and Productivity.

## Files

| File | Purpose |
|---|---|
| `morning-prompts.md` | The prompt library (60 prompts across 3 categories) |
| `morning-prompt-skill.md` | Skill definition — read by the agent at runtime |
| `cronjob-setup.md` | Agent instructions for configuring the cronjob |

## Quick start

Hand `cronjob-setup.md` to your OpenClaw agent (or paste it into Claude Code) and ask it to set up the morning prompt job. It will walk through the steps and ask you for your preferred schedule and model before writing any config.

## Notes to Human

**Notification channel**: This skill assumes a notification channel is already configured in OpenClaw. It doesn't set one up — that's a separate step done when you first configure your gateway. If you haven't done that yet, do it before running the cronjob setup. Supported channels typically include Matrix, Telegram, Discord, Slack, ntfy, and email — check your OpenClaw gateway config docs for the `notify` block.

**Model choice**: The cronjob setup will ask which model to use. This is a lightweight read-and-format task — any fast, cheap model is fine. Good defaults:
- `google/gemini-flash-2.0` — lowest cost (~$0.01/month), good quality
- `anthropic/claude-haiku-4-5` — reliable Anthropic option (~$0.05–0.09/month with caching)
- Any local Ollama model — zero API cost if you're running models locally

**Schedule and timezone**: The cron expression in `cronjob-setup.md` defaults to `0 7 * * *` (7:00 AM server time). If your VPS is in UTC and you're not, the agent will convert your local time during setup — just tell it your timezone.

**Prompt rotation**: The skill tries not to repeat prompts within a 7-day window if OpenClaw memory is enabled. If memory is off, rotation is random each run.

**Claude Code usage**: Drop `morning-prompt-skill.md` into your project and reference `morning-prompts.md` from the same directory. The cronjob setup won't apply directly, but you can trigger the skill manually and the delivery/selection logic still holds.