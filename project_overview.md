# Project Overview

This document orients a new agent (or human collaborator) to the repo. Keep it
current — when the auto-update mechanism or the index structure changes, update
the relevant sections here.

## What This Repo Is

`BrainFlowZZZ_directory_overview` is a **documentation-only** repository: a
natural-language index of the Nedergaard lab's shared sleep-neuroscience archive
at `/gpfs/fs3/archive/dkell12_lab/BrainFlowZZZ/` (rodent EEG/EMG, LFP, fiber
photometry, two-photon imaging, CSD). Its purpose is to let a person or an agent
locate data or code in that large archive without crawling it.

The product is `README.md` (titled `DATA_OVERVIEW.md`): a lookup index with an
at-a-glance table, a topic→folder quick-lookup, folder-by-folder descriptions,
and a Viewpoint file-format reference. It is published to GitHub as
`yzhaoinuw/BrainFlowZZZ_directory_overview`.

The repo is kept current **automatically**: a weekly SLURM job diffs the archive
against a saved structural snapshot and, when something changed, asks Claude
Haiku to surgically rewrite only the affected `README.md` sections, then commits
and pushes. There is no application, build, or test suite — the "runtime" is that
external job.

## Active Runtime Path

The path here is not "user invokes an app" but "scheduled job keeps the index in
sync." The job script itself lives **outside this repo**, in `$HOME`.

### 1. Scheduler entrypoint

`~/weekly_brainflow_update.sh` (on the cluster — not checked into this repo)

- SLURM batch script on the `interactive` partition. No crontab on this cluster
  (PAM policy), so it self-resubmits a fresh job 7 days out at the end of each
  run.
- Slurm logs: `~/logs/brainflow_update_%j.{out,err}`; on error it also copies
  Claude's raw output to `~/logs/brainflow_claude_<jobid>.txt`.

### 2. Structural diff

`/gpfs/fs3/archive/dkell12_lab/BrainFlowZZZ/Yue/.brainflow_snapshot`

- Tracks directories at depth 1–2 plus root files, **sizes stripped** before
  diffing. Only structural adds/removes/renames register; content edits do not.
- If the snapshot is missing (first run), the job writes it and exits without
  calling Claude — the bootstrap fast path. If no diff, it exits at zero cost.

### 3. Surgical README update (the only Claude call)

- On a real structural diff, the job sends the diff to `claude-haiku-4-5-20251001`
  in headless mode and asks it to rewrite only the changed sections of
  `README.md`, identified by `<!-- BEGIN:tag --> / <!-- END:tag -->` markers.
- If no section needs editing, Haiku is told to emit the literal token
  `NO_UPDATES_NEEDED` and the apply step short-circuits.

### 4. Deterministic logs + publish

- `CHANGELOG.md` — prepended deterministically from the diff (no Claude tokens),
  attributing changes to experimenter folders by name, shared data as
  *shared data*.
- `ERROR_LOG.md` — prepended and pushed on failure so problems are visible
  without logging into the cluster.
- Commit + push to `yzhaoinuw/BrainFlowZZZ_directory_overview`.

## Repo Structure Map

```text
BrainFlowZZZ_directory_overview/
|- AGENTS.md            # treaty: read first
|- project_overview.md  # this file
|- next_steps.md        # in-flight threads
|- work_log.md          # session history (+ work_log_archive/)
|- work_log_archive/
|- README.md            # THE PRODUCT: the data overview / index (== DATA_OVERVIEW.md)
|- CHANGELOG.md         # machine-prepended weekly change attribution
|- ERROR_LOG.md         # machine-prepended weekly job errors
|- .copier-answers.yml  # treaty (copier) template state
|- .claude/             # local Claude Code settings

# Lives OUTSIDE this repo (in $HOME / parent dir), but drives it:
~/weekly_brainflow_update.sh                 # the weekly job
~/.claude_env                                # API key (chmod 600)
~/logs/brainflow_*                           # per-run logs
.../Yue/.brainflow_snapshot                  # structural snapshot
.../Yue/WORK_LOG.md, CLAUDE.md               # broader Yue-dir history & guidance
```

## What Looks Active vs. Legacy

### Active / relevant now

- [`README.md`](README.md) — the live index; hand-edit this, preserving markers.
- [`CHANGELOG.md`](CHANGELOG.md) / [`ERROR_LOG.md`](ERROR_LOG.md) — owned by the
  weekly job; treat as append-only machine output.
- `~/weekly_brainflow_update.sh` — the automation (outside the repo).

### Likely older or secondary

- Nothing legacy *inside* this repo — it's young and small. For broader history,
  the parent `Yue/WORK_LOG.md` records how the index and weekly job were built;
  `Yue/CLAUDE.md` holds cluster/environment guidance. These are reference, not
  edited as part of normal work here.

## Tests And Fixtures

There is no test suite and no fixtures. Verification of a docs change is:
Markdown renders, links resolve, and the `BEGIN:`/`END:` markers stay balanced
(`rg -n '<!-- (BEGIN|END):' README.md`). The closest thing to an integration
test is forcing a weekly run (delete `.brainflow_snapshot`, `sbatch` the job)
and confirming it updates `README.md`/`CHANGELOG.md` and pushes.

## User Data Expectations

This repo does not consume user-supplied data files. Its *input* is the directory
structure of `/gpfs/fs3/archive/dkell12_lab/BrainFlowZZZ/` itself, summarized in
`.brainflow_snapshot`. The documented Viewpoint recording formats (`.exp`,
`.xls`, `.bin`, `.H`, `.act`, `.avi`) are described in the file-format reference
section of `README.md` for the benefit of readers, not parsed by anything here.

## Practical Mental Model

To understand this repo, read in this order:

1. [`README.md`](README.md) — what the index actually documents.
2. [`AGENTS.md`](AGENTS.md) — how to work here and the gotchas.
3. This file — the auto-update mechanism.
4. `~/weekly_brainflow_update.sh` — the job, if you need to change its behavior.
5. [`next_steps.md`](next_steps.md) — what's currently in flight.

## Questions Worth Clarifying Later

- Whether the recurring "Haiku emits prose instead of `NO_UPDATES_NEEDED`"
  failure mode is now fully resolved by the v0.3-era prompt/fast-path fixes, or
  still needs a guard in the apply step (see `next_steps.md`).
- Whether the self-resubmitting SLURM chain is the long-term plan, or whether a
  more durable scheduler should replace it if cluster policy ever allows.
