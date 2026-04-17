<div align="center">

# never-start-over

### Your AI forgets everything. These 3 skills make it remember.

**Handover · Error post-mortem · Task extraction.**

[![Agent Skill](https://img.shields.io/badge/format-SKILL.md-blue.svg)]()
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/skills-3-orange.svg)]()
[![Compatible](https://img.shields.io/badge/works%20with-Claude%20Code%20%7C%20Codex%20%7C%20Gemini%20CLI%20%7C%20Cursor-lightgrey.svg)]()

[The Problem](#-the-problem) · [The 3 Skills](#-the-3-skills) · [Install](#-install) · [How They Work Together](#-how-they-work-together) · [Examples](#-real-examples)

</div>

---

## The Problem

AI sessions are stateless. Every time the context window resets, you lose:

- **Decisions** — re-debated from scratch
- **Mistakes** — repeated because the AI has no memory of what failed
- **Tasks** — scattered across the conversation, half of them forgotten
- **Files** — version confusion, "which draft was final?"

You end up spending the first 15 minutes of every session re-explaining where you left off. That's not collaboration. That's Groundhog Day.

## The 3 Skills

This kit solves session continuity with 3 complementary skills. Each handles a different type of knowledge that gets lost:

| Skill | What it captures | When to use |
|-------|-----------------|-------------|
| **[Handover](skills/handover/SKILL.md)** | Project state, closed decisions, deliverables, next action | End of every productive session |
| **[RETEX](skills/retex/SKILL.md)** | Why the AI failed, named anti-patterns, injectable vaccine rules | After the AI spun its wheels (3+ failed attempts) |
| **[TODO Tracker](skills/todo-tracker/SKILL.md)** | Tasks, blockers, open questions, attack order | When you need a clean task list from a messy conversation |

**Used alone, each skill is useful. Used together, they create a feedback loop:**

```
Session N                          Session N+1
────────                          ──────────
Work happens                      Load HANDOVER → know where we are
AI makes mistakes                 Load RETEX → know what NOT to do  
Tasks accumulate                  Load TODO → know what to do next
    │                                 │
    ▼                                 ▼
HANDOVER (state)  ──────────────► Resume instantly
RETEX (errors)    ──────────────► Avoid past mistakes
TODO (tasks)      ──────────────► Clear priorities
```

## Install

### Claude Code

```bash
# Install all 3 skills globally
mkdir -p ~/.claude/skills/session-handover
mkdir -p ~/.claude/skills/retex  
mkdir -p ~/.claude/skills/todo-tracker

cp skills/handover/SKILL.md ~/.claude/skills/session-handover/
cp -r skills/handover/assets ~/.claude/skills/session-handover/
cp skills/retex/SKILL.md ~/.claude/skills/retex/
cp skills/todo-tracker/SKILL.md ~/.claude/skills/todo-tracker/
```

### Project-level (any agent)

```bash
# Drop into your project
cp -r skills/ .claude/skills/
```

### Codex / Gemini CLI / Cursor

Same SKILL.md format — copy into the agent's skill directory. See your agent's documentation for the exact path.

### Claude.ai (web interface)

Upload skill files as custom user skills via Settings.

### Manual use (any LLM)

Copy the template from `skills/handover/assets/TEMPLATE_HANDOVER.md` and paste it at the end of your session. Ask the AI to fill it in. Works with any model.

## How They Work Together

### End of a normal session → **Handover**

Say `"handover"` and the AI produces a structured 5-part briefing: state, decisions, pitfalls, deliverables, next action.

### End of a frustrating session → **Handover + RETEX**

If the AI struggled, also say `"RETEX"`. You get the handover (what happened) PLUS a post-mortem with named anti-patterns and injectable vaccine rules you can load into future sessions.

### Need a task list → **TODO**

Say `"TODO"` and the AI scans the entire conversation with a 6-category checklist (explicit tasks, decisions needing follow-up, open questions, unresolved topics, blockers, new discoveries) and outputs a prioritized JSON + human-readable summary.

### Starting a new session → **Load previous handover**

Paste the handover from your last session. The AI reads it, treats decisions as closed, checks pitfalls, and starts from the NEXT action. No re-explaining.

## Real Examples

See the [examples/](examples/) folder for anonymized, real-world outputs:

- **[HANDOVER example](examples/HANDOVER_example.md)** — Dashboard monitoring project, WebSocket architecture decisions, memory leak pitfall
- **[RETEX example](examples/RETEX_example.md)** — SVG badge generator, 4 failed iterations, 2 named anti-patterns, vaccine rules
- **[TODO example](examples/TODO_example.json)** — Prioritized task extraction with attack order, open questions, and cross-session tracking
- **[CLAUDE.md example](examples/CLAUDE_MD_example.md)** — Sample project config that wires all 3 skills together with quality standards and session startup checklist

## What Makes This Different

**Named anti-patterns.** Most error-tracking is generic ("be more careful"). The RETEX skill forces the AI to name each failure pattern ("The Fly Against the Window", "The Digital Duct Tape"). Named patterns get caught. Unnamed patterns get repeated.

**Closed decisions.** The handover explicitly marks decisions as closed and documents rejected alternatives. The next AI instance won't propose the same rejected approach.

**Injectable vaccines.** RETEX produces `VACCINE_RETEX_NNN` code blocks — self-contained rules you can paste into any system prompt or AI memory. They work without context.

**Attack order.** The TODO tracker doesn't just list tasks — it sequences them. "Do this first, then this, then this." Flat lists don't get executed. Ordered lists do.

**30-second rule.** Every handover must be parseable in 30 seconds by an AI instance that has never seen the project. If it takes longer to read than to re-explain, it failed.

**CLAUDE.md integration.** The kit includes a sample project config ([example](examples/CLAUDE_MD_example.md)) that wires all 3 skills together with quality standards, session startup checklists, and active vaccine loading. Drop it in and the entire system works automatically.

## File Structure

```
never-start-over/
├── README.md
├── LICENSE
├── skills/
│   ├── handover/
│   │   ├── SKILL.md
│   │   └── assets/
│   │       └── TEMPLATE_HANDOVER.md
│   ├── retex/
│   │   └── SKILL.md
│   └── todo-tracker/
│       └── SKILL.md
└── examples/
    ├── HANDOVER_example.md
    ├── RETEX_example.md
    ├── TODO_example.json
    └── CLAUDE_MD_example.md
```

## FAQ

**Do I need all 3 skills?**  
No. The handover alone covers 80% of the continuity problem. Add RETEX when debugging sessions waste your time, and TODO when conversations get complex.

**Does this work with models other than Claude?**  
Yes. The SKILL.md format is an open standard. The skills work with any agent that supports it (Claude Code, Codex, Gemini CLI, Cursor, Copilot). The templates work with any LLM by copy-pasting.

**How is this different from CLAUDE.md / AGENTS.md?**  
CLAUDE.md defines project-wide conventions (always-on context). Skills are modular behaviors invoked on demand. They complement each other — see [CLAUDE_MD_example.md](examples/CLAUDE_MD_example.md) for how to wire them together.

**Can I customize the anti-pattern bank?**  
Yes. The RETEX skill's anti-pattern bank is designed to grow. Each time you run a RETEX, new patterns get added to your local copy. Over time, your bank becomes a personalized encyclopedia of things your AI gets wrong.

## Contributing

PRs welcome. The quality bar: every change must make a skill **more scannable**, not longer. If your improvement adds 10 lines, it better save 30 seconds of reading time.

## Origin

Built by [DrDiabelsBafian](https://github.com/DrDiabelsBafian) through 500+ hours of real-world AI collaboration — not generated from a template. The formats survived because they work: anything less loses critical information, anything more doesn't get read. The named anti-patterns ("The Fly Against the Window", "The Scribble", "The Digital Duct Tape") emerged from actual debugging sessions where the AI kept making the same mistakes until the patterns were named and documented.

Questions or ideas? → diabels.bafian@proton.me

## License

MIT — use it, fork it, adapt it, sell it. Just keep the attribution.

---

<div align="center">

**If this kit saved you from re-explaining your project, consider starring the repo.**

*Built by [DrDiabelsBafian](https://github.com/DrDiabelsBafian). Battle-tested, not theoretical.*

</div>
