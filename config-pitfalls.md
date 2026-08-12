# Config patch limitations

`gateway config.patch` is not universal. Several config paths are locked and must be edited directly in `~/.openclaw/openclaw.json`, followed by a full gateway restart.

## Locked paths (must edit directly)

- `agents.defaults.*` — config patch is locked for the entire defaults block
- `agents.list[].heartbeat` — heartbeat config is patch-locked per agent
- `models.providers.<provider>.models[].maxTokens` for some providers — patch may be silently ignored

## The workflow

1. Edit `~/.openclaw/openclaw.json` directly for locked paths.
2. Run `systemctl --user restart openclaw-gateway` (or `openclaw gateway restart`).
3. Verify the new PID: `ps -o pid,cmd -p <new_pid>`.
4. Hot reload does **not** re-read environment files — if you changed `gateway.systemd.env`, restart is mandatory.

## Env-file gotcha

`OPENCODE_API_KEY` in `gateway.systemd.env` overrides the config's `apiKey` exec-provider. Key rotations that only update 1Password + openclaw.json silently don't apply until you delete or update the env line and restart.

## Source

TOOLS.md OpenClaw runtime notes; multiple rotation incidents where the old key kept being used.
