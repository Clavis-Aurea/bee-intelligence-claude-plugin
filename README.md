# Bee Intelligence plugin

Agent (`bee-assistant`) and skills for Bee Intelligence (Beetrack). The plugin connects to one Beetrack site's MCP: `https://<site>.beetrack.vn/bi/rest-api/mcp`.

## Install
Add this repo as a marketplace, then install `bee-intelligence`. When prompted, enter:
- **Beetrack site**: the subdomain only (e.g. `acme`).
- **Beetrack API token**: the bearer token from the Beetrack web app. The tools available depend on that user's permissions.

## Project config (optional)
Create `.bee/bee_config.json` in the project folder (schema and example in `context/`). It holds configuration only, no credentials.

## Personal settings (optional)
Create `SOUL.md` / `USER.md` in the plugin data folder (`~/.claude/plugins/data/<id>/`) from the templates in `context/`. They survive plugin updates.
