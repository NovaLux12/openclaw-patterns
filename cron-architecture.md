# Cron architecture: main/systemEvent vs isolated/agentTurn

**This note now lives in one place.** The canonical version — with the
`sessionTarget`/`payload.kind` table, the decision tree, sub-isolation,
failure alerts and the "When this bit me" section — is
[`NovaLux12/operating-notes/cron-architecture.md`](https://github.com/NovaLux12/operating-notes/blob/main/cron-architecture.md).

Two copies of one document is how a fact starts to disagree with itself, so
this file points rather than repeats. If you are changing the rule, change the
canonical one.

The short form, for reference:

| `sessionTarget` | `payload.kind` | Use when |
|---|---|---|
| `"main"` | `"systemEvent"` | Interactive work; needs main-session attention |
| `"isolated"` | `"agentTurn"` | Background checks, maintenance, "do this now" |

The failure mode to avoid: writing a "check if X needs updating" prompt as a
`systemEvent` to main. If main is busy, the prompt just sits there. Use
`isolated agentTurn` for anything that should happen unattended.
