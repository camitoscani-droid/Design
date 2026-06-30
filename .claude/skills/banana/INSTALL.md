# Banana Claude -- Installation & Setup

This skill is committed to the repo, so it loads automatically in every Claude
Code session that has this repo checked out. The only per-environment step is
configuring your Google AI API key (a secret, NOT stored in the repo).

## One-time setup per environment

1. Get a free Google AI API key: https://aistudio.google.com/apikey

2. Configure the MCP server with your key:
   ```bash
   python3 .claude/skills/banana/scripts/setup_mcp.py --key YOUR_KEY
   ```
   This writes the MCP config (including the key) to `~/.claude/settings.json`,
   which is local to your machine/container and never committed.

3. Verify:
   ```bash
   python3 .claude/skills/banana/scripts/validate_setup.py
   ```

4. Restart Claude Code so the MCP server loads.

## Persisting the key in Claude Code on the web

The container is ephemeral, so `~/.claude/settings.json` is wiped between fresh
sessions. To avoid re-running setup every time, set `GOOGLE_AI_API_KEY` as a
secret/environment variable in your Claude Code on the web environment settings.
The fallback scripts (`generate.py`, `edit.py`) read that env var directly.

## Usage

- `/banana generate <idea>` -- generate an image
- `/banana edit <path> <instructions>` -- edit an existing image
- `/banana batch <idea> [N]` -- generate N variations
- `/banana preset list|create|show|delete` -- manage brand presets
- `/banana cost summary|today|estimate` -- cost tracking

See `SKILL.md` for the full command reference and workflow.
