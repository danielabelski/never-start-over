---
name: session-handover
description: >
  Produces a structured 5-part handover document at the end of a complex session,
  ensuring continuity between AI instances. Triggers on "handover", "session end",
  "wrap up", or after 10+ substantive exchanges. Outputs an operational briefing
  the next AI instance can parse in 30 seconds — not a conversation summary.
---

# Skill: Session Handover

> **Version**: 1.0  
> **Part of**: [never-start-over](../../README.md)  
> **Companion skills**: [retex](../retex/SKILL.md) (error post-mortem), [todo-tracker](../todo-tracker/SKILL.md) (task extraction)

---

## 1. Mission

Produce a **continuity document** so the next AI instance can resume work with zero context loss, no re-debating of closed decisions, and no repeating mistakes already fixed.

A handover is NOT a conversation summary. Summaries are for humans who were absent. Handovers are **operational briefings** for AI instances that need to execute.

The difference: a summary says "we discussed authentication options." A handover says "D1: JWT over session cookies — stateless, scales horizontally. Rejected: OAuth-only (too complex for MVP timeline)."

---

## 2. The 5 Mandatory Sections

### Section 1: STATE

Current project situation. Factual, scannable, present tense.

Answer: *"Where are we right now?"*

Include:
- What works
- What remains to be done
- Current version of deliverables
- Blockers (if any)

Format: short paragraph or factual bullets. No narrative. No "we then proceeded to..."

### Section 2: DECISIONS (closed)

Choices made during the session + why. The goal: prevent the next instance from re-opening debates that are already settled.

| # | Decision | Rationale |
|---|----------|-----------|
| D1 | ... | ... |

Also include **rejected alternatives** if the debate was significant (1 line per rejected option + rejection reason). This is critical — without it, the next instance will propose the same rejected approaches.

If the session was purely execution with no architectural or strategic decisions: "No new decisions this session — previous decisions still apply."

### Section 3: PITFALLS (this project ONLY)

AI errors encountered during THIS session on THIS project. No generalities, no universal lessons — those belong in a [RETEX](../retex/SKILL.md).

| Context | AI Error | User Fix | Impact |
|---------|----------|----------|--------|
| ... | ... | ... | ... |

If no significant errors occurred: "No notable pitfalls this session."

### Section 4: DELIVERABLES

Every file produced. Exact name, version, path.

| File | Version | Path / Description |
|------|---------|-------------------|
| ... | ... | ... |

Include intermediate files if useful for the next session (V0 snapshots, configs).  
Mark obsolete files with ~~strikethrough~~.  
Verify version coherence — no jump from V1 to V3 without V2.

### Section 5: NEXT

Next concrete action. One line if possible.  
If multiple actions: order by priority.

---

## 3. Loading a Handover (for the next AI instance)

When a handover document is provided at the start of a session:

1. **Read the entire document before responding to anything**
2. **Treat DECISIONS as closed** — do not re-debate them unless the user explicitly reopens
3. **Check PITFALLS** — if a similar task arises, actively avoid the documented errors
4. **Reference DELIVERABLES** — know what files exist, their versions, their locations
5. **Start from NEXT** — this is your entry point, not a suggestion

If anything in the handover contradicts new user instructions, flag the conflict. Don't silently override.

---

## 4. Production Rules

**Full scan**: Re-read the ENTIRE conversation. Critical decisions often happen in the first 20% of a session — skipping early messages is the #1 cause of incomplete handovers.

**30-second rule**: The handover must be parseable in 30 seconds by an AI instance discovering the project cold. If you wrote prose, you failed.

**Numbering**: Use decision IDs (D1, D2...). These propagate across sessions — if the previous handover ended at D4, this one starts at D5. If this is the first session or no previous handover exists, start at D1.

**Tone**: Factual. Direct. Operational. Write like a military briefing, not a meeting recap. The reader is a machine, not a human who needs emotional context.

---

## 5. Template

Use the template in `assets/TEMPLATE_HANDOVER.md`. Read it BEFORE writing. Do not improvise the structure.

If the template file is unavailable, follow the 5-section structure from Section 2 exactly.

---

## 6. Naming Convention

```
HANDOVER_{{Project}}_{{dd-mm-yy}}.md
```

---

## 7. Metadata (mandatory end block)

```yaml
---
uri: {{conversation link or ID}}
date: {{dd-mm-yy}}
project: {{name}}
type: HANDOVER
reorientations: {{if AI error caused direction change, note here}}
by: AI + User
---
```

---

## 8. Workflow

When the user triggers "handover":

1. Re-read the entire conversation
2. Read `assets/TEMPLATE_HANDOVER.md`
3. Fill in all 5 sections
4. Add metadata block
5. Create file with correct naming
6. Deliver the file
7. List session files: OBSOLETE or VALID

---

## 9. Anti-Patterns

| Do NOT | Do |
|--------|-----|
| Write a chronological summary | Capture current state + closed decisions |
| Copy-paste messages | Synthesize each point in 1 line |
| List generic AI lessons | Document errors specific to THIS project |
| Forget intermediate files | List EVERYTHING produced |
| Write paragraphs of prose | Keep it scannable in 30 seconds |
| Omit the "why" behind decisions | Every D needs a rationale |
| Start decision numbering from D1 every time | Continue from the previous handover |
| Summarize the user's emotions or reactions | Stick to facts and deliverables |

---

## 10. Quality Criteria

A handover is good when it passes these 3 tests:

**Content test**: Could a new AI instance resume the project from this document alone, without reading the conversation? If not, something is missing.

**Scannability test**: Can you identify the current state, the last decision, and the next action within 30 seconds of reading? If not, it's too verbose.

**Continuity test**: Does the handover prevent the next instance from (a) re-debating a closed decision, (b) repeating a documented mistake, (c) producing a file that already exists? If any of these could still happen, the handover failed.

A quick self-check before delivering:

- [ ] STATE answers "where are we?" in under 5 lines
- [ ] Every DECISION has a rationale (not just "we decided X")
- [ ] PITFALLS are specific to this project, not generic AI advice
- [ ] DELIVERABLES list every file with exact version numbers
- [ ] NEXT is a single concrete action, not a vague direction

---

## Why This Skill Exists

Every AI session produces knowledge that vanishes when the context window resets. Decisions get re-debated. Mistakes get repeated. Files get lost. Version numbers get confused.

This skill was born from 500+ hours of real-world AI collaboration and refined through dozens of iterations. The 5-section format survived because anything less loses critical information, and anything more doesn't get read.
