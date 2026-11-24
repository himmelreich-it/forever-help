# WoW Forever helper

A small toolkit for using Claude Code as a WoW Forever companion: quest-route guides, gear checks and live character data from the Battle.net API.

- `bnet`: command-line helper for the Battle.net API (Python 3, no dependencies).
- `CLAUDE.md`: instructions Claude Code follows in this repo (guide format, gear advice, API usage).
- `CLAUDE.local.md`: your own characters and plans (git-ignored). Start from `CLAUDE.local.md.example`.

## Setup

1. Create an API client at https://develop.battle.net/access/clients and add the redirect URI `http://localhost:8765/callback`.
2. `cp .env.example .env` and fill in your client ID, secret, region and main character slug (`name-surname`, lower case).
3. `cp CLAUDE.local.md.example CLAUDE.local.md` and describe your characters and plans.
4. `./bnet login`, log in in the browser that opens.
5. Try `./bnet chars` and `./bnet char equipment`.

Logins last 24 hours; run `./bnet login` again when account commands report 401/403.

## Commands

```
./bnet login
./bnet chars
./bnet char [NAME] [equipment|statistics|specializations|professions|quests|quests/completed|appearance|titles]
./bnet protected [NAME]
./bnet item ID [ID ...]
./bnet search --slot SHIELD --quality RARE --level 22-34 [--name TEXT]
./bnet raw PATH [--static] [key=value ...]
```

Add `--json` for raw output. `NAME` defaults to `BNET_MAIN`.

`.env`, `CLAUDE.local.md` and the token cache are git-ignored; keep it that way.
