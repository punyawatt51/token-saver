---
name: token-saver
description: Lean working mode that cuts Claude usage and plan-limit burn. Use at the start of any coding or agent session, whenever the user mentions usage, credits, limits, tokens, "save tokens", "lean mode", or "cheap mode", or when a session is getting long.
---

# Token saver

Every reply re-sends the whole conversation (as cache reads), so cost grows with
context size × number of round trips. Most of the bill is context that gets
re-read over and over, not new text. So: keep context small, take fewer turns, and keep output short.

## 1. Fewer round trips
- Batch independent tool calls into ONE message (reads, greps, edits, checks).
- Plan the next 2–3 steps and send them together instead of one call per turn.
- Don't make a turn just to narrate, confirm, or "check in".
- Edit and then run the check in the same message when the edit is certain.

## 2. Keep context small
- Grep / glob first, then Read only the lines you need (`offset` + `limit`).
- Never re-read a file already in context; never re-read after your own Edit.
- Shell output: always cap it — `| head -50`, `| tail -30`, `grep -m 20`,
  `--quiet` / `-q` / `--silent`. Send long logs to a file and grep the file.
- Installs/builds: `npm i --silent`, `pip -q`, `--no-progress`; only show errors.
- Run the single relevant test, not the whole suite, until the end.
- Don't load skills, MCP tools, widgets or artifacts unless the task needs them.

## 3. Push wide reading out of the main context
- Searching/reading many files → one subagent with `model: "haiku"` that
  returns only the conclusion (paths + a few lines). Don't spawn one for a
  task 2–3 direct calls would solve.

## 4. Short output
- Answer first, no preamble, no recap of what the user said, no closing summary
  that repeats the diff.
- Don't paste back code you just wrote; reference `file:line`.
- Use Edit for changes; never rewrite a whole file to change a few lines.

## 5. Session hygiene (biggest lever on long work)
- One task per session. When a task is done, or context passes ~150k tokens,
  tell the user: "Good point to /clear or start a new session", and give a
  ≤5-line handoff note (goal, files touched, what's left) they can paste in.
- Prefer a fresh session over /compact for unrelated new work.

## 6. Model & effort
- Routine edits, renames, small fixes, Q&A → suggest Sonnet or Haiku and low
  effort. Keep Opus / high effort for hard design or debugging.
- Don't think at length about simple steps.

## One-time setup to suggest (if the user hasn't done it)
Tool and skill definitions ride along on every request. Disabling MCP servers
and plugins that aren't used for the current project (via `/mcp` and `/plugin`
in a terminal `claude`, or the app's connector settings) can remove tens of
thousands of tokens from *every* reply. Mention this once, not every session.
