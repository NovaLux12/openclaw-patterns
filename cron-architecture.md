# Cron architecture: main/systemEvent vs isolated/agentTurn

OpenClaw cron has two fundamentally different shapes. Pick the wrong one and your work sits in a queue forever.

## The two shapes

| `sessionTarget` | `payload.kind` | Behaviour | Use when |
|---|---|---|---|
| `"main"` | `"systemEvent"` | Drops a prompt into the main session | Interactive work; needs main-session attention |
| `"isolated"` | `"agentTurn"` | Spawns a sub-agent that does the work | Background checks, maintenance, "do this now" |

## The failure mode to avoid

Writing a "check if X needs updating" prompt as a `systemEvent` to main. If main is busy, the prompt just sits there. Use `isolated agentTurn` for anything that should happen unattended.

## The rule

- **Interactive or needs human attention?** `main` + `systemEvent`.
- **Should run unattended?** `isolated` + `agentTurn`.
- **Need to bind to current session?** `current` + `agentTurn`.
- **Need to target a specific session?** `session:<id>` + `agentTurn`.

## Source

AGENTS.md cron architecture reference; multiple stuck-cron incidents where `systemEvent` to main sat unprocessed for hours.
