# API Key Setup

The scripts resolve the key in this order: `--api-key` flag → `RUNNINGHUB_API_KEY`
environment variable → (OpenClaw config, if present). For all non-OpenClaw agents,
set the environment variable.

## Check Status

Run `--check` first:
```bash
python3 {baseDir}/scripts/runninghub.py --check
```

React by `status`:
- `"ready"` → "Account ready! Balance ¥{balance}. What would you like to make?
  Images, videos, voiceovers — I've got you～" (reply in the user's language)
- `"no_key"` → Guide: 1) register runninghub.cn 2) create a key 3) recharge 4) set
  the `RUNNINGHUB_API_KEY` env var
- `"no_balance"` → "Balance is empty～ Top up to continue:
  https://www.runninghub.cn/vip-rights/4"
- `"invalid_key"` → "That key doesn't look right. Check it here:
  https://www.runninghub.cn/enterprise-api/sharedApi"

## Save Key (per agent)

When the user provides a key, verify it first:
```bash
python3 {baseDir}/scripts/runninghub.py --check --api-key THE_KEY
```

If valid, persist it as an environment variable for the user's agent:

| Agent | Where to set `RUNNINGHUB_API_KEY` |
|-------|-----------------------------------|
| **Claude Code** | `~/.claude/settings.json` under `env`, or export in shell profile |
| **Cursor** | `.cursor/mcp.json` env block, or shell profile |
| **Qoder** | `~/.qoder/mcp.json` env block, or shell profile |
| **OpenClaw** | Auto-injected via `primaryEnv` (no action needed) |
| **Any / CI** | Export in shell: `export RUNNINGHUB_API_KEY=...` (or Windows `setx`) |

Example (shell profile):
```bash
export RUNNINGHUB_API_KEY="rh-xxxxxxxxxxxxxxxx"
```

⚠️ **Never** commit the key to a repository or echo it back into chat history.
