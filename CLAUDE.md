# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Status

This repository contains the configuration and documentation for the `discord-jobs` skill — a Claude Code skill that scrapes job boards and posts opportunities to a Discord Forum Channel.

## Environment Configuration

The project uses a `.env` file for secrets management with the following variables:

- `DISCORD_BOT_TOKEN` - Discord bot authentication token
- `DISCORD_CHANNEL_ID` - ID of the Forum Channel where jobs are posted
- `DISCORD_WEBHOOK_URL` - Webhook URL for creating threads in the channel

**Security note:** Never commit `.env` or expose these values in code, logs, or error messages.

## Skill Architecture

### Location
- **Skill definition:** `~/.claude/skills/discord-jobs/skill.md` (global skill)
- **Reference files:** `~/.claude/skills/discord-jobs/references/`

### Reference Files

| File | Purpose |
|------|---------|
| `setup-discord-bot.md` | Guide to create Discord bot, obtain token, configure permissions |
| `portales.md` | List of job board portals to scrape (editable by user) |

### Workflow

1. Read `references/portales.md` to get configured job portals
2. Scrape each portal using `WebFetch` tool
3. Fetch existing Discord threads via Discord API (bot token)
4. Detect duplicates by comparing links and titles
5. Post new jobs via webhook, update existing via bot API
6. Report results summary

### Commands

Use the skill via:
```
/discord-jobs
```

The skill auto-triggers when the user mentions:
- Posting jobs to Discord
- Scraping job portals (LinkedIn, GetOnBoard, Bumeran, etc.)
- Checking for new offers to publish
- Updating the Discord feed
