# Guidelines and Tips for Agents

This file is the first thing any agent (Claude, Codex, or other) should read when joining a session on this repo. It defines the runtime, the common tasks, the conventions, and the project-specific reminders. Keep it short and current.

## Startup Rule

At the beginning of a new chat or agent session for this project, read this file first and do not automatically read every markdown file in the repository. Use the [Documentation](#documentation) map below to decide which other files are relevant to the current task.

## Runtime Environment

This repository is **documentation-only** — there is no application to launch, no
build step, and no automated test suite. The "code" is Markdown: a
natural-language index of the lab archive plus auto-maintained change/error logs.

There is therefore no conda env or virtualenv to activate for editing the docs.
Plain text tools (an editor, `rg`, `git`) are all that's needed.

The one piece of automation that *acts on* this repo runs **outside** it: a SLURM
batch script at `~/weekly_brainflow_update.sh` on the cluster. It is not invoked
from a checkout — see "Common Tasks" for how it works and how to exercise it.

## Common Tasks

Short recipes for the things you'll usually do in a session.

Edit the data index — almost all work is hand-editing `README.md` (the data
overview). Keep the `<!-- BEGIN:tag --> / <!-- END:tag -->` section markers
intact: the weekly auto-update relies on them to do surgical, token-efficient
section rewrites.

Inspect or exercise the weekly auto-update job (runs on the cluster, not here):

```
# View the script and its current state
cat ~/weekly_brainflow_update.sh

# Force a run for testing: deleting the snapshot makes the next run treat the
# whole archive as "new", then submit the batch job
rm -f /gpfs/fs3/archive/dkell12_lab/BrainFlowZZZ/Yue/.brainflow_snapshot
sbatch ~/weekly_brainflow_update.sh

# Full per-run output lands here (see ERROR_LOG.md):
#   ~/logs/brainflow_update_<jobid>.out
#   ~/logs/brainflow_claude_<jobid>.txt   (Claude raw output, kept only on error)
```

There is no test runner. "Verification" for a docs change means: the Markdown
renders correctly, links resolve, and the section markers are still balanced.
Check the markers with:

```
rg -n '<!-- (BEGIN|END):' README.md
```

Pre-flight checklist before committing:

- `README.md` still has matching `BEGIN:`/`END:` markers (the command above —
  every `BEGIN` has its `END`).
- Internal links and folder paths referenced in the docs still resolve.
- A new entry has been prepended to `work_log.md` describing what was done
  (including model + version, effort/thinking mode, and token budget if
  available) and the verification commands that were actually run.

## When To Update Treaty Docs

At the end of any substantive work session, update `work_log.md` unless the user explicitly asks not to document it, says it is off the book, or the exchange was clearly trivial.

A session is substantive when it includes any of:

- file edits
- meaningful validation, debugging, profiling, or artifact inspection
- a technical decision or reversal
- discovered evidence future agents should not have to rediscover
- branch, PR, release, deployment, or environment state changes
- unfinished follow-up that belongs in `next_steps.md`

No work-log entry is usually needed for casual Q&A, explanation-only exchanges with no lasting project state, or tiny one-off commands with no future coordination value.

Log experiments when they produce reusable evidence, a decision, or a warning for future agents, even if the code change is reverted. Skip pure scratch work when it has no future coordination value or the user wants it omitted.

When a session creates or changes future work, update `next_steps.md` in the same pass: add concrete follow-ups, remove completed items, and keep "Currently Hot" accurate.

## Branch Handoff Discipline

Before switching away from an experimental or feature branch, fully resolve the work on that branch. Confirm whether the branch contains all intended changes, whether those changes are committed, and whether the user expects them merged, pushed, or intentionally left parked.

Do not switch to the integration branch (`main`) or start new work on another branch while important experimental-branch changes are only local, unmerged, or unverified. If related work accidentally lands on the integration branch, move that work back onto the experimental branch first and retest the combined behavior there before updating the integration branch.

Useful checks before switching or merging (portable git commands; run in any shell):

```
git status --short --branch
git log --oneline --left-right --cherry-pick main...HEAD
git merge-base --is-ancestor main HEAD
```

## Documentation

Read these documents only as needed. The map below names each file and when it's worth opening.

- `work_log.md` and `work_log_archive/`
  - Use when the task needs recent implementation history, experiment outcomes, or verification breadcrumbs.
  - The live `work_log.md` holds at most the 5 most recent unique calendar dates. Default to reading only the two most recent dated entries.
  - Find date anchors with ripgrep and read only the slice you need:
    `rg -n '^## [0-9]{4}-[0-9]{2}-[0-9]{2}' work_log.md`
  - When older context is needed, open the matching file under `work_log_archive/` by its date-range filename, or grep across both at once:
    `rg -n '^## [0-9]{4}-[0-9]{2}-[0-9]{2}' work_log.md work_log_archive/`
  - When prepending a dated entry, if today's calendar date already has a `## YYYY-MM-DD` header at the top, add a new `###` session subsection under it. Do not start a second `## YYYY-MM-DD` header for the same date.
  - When prepending a new date would push the live log past 5 unique calendar dates, move the oldest 5 dates as a chunk into a new file at `work_log_archive/work_log_<earliest>_to_<latest>.md`. The live file always holds at most 5 unique dates; each archive file always holds exactly 5.

- `next_steps.md`
  - Use when planning or continuing unfinished work from previous sessions.
  - The "Currently Hot" pointer at the top names the active threads — read it first to know what's in flight.
  - Remove items after they are completed. Add new planned follow-ups when they become concrete.

- `project_overview.md`
  - Use when onboarding to the codebase structure or when a task touches an unfamiliar area.
  - The "What Looks Active vs. Legacy" section is the single most important map before editing — many repos accumulate parallel implementations, and this section keeps an agent from editing the wrong file.

- `README.md`
  - Use when changing user-facing setup, packaging, usage, or input-file expectations.

- Treaty badge (in your README)
  - `treaty init` offers (opt-in) a centrally-hosted "Agent Collab Treaty - adopted" badge (Codex blue / Claude amber / Grok dark tri-color SVG, or reliable single-color shields.io). It is a pure visibility signal that links back to this treaty repository. No asset files are added to your project, and the image updates automatically if the design improves later. The badge is fully optional. The shields.io version is the dependable recommendation for GitHub READMEs.

- `CONTRIBUTING.md` (if present)
  - Use when changing collaboration workflow, branch/test expectations, or documentation conventions.

The same anchor-grep pattern works for any structured Markdown doc in the repo — `grep -n '^## ' <file>` for the section map, then a targeted slice read rather than loading the whole file.

## Git Ownership Note

If Git reports a "detected dubious ownership" warning for this repo, mark this repository as safe.

Windows (PowerShell):

```powershell
git config --global --add safe.directory C:/path/to/this/repo
```

macOS / Linux:

```bash
git config --global --add safe.directory "$HOME/path/to/this/repo"
```

This is the preferred fix unless the repository ownership itself needs to be changed at the OS level.

## Pre-commit Note

If your stack uses [pre-commit](https://pre-commit.com) and it cannot write to its default cache location, set a repo-local cache before running it.

Windows (PowerShell):

```powershell
$env:PRE_COMMIT_HOME = "C:\path\to\this\repo\.pre-commit-cache"
python -m pre_commit run --all-files
```

macOS / Linux:

```bash
export PRE_COMMIT_HOME="$PWD/.pre-commit-cache"
python -m pre_commit run --all-files
```

Adjust the formatter / linter invocation for your stack (e.g., `npx lint-staged`, `cargo fmt`, `gofmt -l .`).

## Commit Message Guidelines

Commit messages should use:

- a short title line
- a short body with flat bullet points for additional requested changes when a commit contains multiple user-requested updates

Commit message bullets should describe high-level added or changed behavior, not implementation details.

For feature commits, mention only the user-facing behavior that was added or changed.

Do not mention tests, docs, project memory updates, or behind-the-scenes implementation details in a feature commit message unless that internal work is itself the main purpose of the commit.

## Project-Specific Reminders

Add domain-specific gotchas here. These are the things that aren't obvious from reading the code and that an agent would otherwise have to learn the hard way.

- This repo is a *public mirror* of an index. It is pushed to GitHub
  (`yzhaoinuw/BrainFlowZZZ_directory_overview`) by the weekly job. Do not put
  anything in `README.md`, `CHANGELOG.md`, or `ERROR_LOG.md` that shouldn't be
  public — they leave the cluster.
- `README.md` is titled `DATA_OVERVIEW.md` and *is* the product. The
  `<!-- BEGIN:tag --> / <!-- END:tag -->` markers are load-bearing: the weekly
  Haiku update edits only the section between a matching pair. Never delete or
  rename a marker, and never let them get unbalanced.
- `CHANGELOG.md` and `ERROR_LOG.md` are **machine-prepended** by the weekly job
  (newest entry on top). Edit their format only if you also update
  `~/weekly_brainflow_update.sh` to match; otherwise the next run's appended
  entry will clash.
- The structural snapshot (`Yue/.brainflow_snapshot`) tracks only directories at
  depth 1–2 and root files, with sizes stripped before diffing. Editing file
  *contents* or growing a recording does **not** trigger a Claude update — only
  structural adds/removes/renames do.
- The update script, snapshot, API key (`~/.claude_env`), and run logs all live
  in `$HOME` (`/gpfs/fs1/home/yzhao38/`), **not** in this repo. A checkout alone
  does not contain the automation.
- No crontab on this cluster (PAM policy) — the weekly job keeps itself alive by
  self-resubmitting a SLURM job 7 days out. If the chain ever dies, it must be
  restarted by hand with `sbatch ~/weekly_brainflow_update.sh`.
- Known live issue: when Haiku decides no documented section needs updating it
  may emit prose instead of the `NO_UPDATES_NEEDED` token, which the apply step
  logs as an error. See `next_steps.md`.
