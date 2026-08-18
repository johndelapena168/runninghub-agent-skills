# Output & Delivery

## Progress Notification (for slow tasks)

For video, AI app, 3D, and music generation: **ALWAYS tell the user the task has
started BEFORE running the script.** These tasks take 1-10+ minutes.

> "开始生成啦，视频一般需要几分钟，请稍等～ 🎬" (or the equivalent in the user's language)

For fast tasks (text-to-image, image upscale, TTS), notification is optional.

## Media (image/video/audio/3D)

The script prints `OUTPUT_FILE:/path` and optionally `COST:¥X.XX`.

**Deliver the saved local file via your agent's native mechanism:**

- **IDE agents (Claude Code, Cursor, Qoder):** reference the local file in your
  reply so the user can open it — e.g. a markdown image for images
  `![result](/path/to/out.png)`, or a file link for video/audio/3D.
- **Messaging agents (OpenClaw, Slack/Feishu bots):** call your `message` /
  media-send tool with the local file path; do NOT just print the path as text.

**NEVER do these**:
- Show `runninghub.cn` task/CDN URLs as the deliverable (they expire / are internal)
- Print a raw file path as the *only* delivery when your agent can send/preview files
- Claim "sent" without actually delivering the file

## Text Results

Print the text directly to the user. Include cost if a `COST:` line is present.

## Errors & Retry

| Error | Action |
|-------|--------|
| `NO_API_KEY` | Guide key setup → Read `{baseDir}/references/api-key-setup.md` |
| `AUTH_FAILED` | Key expired → https://www.runninghub.cn/enterprise-api/sharedApi |
| `INSUFFICIENT_BALANCE` | "Balance is low～" → https://www.runninghub.cn/vip-rights/4 |
| `TASK_FAILED` | For video: offer a fallback model. For others: friendly error + offer retry. |

## General Notes

- Video is slow (1-5 min); the script auto-polls up to 20 min.
- Images < 5MB → base64; larger → upload first.
- Key resolution order: `--api-key` flag → `RUNNINGHUB_API_KEY` env → OpenClaw config.
- Default output dir (when `-o` omitted) is the system temp dir's `rh-output/`.
