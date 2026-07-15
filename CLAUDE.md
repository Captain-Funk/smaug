# Smaug — Twitter/X Bookmark Archiver

> Forked from [alexknowshtml/smaug](https://github.com/alexknowshtml/smaug). Customized for CAPTAIN.

## What This Tool Does

Archives Twitter/X bookmarks to categorized markdown using Bird CLI + Claude API.
Output feeds into the-library knowledge pipeline.

## CAPTAIN Ecosystem Context

- **Team:** The Forge (business tooling)
- **Project:** The Archives — Knowledge Library
- **Config key:** `smaug` in `~/.claude/captain.config.json`
- **Category:** tool (forked third-party)

## Usage

1. Install Bird CLI (requires Twitter/X session cookies)
2. Credentials live in `~/.config/smaug/secrets.env` (chmod 600) as `AUTH_TOKEN`/`CT0` exports —
   NOT in `smaug.config.json` (moved 2026-07-15 per the token-leak-prevention rule; the loader
   reads those env vars natively, `src/config.js` ~line 230)
3. **Manual runs must source the secrets first:** `source ~/.config/smaug/secrets.env && npx smaug run`
   (add `--limit N` for batch size, `-t` for token tracking). Without the env vars there is no
   config-time error — bird just fails at fetch with an auth error.
4. The weekly launchd job (`com.captain.smaug`, Sun 09:00) sources the same file via its
   `/bin/zsh -c` wrapper — no separate credential copy exists.
5. Output: `./bookmarks.md` — categorized markdown
6. Processing command: `.claude/commands/process-bookmarks.md` (detailed categorization workflow)

## Security Notes

- Never put literal tokens back into `smaug.config.json` (gitignored, but the token-leak rule
  requires env vars). Rotate Twitter cookies → update `~/.config/smaug/secrets.env` only.
- Known residual exposure (accepted 2026-07-15): an OLD, since-rotated token pair is recoverable
  from git history via tag `v0.3.0` (commit `cbec40b`). Current tokens are NOT in history.
  If that tag is ever pushed anywhere public, scrub it first; the old pair (prefix 888e979d…)
  is presumed revoked — verify if in doubt.

## Cross-Repo Rules

- **Reference but not update.** When working in this repo, you can READ from other CAPTAIN repos. Never WRITE to another repo.
- **Branch strategy:** `main` stays synced with upstream via rebase. All CAPTAIN customizations go on the `captain` branch.
- **After fresh clone:** Run `bash ~/Projects/captain-plugin/hooks/bootstrap.sh` to restore hookify symlinks.

## Upstream Tracking

- **Upstream:** https://github.com/alexknowshtml/smaug.git
- **Sync:** `git fetch upstream && git checkout main && git rebase upstream/main && git checkout captain && git rebase main`

## Knowledge Pipeline Integration

Smaug output (markdown) can feed into the-library repo:
- Path: `~/Projects/the-library` (config key: `library`)
- Manual transfer for now — automated pipeline is future work
