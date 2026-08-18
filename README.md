# RunningHub Agent Skills

Give **any** AI coding agent the ability to generate images, videos, audio, and 3D
models through the [RunningHub](https://www.runninghub.cn) API â€” and to run any
RunningHub AI Application (custom ComfyUI workflow).

> Adapted for cross-agent use from
> [HM-RunningHub/OpenClaw_RH_Skills](https://github.com/HM-RunningHub/OpenClaw_RH_Skills)
> (Apache-2.0). Works with **Claude Code, Cursor, Qoder, OpenClaw, Windsurf**, and
> any agent that can read a `SKILL.md` and run a Python script.

## Why this exists

The upstream skill was written specifically for **OpenClaw**. This fork strips out
the OpenClaw-only assumptions (hard-coded `/tmp/openclaw` paths, OpenClaw config
storage, Chinese-only persona, OpenClaw `message` tool) and makes the skill
**agent-agnostic and cross-platform**, so it drops into Claude Code, Cursor, Qoder,
or any other agent that supports the Agent Skills format.

## Capabilities

| Category | Endpoints | What you can do |
|----------|-----------|-----------------|
| **Image** | 97 | text-to-image, image-to-image, upscale, Midjourney-style |
| **Video** | 213 | text-to-video, image-to-video, first/last-frame, extend/edit, motion control, multimodal |
| **Audio** | 20 | TTS, music generation, voice clone |
| **3D** | 16 | text-to-3D, image-to-3D, multi-image-to-3D |
| **Text** | 48 | image understanding, video understanding, text processing |
| **AI Apps** | âˆž | run any RunningHub AI Application (custom ComfyUI workflow) |

## Prerequisites

- **Python 3** and **curl** on the machine running the agent.
- A **RunningHub API Key** (Enterprise-Shared). Create one at
  <https://www.runninghub.cn/enterprise-api/sharedApi>, then top up your wallet.

## Install

The skill lives in `skills/runninghub/`. Copy that folder into your agent's skills
directory.

### Claude Code
```bash
# user-level (all projects)
git clone https://github.com/johndelapena168/runninghub-agent-skills /tmp/rh-skills
mkdir -p ~/.claude/skills
cp -r /tmp/rh-skills/skills/runninghub ~/.claude/skills/
```

### Cursor
```bash
# project-level
mkdir -p .cursor/skills
cp -r /path/to/runninghub-agent-skills/skills/runninghub .cursor/skills/
```

### Qoder
```bash
# user-level (all projects)
git clone https://github.com/johndelapena168/runninghub-agent-skills /tmp/rh-skills
mkdir -p ~/.qoder/skills
cp -r /tmp/rh-skills/skills/runninghub ~/.qoder/skills/
```
Or install as a Qoder plugin â€” this repo ships a `.qoder-plugin/plugin.json`.

### OpenClaw
```
ä»Ž https://github.com/johndelapena168/runninghub-agent-skills å®‰è£… RunningHub æŠ€èƒ½
```
(The upstream install flow still works; this fork keeps OpenClaw compatibility.)

## Configure your API key

The scripts read the key in this order: `--api-key` flag â†’ `RUNNINGHUB_API_KEY`
env var â†’ OpenClaw config. For every agent except OpenClaw, set the env var:

```bash
export RUNNINGHUB_API_KEY="your-key-here"     # macOS/Linux
setx RUNNINGHUB_API_KEY "your-key-here"       # Windows
```

Verify it works:
```bash
python3 skills/runninghub/scripts/runninghub.py --check
# â†’ {"status": "ready", "balance": "...", "coins": "...", "api_type": "SHARED"}
```

## Usage

Just ask your agent in natural language. It reads the skill, presents a curated
model menu, and runs the generation:

- "Draw a puppy playing in a park"
- "Turn this photo into a video"
- "Add background music to my video"
- "Upscale this image to 4K"
- "Run this AI app https://www.runninghub.cn/ai-detail/1877265245566922800"
- "What are the hottest AI apps right now?"

## Script reference

### Standard Model API (`runninghub.py`)
| Mode | Command | Purpose |
|------|---------|---------|
| Check | `--check` | Verify key + balance |
| List | `--list [--type T] [--task T]` | Browse endpoints |
| Info | `--info ENDPOINT` | Show endpoint params |
| Run | `--endpoint EP --prompt "..." -o out.png` | Run a specific endpoint |
| Auto | `--task TASK --prompt "..." -o out.png` | Auto-pick best endpoint |

### AI Applications (`runninghub_app.py`)
| Mode | Command | Purpose |
|------|---------|---------|
| Check | `--check` | Verify key + balance |
| Browse | `--list [--sort S] [--size N] [--page N]` | Recommended / hottest / newest apps |
| Nodes | `--info WEBAPP_ID` | Show an app's modifiable nodes |
| Run | `--run WEBAPP_ID --node ... --file ... -o out.png` | Run an app |

## Repo layout

```
runninghub-agent-skills/
â”œâ”€â”€ skills/runninghub/
â”‚   â”œâ”€â”€ SKILL.md                      # Cross-agent skill definition
â”‚   â”œâ”€â”€ scripts/
â”‚   â”‚   â”œâ”€â”€ runninghub.py             # Standard Model API client (394 endpoints)
â”‚   â”‚   â”œâ”€â”€ runninghub_app.py         # AI Application client
â”‚   â”‚   â””â”€â”€ build_capabilities.py     # Regenerate capabilities.json
â”‚   â”œâ”€â”€ data/capabilities.json        # Full endpoint catalog (auto-generated)
â”‚   â””â”€â”€ references/                   # Model menus, key setup, delivery rules
â”œâ”€â”€ .qoder-plugin/plugin.json         # Qoder packaging
â”œâ”€â”€ assets/avatar.svg
â”œâ”€â”€ LICENSE                           # Apache-2.0
â””â”€â”€ README.md
```

## What changed vs upstream

- Removed OpenClaw-only metadata from `SKILL.md`; persona now matches the user's
  language instead of forcing Chinese.
- File delivery is agent-native (IDE agents reference local files; messaging agents
  use their media-send tool) instead of OpenClaw's `message` tool only.
- Output paths are cross-platform (`tempfile.gettempdir()/rh-output`) instead of
  hard-coded `/tmp/openclaw/...`.
- API-key guidance now centers on the `RUNNINGHUB_API_KEY` env var.

## License

[Apache-2.0](./LICENSE). Copyright of the original scripts belongs to their
respective authors at RunningHub; this adaptation is provided under the same license
with attribution.
