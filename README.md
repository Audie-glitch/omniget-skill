# OmniGet agent skill

**Action skill:** teach an agent to preview and download media with OmniGet’s CLI — not a contributor handbook.

Upstream app: [tonhowtf/omniget](https://github.com/tonhowtf/omniget)

## What the agent does

1. Ensure `omniget` / `omniget-cli` is available  
2. `omniget info <url>` (optionally `--json`)  
3. `omniget download` / `batch` / `import-cookies` as needed (skill covers Netscape cookie export / extension pairing)  
4. Report paths and errors  

Legal: only content the user’s session can already open — no DRM/paywall bypass.

## Layout

```text
omniget/SKILL.md   # agentskills-style
SKILL.md           # same, repo root
```

## Install into an agent

Copy `omniget/` into your skills folder, or load `SKILL.md` from this repo.

## License

GPL-3.0 (derived from OmniGet documentation and CLI surface).
