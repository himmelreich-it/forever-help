# WoW Forever helper

Open a Claude Code session in this repo and ask about your WoW Forever character in plain language. Claude does the work: it logs in to the Battle.net API, reads your live character data, researches quests and gear, and builds guides with routes and maps.

Examples:

- "Make me a quest guide for Blackfathom Deeps."
- "Which shields and one-handers are upgrades for me right now?"
- "What's left in my quest log for Redridge?"

## Getting started

1. **Create a Battle.net API client** (one time; Claude can't do this for you). Go to https://develop.battle.net/access/clients, create a client and add the redirect URI `http://localhost:8765/callback`.
2. **Start Claude Code in this folder** and say "set me up".

Claude takes it from there: it creates the config, asks for your region and main character, tells you where to put the client secret, logs you in through your browser and writes a personal notes file with your characters and plans. Logins last 24 hours; Claude renews them when needed.

## Your data stays local

`.env` (API credentials), `CLAUDE.local.md` (your characters and plans) and the token cache are git-ignored. Don't commit them.

## What's in the repo

- `CLAUDE.md`: how Claude works here (guide format, gear advice, API usage).
- `bnet`: the Battle.net API helper Claude uses. You don't need to run it yourself.
- `.env.example`, `CLAUDE.local.md.example`: templates Claude fills in during setup.
