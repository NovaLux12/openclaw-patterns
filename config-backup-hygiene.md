# Config backup hygiene: dated directories, not `.bak` litter

Before changing a live config file, put the previous version somewhere deliberate. Ad-hoc `.bak` files next to the live config are worse than nothing, because they look like a safety net.

## The anti-pattern

```
~/.openclaw/openclaw.json
~/.openclaw/openclaw.json.bak-merge
~/.openclaw/openclaw.json.bak.20260826-125048
~/.openclaw/openclaw.json.bak-final-FIXED
```

Problems with this shape:

- **Ambiguous restore point.** When a change goes wrong, which `.bak` is the pre-change state? Suffixes like `-merge` or `-final` describe intent at write time, not content.
- **Commit leakage.** Broad staging (`git add -A`) sweeps `.bak` files into commits. Several of these configs live in git-tracked workspaces; a stray backup is one careless stage away from a public repo.
- **Root litter.** Backups scattered beside the live file accumulate forever, because nothing owns their cleanup.

## The pattern

One backup location, one naming rule:

```
backups/config-history/<YYYY-MM-DD>/<filename>.pre-<change-description>
```

- **Before** any batch edit to a live config: copy the current file there.
- **Name by what changed**: `openclaw.json.pre-umans-fallback-fix`, not `copy of openclaw.json`.
- **Same commit discipline as everything else**: never stage backup directories; if a temp backup was made in `/tmp` or beside the live file, remove it once the dated copy exists.
- **Date-partitioned** directories make "the state before last Tuesday's provider migration" a single `ls` away, and make periodic cleanup trivial.

The extra ten seconds of `mkdir -p` + `cp` pays for itself the first time a config edit has to be unwound — and it removes the temptation to keep "just in case" copies cluttering the live directory permanently.

## Source

AGENTS.md config-hygiene rule (2026-08-23); provider-reconfig sessions on 2026-08-26 that produced both correct dated backups (`backups/config-history/2026-08-26/openclaw.json.pre-...`) and root-level `.bak` litter that later had to be kept out of commits by hand-staging.
