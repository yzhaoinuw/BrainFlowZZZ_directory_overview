# Work Log

Prepend new session notes to the top of this file.

## 2026-06-10

### Adopt Agent Collab Treaty: fill scaffolded docs + add badge (claude-opus-4-8, extended thinking)

- `treaty init` (v0.3.2, `gh:yzhaoinuw/agent_collab_treaty`) had scaffolded the
  treaty files in a prior session that was interrupted before any were filled in;
  all four were still raw placeholder templates. Resumed and completed the
  adoption from scratch (no prior progress to recover).
- Filled `AGENTS.md`: replaced the runtime/test/app placeholders to reflect that
  this is a docs-only repo (no env, no build, no test suite); documented the
  external weekly SLURM job as the real "runtime"; added project-specific
  reminders (public-mirror caution, load-bearing `BEGIN/END` markers,
  machine-prepended logs, snapshot tracks structure-only, automation lives in
  `$HOME`, no-crontab self-resubmit, the Haiku-no-markers gotcha).
- Rewrote `project_overview.md` to describe the index + the weekly auto-update
  pipeline (scheduler → structural diff → Haiku surgical section rewrite →
  deterministic CHANGELOG/ERROR_LOG → push), with an accurate structure map and
  active-vs-legacy guidance.
- Populated `next_steps.md` "Currently Hot" with the live weekly-auto-update
  reliability thread (carried forward from `Yue/WORK_LOG.md` 2026-05-08).
- Added the treaty "adopted" badge to `README.md` (tri-color SVG variant served
  from the treaty repo's `assets/`), linking back to the treaty repo.
- Verification:
  - `rg -n '<!-- (BEGIN|END):' README.md` — markers still balanced after the
    badge edit (badge inserted above the first marker, none disturbed).
  - Scanned the treaty docs for leftover `[...]` template placeholders — none
    remain in `AGENTS.md`, `project_overview.md`, or `next_steps.md`.

Rotation policy: the live log holds at most the **5 most recent unique calendar dates**. When a new date would push the file past 5 unique dates, move the oldest 5 dates as a chunk into a new file at `work_log_archive/work_log_<earliest>_to_<latest>.md`. The live file always holds at most 5 unique dates; each archive file always holds exactly 5.

If today's date already has a `## YYYY-MM-DD` header at the top, add a new `###` session subsection under it rather than starting a second `## YYYY-MM-DD` header for the same date.

Update this log at the end of any substantive work session unless the user explicitly asks not to document it. Substantive work includes file edits, meaningful validation or debugging, technical decisions or reversals, reusable discoveries, branch/PR/release state changes, or follow-up work that future agents need. Log useful experiments even when the code was reverted; skip casual Q&A, trivial one-off commands, and pure scratch work with no future coordination value.

<!--
Each session entry follows this shape:

## YYYY-MM-DD

### Short title for what was done (model + version, effort/thinking mode, token budget if known)

- bullet describing what was added or changed
- another bullet — keep them high-level and user/agent-facing, not implementation play-by-play
- if relevant, intended profiling signal or measurement:
  - what to look for in logs / output
  - what numbers were observed
- Verification:
  - the exact command(s) that were actually run
  - what passed / what was confirmed

Model / effort / token info goes in the parentheses after the `###` title when available from the system. Use whatever the model or interface actually reports — do not estimate or hallucinate. Omit any field that the interface does not surface.

- **Model**: the version string the interface reports (e.g. `grok-4.3`, `gpt-4o`, `claude-opus-4-7`).
- **Effort / thinking mode**: the effort knob the interface reports (e.g. `high`, `low`, `extended thinking`). Omit if no such knob exists or its setting is not surfaced.
- **Token budget**: **output tokens for the session** (output + thinking/reasoning tokens for models that report them separately, e.g. Claude with extended thinking). This is the cleanest cross-agent proxy for "amount produced." Omit if the interface does not surface a count.

Purely human-driven work can use `(human)`. Mixed human + agent sessions can combine them, e.g. `(human + grok-4.3, high)`.

Keep the parenthetical compact. Examples:
- `(grok-4.3, high, ~18k out)`
- `(gpt-4o, high, ~22k out)`
- `(claude-opus-4-7, extended thinking, ~30k out)`
- `(grok-4.3, low)`

Newest entry goes on top. If the session did multiple distinct pieces of work, use multiple `###` subsections under one `##` date header.
-->
