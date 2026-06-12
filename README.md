# Project Manager Stack — a protoAgent plugin bundle

A **bundle** (ADR 0040): a curated, pinned set of plugins you install with one command.
`pm-stack` stands up a board-driven project-management agent — it decomposes an idea,
dispatches coding agents to ship it, and gives the agent a real browser.

## What's in it
| Plugin | Role |
|---|---|
| **[project_board](https://github.com/protoLabsAI/projectBoard-plugin)** | beads-backed 6-state board + ACP spawn loop + adversarial planning + Kanban/list view |
| **[agent_browser](https://github.com/protoLabsAI/agent-browser-plugin)** | browser automation (vercel-labs/agent-browser) + a live Browser panel |
| **delegates** | the ACP/A2A spawn spine (ships with protoAgent) |

The two external plugins are **pinned to release tags** — `pm-stack` is a *tested combo*,
not "whatever's latest." The pin means **"last verified working"** and only moves through
a passing verification (ADR 0049): CI installs this manifest's pin set into a scratch
agent and probes every declared console view on each PR + weekly, and a scheduled job
opens a pin-bump PR when a member tags a new release. `verified_against:` in the manifest
records the core version the set was last verified on.

```bash
# verify locally (from a protoAgent checkout, deps synced)
uv run --no-sync python /path/to/pm-stack/scripts/verify_bundle.py /path/to/pm-stack

# check members for newer release tags (rewrites refs in place)
python3 scripts/check_bundle_updates.py protoagent.bundle.yaml
```

## Install
```bash
python -m server plugin install https://github.com/protoLabsAI/pm-stack
```
That fans out and installs each member (pinned in `plugins.lock`). It does **not** enable
anything — install ≠ enable ≠ trust. To turn the stack on, the installer prints the suggested
`plugins.enabled` list + config; apply them to your `config/langgraph-config.yaml`:
```yaml
plugins:
  enabled: [delegates, project_board, agent_browser]
agent_browser:
  panel_mode: full
```
then restart. Each plugin's own README covers its prerequisites (the board needs the `br`
CLI; agent_browser needs the `agent-browser` binary).

## See it running
**[roxy](https://github.com/protoLabsAI/roxy)** runs this stack — the reference host.
