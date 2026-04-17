# Example: CLAUDE.md using never-start-over

> This is a sample project-level `CLAUDE.md` (or `AGENTS.md`) that demonstrates
> how to wire the 3 never-start-over skills into a real project workflow.
> Adapt to your project, team, and conventions.

---

# CLAUDE.md — My Project

## Project Overview

{{Brief description of the project — what it does, what stack it uses, current status.}}

## Session Continuity

This project uses [never-start-over](https://github.com/DrDiabelsBafian/never-start-over) for cross-session memory. Three skills are available:

### Handover (end of session)
- Trigger: "handover" or "wrap up"
- Produces: `HANDOVER_{{Project}}_{{dd-mm-yy}}.md`
- **Always produce a handover at the end of a productive session**
- Previous handovers are stored in `.session/handovers/`
- Decision numbering continues across sessions (check the last handover for the current D number)

### RETEX (after AI errors)
- Trigger: "RETEX" or after 3+ failed attempts on the same problem
- Produces: `RETEX_{{Subject}}_{{dd-mm-yy}}.md`
- Stored in `.session/retex/`
- **Active vaccines from previous RETEXes are listed below**

### TODO Tracker (task extraction)
- Trigger: "TODO"
- Produces: `TODO_{{Project}}_{{YYYYMMDD}}_{{HHhMM}}.json`
- Stored in `.session/todos/`
- At session start, if a previous TODO exists, review it first: mark completed tasks, carry forward open ones

## Active Vaccines

Paste vaccine rules from previous RETEX files here. These are always-on rules.

```
VACCINE_RETEX_001:
{{paste your vaccine rules here — they are self-contained and work without context}}
```

## Quality Standards

### Before delivering any file:
1. Verify it works (don't just write code — trace the logic mentally)
2. Check edge cases (empty input, missing data, large files)
3. For files >100 lines: do a 3-eye audit (content accuracy / technical correctness / continuity with previous versions)

### Before delivering a handover:
- Can a new AI instance resume from this document alone? If not, add what's missing
- Can you identify state, last decision, and next action in 30 seconds? If not, cut the prose

### Before delivering a RETEX:
- Does every anti-pattern have a memorable name? Generic descriptions get forgotten
- Is the vaccine block self-contained? Test: read ONLY the vaccine — does it make sense?

### Before delivering a TODO:
- Re-read the entire conversation one more time — did you miss anything?
- Does every task have an owner, a priority, and enough context to be actionable?

## Working Style

- Compare 2-3 approaches before committing to an implementation
- Surgical fixes over full rewrites — never regenerate an entire file to change 3 lines
- When uncertain, search before asking — check previous files, search the web, read docs
- If you detect yourself falling into a known anti-pattern (see RETEX bank), alert immediately: "Warning: [pattern name] detected. Changing approach."

## Session Startup Checklist

When starting a new session on this project:

1. Read the latest handover in `.session/handovers/`
2. Check for active vaccines above
3. Review the latest TODO in `.session/todos/` — mark completed tasks
4. Read any relevant RETEX files if working on a previously problematic area
5. Then — and only then — ask the user what they want to work on

## File Conventions

- Session files: `.session/` directory
- Source code: `src/`
- Tests: `tests/`
- Documentation: `docs/`

{{Adapt to your actual project structure.}}
