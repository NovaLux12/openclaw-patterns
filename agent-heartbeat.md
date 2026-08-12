# Agent heartbeat: the "only those agents run" gotcha

Heartbeats in OpenClaw are opt-in per agent, but the opt-in is inverted.

## The gotcha

If **any** agent has a `heartbeat` block in `agents.list[]`, **only those agents** run heartbeats. To scope heartbeat to main only, you must set a block on **both**:

```json
{
  "agents": {
    "list": [
      { "id": "main", "heartbeat": {} },
      { "id": "work-agent", "heartbeat": { "every": "0m" } }
    ]
  }
}
```

Disabling only one agent flips main OFF too — which kills BG checks and other heartbeat-dependent work.

## The pattern

- Main needs heartbeats → `main: {}` (inherit defaults)
- Other agents should not → `other: {"every": "0m"}` (explicitly disabled)
- Both must be present; omitting the disabled agent re-enables heartbeats for everyone.

## Why it matters

Heartbeats drive Nightscout BG checks, email polling, calendar checks, and other standing work. An accidental heartbeat disable on main is silent until you notice the checks aren't running.

## Source

AGENTS.md heartbeat rules; TOOLS.md heartbeat gotcha (2026-08-12 verified).
