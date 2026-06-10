# Next Steps

Use this checklist alongside `work_log.md`.

## Currently Hot

Active threads — read these first to know what work is in flight:

- **Weekly auto-update reliability** — confirm the patched `~/weekly_brainflow_update.sh`
  produces correct marker-wrapped output on a *real* structural diff (it has only
  been exercised on the no-diff / bootstrap paths so far). Next action: inspect
  the next scheduled run's logs.

When an agent (or human) creates or significantly updates a thread/plan here,
include model + version, effort/thinking mode, and token budget (if known) in
parentheses after the thread name.

## Weekly auto-update reliability (claude-opus-4-7-era diagnosis; see Yue/WORK_LOG.md 2026-05-08)

Status: in progress (patched, awaiting a real-diff confirmation run)

Framing: the weekly job failed silently three weeks running (Apr 22 / 29,
May 6) and again surfaced a no-markers error (May 8, May 22). Root cause: when
Haiku judges that no documented section needs updating, it returns prose instead
of marker-wrapped section blocks, so the apply step finds zero
`<!-- BEGIN:tag -->` matches and logs it as a failure. Three compounding
diagnostic bugs (discarded Slurm logs, cleanup deleting Claude's raw output,
empty-stdout treated as error) were also fixed.

Patches already applied to `~/weekly_brainflow_update.sh`:

- Slurm logs now persist to `~/logs/brainflow_update_%j.{out,err}`.
- `cleanup()` copies Claude's raw output to `~/logs/brainflow_claude_<jobid>.txt`
  when errors occurred.
- Apply step distinguishes "errored" vs "no markers found" and includes a
  200-char preview in the pushed error message.
- First-run / missing-snapshot path writes the snapshot and exits without
  calling Claude (kills the bootstrap failure mode).
- Prompt instructs Haiku to emit the literal `NO_UPDATES_NEEDED` token when no
  section needs editing; apply step short-circuits cleanly on it.

Remaining work:

- Verify a run with a genuine structural diff yields correct marker-wrapped
  output from Haiku and a clean push. Until then this is "fixed in theory."
- Check the next scheduled run's `~/logs/brainflow_update_<jobid>.out`. If a real
  diff hasn't landed, force one (delete `.brainflow_snapshot`, `sbatch` the job)
  to exercise the Claude-update path end-to-end.

## Background / Paused

Sections below this line are older threads kept for context.

(none yet)
