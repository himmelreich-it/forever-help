# WoW Forever helper

Instructions for Claude Code in this repo. Personal details (characters, spec plans, region, personal preferences) live in `CLAUDE.local.md` and `.env`, which are git-ignored. Read `CLAUDE.local.md` if it exists before answering any WoW question; if `.env` or `CLAUDE.local.md` is missing, run the first-run setup below.

The player doesn't run anything themselves: you run `./bnet`, handle logins and do the research. The script exists for you.

## First-run setup

When the player says "set me up", or `.env` / `CLAUDE.local.md` is missing:

1. Check the player has a Battle.net API client with redirect URI `http://localhost:8765/callback` (README step 1). If not, walk them through creating one.
2. Create `.env` from `.env.example`. Ask for the client ID, region (eu/us/kr/tw) and main character slug, and fill them in.
3. Ask the player to paste the **client secret into `.env` themselves** (`BNET_CLIENT_SECRET=`). Never ask for it in chat.
4. Log in (see "Logging in" below), then run `./bnet chars` to confirm, and use it to correct the main slug if needed.
5. Interview the player (main character's role, spec plan, talent path, alts, how they like answers) and write `CLAUDE.local.md` from `CLAUDE.local.md.example`. Show it to them before saving.

## Logging in

You handle logins. User tokens last 24 hours; any 401/403 on account data means it expired.

1. Run `./bnet login` in the background. It prints a login link, tries to open the browser and waits up to 3 minutes for the redirect on port 8765.
2. Give the player the link and ask them to log in. Nothing else is needed from them: the script catches the redirect itself.
3. When the command finishes ("Logged in."), re-run what failed.
4. Only if the redirect can't reach port 8765 (e.g. a cloud session, where the player's browser isn't on this machine), the command ends with "No redirect received". Ask the player to paste the full address-bar URL they landed on, then run `./bnet login --url '<that URL>'`. Codes expire within minutes, so do it right away.

## How to work

- Check facts before stating them. Pull live data with `./bnet` instead of guessing.
- Say when something is unverified. WoW Forever is **not** WoW Classic: items, stats and quests have changed (e.g. Verigan's Fist has Intellect/Spirit and a Holyfire proc in Forever). Classic-era knowledge is a lead, not a fact; mark it "verify".
- Cite sources for game facts taken from the web.
- Judge advice against the character's plan in `CLAUDE.local.md` (role, spec), not in general. Example of a mistake to avoid: comparing a tank's one-hander with a two-hander when they play sword and board.
- When the player says "do not update the artifact/file", answer in chat only.
- Always fetch live data. Never store character snapshots in the repo.
- Guides and write-ups: publish as a **claude.ai artifact** (maps, checklists) where the artifact tool is available; otherwise ask.
- **When the player corrects you**, propose the exact change to `CLAUDE.md` (shared rule) or `CLAUDE.local.md` (personal), and edit only after they approve. Nothing personal goes into `CLAUDE.md`, the script or any other committed file.

## Game context

- No realms: WoW Forever uses phasing. Character endpoints use the slug `name-surname` instead of a realm.
- Rulesets exist (e.g. Normal); the character summary shows which one.
- Quest log cap: **40**.
- Level cap: unknown. Check before assuming (the beta was capped at 30).

## Quest guides: required format

When asked about a dungeon or a set of quests:

1. **Check the quest log and completed quests first** (`./bnet char quests`, `./bnet char quests/completed`) and tailor the guide: skip finished quests, use ones already in the log.
2. **Weave in class quests** for the character's class, but first show the **rewards and whether they are worth it for the character's role and spec**, so the player can decide.
3. **One section per chain.** Name each chain, list its quests in order, and say whether chains are independent of each other.
   - **Label each chain by the effort outside the dungeon**, not by its quest count. A chain whose only outside steps are pick-up, a short delivery on the way, or the turn-in is an **in-dungeon chain, one trip**; say so in its label and summary. Reserve "multi-zone chain" for chains with real work elsewhere (other zones, other dungeons, escorts). Never make a two-quest dungeon chain look like legwork to do before the run.
4. **Never write "part 1 / part 2".** Use quest names.
5. **Pick-up point, clearly marked:** NPC and location for every single quest and for the **first quest** of every chain.
6. **Quest level next to every quest** (minimum level). Say when sources disagree or none gives one.
   - **Shareable status next to every quest**: shareable, not shareable, or unknown. Warcraft Tavern's Forever dungeon quest guides list "Shareable: Yes/No" per quest; Method does not. Never guess; show "unknown" when no source says.
7. **One overview map with every step in order**, plus a small map per chain. Pins numbered to the steps; a pin visited twice shows both numbers ("8·11").
8. **A pick-up table:** quest, giver, location, route stop.
9. Tag unverified, Classic-derived steps with "verify".
10. Call out dependencies that bite, e.g. a follow-up offered inside a dungeon only if you carry the first quest in.

### Artifact notes

- Maps are schematic inline SVG (not to scale). Do **not** use `<symbol>` + `<use>` for map shapes: the page's CSS does not reach the copies and they render black. Put the shapes inline in every map.
- A checklist per step, ticks saved in the browser.
- Works in light and dark mode and at phone width.

## Gear advice

- Compare like with like: one-hander vs one-hander, shield vs shield, two-hander vs two-hander.
- Weight stats by the character's role (e.g. a tank: Strength, Stamina, armor, Defense, block).
- Say how each item is obtained. If unknown, say so. Hints from item data: "Binds when equipped" means it can be bought on the Auction House; required level 0 plus "Binds when picked up" usually means a quest reward.
- Use `./bnet search` to find candidates and `./bnet char equipment` for the current gear.

## Battle.net API

### Use the script

`./bnet` (Python 3, standard library only) wraps the API. Prefer it over hand-written curl. `NAME` is a character slug and defaults to `BNET_MAIN`.

| Command | What it returns |
|---|---|
| `./bnet login` | Logs in (user token). Run it in the background; see "Logging in". |
| `./bnet chars` | All characters on the account, with slug and protected-character id |
| `./bnet char [NAME] [SUB]` | Summary, or `equipment`, `statistics`, `specializations`, `professions`, `quests`, `quests/completed`, `appearance`, `titles` |
| `./bnet protected [NAME]` | Gold, deaths, current zone, hearthstone |
| `./bnet item ID [ID ...]` | One line per item: quality, ilvl, req level, slot, DPS, armor/block, stats, procs, class, binding |
| `./bnet quest ID [ID ...]` | One line per quest: zone, type (Dungeon, Elite, …), XP, money, reputation. Quest IDs come from `./bnet char quests` |
| `./bnet search --slot SHIELD --quality RARE --level 22-34 [--name TEXT]` | Search plus details per hit. Slots: `WEAPON`, `WEAPONMAINHAND`, `TWOHWEAPON`, `SHIELD`, … |
| `./bnet raw PATH [--static] [key=value ...]` | Any endpoint as JSON |

Add `--json` to any command for raw JSON. A 401/403 on account data means the user token expired: log in again yourself (see "Logging in"). When you find a useful endpoint without a command, propose adding one.

### Configuration

All from `.env` (git-ignored; template `.env.example`) or the environment: `BNET_CLIENT_ID`, `BNET_CLIENT_SECRET`, `BNET_REGION` (default `eu`), `BNET_LOCALE` (default `en_GB`), `BNET_MAIN`, optional `BNET_REDIRECT`.

- Each player uses their own Blizzard API app with redirect URI `http://localhost:8765/callback` registered.
- In a Claude **cloud** environment with Battle.net connected, there may be no `.env` secret: the network proxy adds the app credentials to `oauth.battle.net` requests. The login listener can't receive the browser redirect there, so use the paste fallback.
- Tokens are cached in `.bnet-cache.json` (git-ignored, mode 600) until they expire.

### Tokens

| Token | How | Used for | Lifetime |
|---|---|---|---|
| App token | `POST https://oauth.battle.net/token`, `grant_type=client_credentials`, client ID + secret as basic auth | Game data: items, classes, search | 24 h |
| User token | Authorization code flow, scope `wow.profile` | The player's account and characters, including protected data | 24 h |

User token flow (`./bnet login` does all of it):

1. Open `https://oauth.battle.net/authorize?response_type=code&client_id=<ID>&redirect_uri=<REDIRECT>&scope=wow.profile&state=<random>` and log in.
2. The browser lands on `<REDIRECT>?code=...&state=...`. Check `state` matches.
3. Exchange quickly (codes expire within minutes, single use): `POST /token` with `grant_type=authorization_code`, `code` and the same `redirect_uri`.
4. Cache the token with its expiry.

### Namespaces

- Profile: **`profile-forever-{region}`**. `profile-{region}`, `profile-classic-*`, `profile-classic1x-*`, `profile-classicann-*` return 404; `profile-classic-forever-*` returns 403.
- Static game data: **`static-forever-{region}`**. Versioned forms also work (e.g. `static-1.60.1_70399-forever-eu`) but change with patches.

### Endpoints that work

Account and character (user token, profile namespace):

- `GET /profile/user/wow`: account with all characters.
- `GET /profile/user/wow/protected-character/{realmId}-{characterId}`: gold, deaths, position, hearthstone. Take the path from the account response (`protected_character.href`).
- `GET /profile/wow/character/{slug}` plus `/equipment`, `/statistics`, `/specializations`, `/professions`, `/quests`, `/quests/completed`, `/appearance`, `/titles`.

Game data (app token, static namespace):

- `GET /data/wow/item/{id}`: quality, item level, required level, slot, damage/speed/DPS, armor, block, stats, on-hit effects, class restriction, binding.
- `GET /data/wow/search/item`: `inventory_type.type`, `quality.type`, `level=[a,b]`, `name.en_GB`, `orderby`, `_pageSize`, `_page`.
- `GET /data/wow/quest/{id}`: title, zone (`area`), type, XP, money and reputation rewards. It does **not** list item rewards, quest level, minimum level or shareability; get those from guides.
- Quest IDs: `/profile/wow/character/{slug}/quests` and `/quests/completed` include an `id` per quest.

### Gotchas

- With curl, pass `-g` when a URL contains `[ ]`; otherwise curl treats them as globs (exit code 3).
- Name search is fuzzy and noisy; filter the results.
- Item data does **not** say where an item comes from.
- Items with IDs above 200000 are new in Forever; Classic sites have nothing on them.
- Web sources: Wowhead blocks automated reading (also headless browsers), forever.warcraftdb.com renders in JavaScript, Icy Veins returns 403. Method and Warcraft Tavern guides are readable.

### Never

- Commit tokens, client secrets, authorization codes, `.env`, `CLAUDE.local.md` or personal character data.
- Send account data anywhere other than the Battle.net API.
