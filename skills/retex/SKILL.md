---
name: retex
description: >
  AI Error Post-Mortem. Triggers when the user says "RETEX" or after the AI
  has failed 3+ times on the same problem. Captures WHY the AI failed and HOW
  to prevent recurrence. Produces a structured markdown file with 6 mandatory
  blocks including named anti-patterns and injectable "vaccine" rules.
  This is not a handover (project state) or an audit (quality check) — it is
  a diagnostic that produces immunity.
---

# Skill: RETEX — AI Error Post-Mortem

> **Version**: 1.0  
> **Part of**: [never-start-over](../../README.md)  
> **Companion skills**: [handover](../handover/SKILL.md) (project state), [todo-tracker](../todo-tracker/SKILL.md) (task extraction)  
> **Philosophy**: A RETEX is a vaccine, not a diagnosis. It captures WHY the AI failed and HOW to never fail the same way again.

---

## 1. Structure (6 mandatory blocks)

### Block 1 — CONTEXT (3 lines max)

What, where, why. No history, just the frame.

### Block 2 — ERROR CHRONOLOGY

Table: each failed attempt, what was done, the cognitive bias identified.

| # | Attempt | What Was Done | Why It Failed | Bias |
|---|---------|--------------|---------------|------|
| 1 | ... | ... | ... | Named bias |

Always include: **Total duration of the error cycle** and **Duration of the final solution**.

### Block 3 — ANTI-PATTERNS (named)

Each toxic reflex gets a **memorable name**. This is critical — unnamed patterns get repeated; named patterns get caught.

Format per anti-pattern:
- **Name**: short, memorable label
- **Definition**: 1 line
- **Example**: concrete, from this session
- **Warning signal**: how to detect BEFORE falling into it

### Block 4 — TIPPING POINT

The exact moment it unblocked. Always formulate as:
- **Moment**: exact quote or action
- **What the AI thought**: X
- **The real problem**: Y
- **Lesson**: 1 sentence

### Block 5 — BEST PRACTICES

Extracted rules, formulated as imperative directives (copy-pasteable into prompts).  
Format: action verb + constraint + reason.

### Block 6 — VACCINE

3 to 7 condensed sentences. Directly injectable into system prompts or AI memory.  
Each sentence = 1 autonomous rule, understandable without context.  
Format: code block with identifier `VACCINE_RETEX_{{NNN}}`.

**Numbering**: auto-increment from the last known vaccine number. If no previous RETEX exists, start at 001.

**Where to inject vaccines after generating them**:
- **CLAUDE.md / AGENTS.md** — paste into project-level instructions (always-on)
- **System prompt / AI memory** — paste for persistent cross-session rules
- **Session start** — paste manually when starting a related project
- Vaccines are designed to work without context — no need to include the full RETEX

---

## 2. Named Anti-Pattern Bank (cumulative)

These anti-patterns have been identified across multiple sessions. The AI MUST proactively detect them and ALERT the user when it recognizes itself falling into one.

### The Fly Against the Window
Repeating the same approach with varying parameters instead of questioning the approach itself.  
**Signal**: 3rd attempt in the same solution category without results → STOP, change paradigm.

### The Scribble
Hand-drawing (artisanal SVG, code from scratch) what a specialized tool does infinitely better.  
**Signal**: If you're writing coordinates/values "by feel" → STOP, find the existing source.

### The Digital Duct Tape
Layering elements (pseudo-elements, overlays, patches) on top of existing work instead of replacing it.  
**Signal**: If you're adding elements ON TOP to modify → the approach is dead.

### The Local Patch
Fixing one parameter without checking coherence with the global system.  
**Signal**: If you modify ONE parameter → re-read ALL context and verify system coherence.

### The Ignored Good Result
Getting a result that works (or almost works), then abandoning it to chase a "cleaner" solution that ends up worse.  
**Signal**: If a deliverable is "almost good" → ITERATE on it instead of starting over.

### The Technician vs The Designer
Translating a visual request into technical parameters instead of reproducing the intended visual outcome.  
**Signal**: If you're thinking in CSS parameters before thinking in visual output → step back and describe the EXPECTED RESULT in human words first.

### The Nuclear Option
Rewriting an entire file instead of making a surgical fix. The AI regenerates 200 lines to change 3, introducing new bugs in previously working code.  
**Signal**: If the fix touches more than 20% of the file → STOP, isolate the exact lines that need changing and patch only those.

### The Headfirst Dive
Jumping straight into implementation without comparing 2-3 possible approaches first. The first idea feels obvious but is rarely optimal.  
**Signal**: Before writing any code or producing any deliverable → list at least 2 approaches, compare trade-offs, then commit.

### The Amnesia Loop
Proposing a solution that was already tried and failed earlier in the same session. The AI loses track of what's been attempted and cycles back to rejected approaches.  
**Signal**: Before proposing a fix → scan the conversation for previous attempts. If it looks similar to something already tried → flag it explicitly.

### The Interrogation
Asking 5 clarifying questions instead of making a decision. The user asked for help, not for a questionnaire. When the answer is inferrable, infer it.  
**Signal**: If you're about to ask more than 1 question → pick the most likely interpretation, execute, and note your assumption.

---

## 3. Production Rules

- Re-read the ENTIRE conversation before writing the RETEX
- Every anti-pattern MUST have a memorable name, definition, example, and warning signal
- The vaccine MUST be self-contained (understandable without reading the rest of the RETEX)
- Newly identified anti-patterns get added to the cumulative bank (Section 2)
- The RETEX is delivered as a file, never as inline chat prose
- If the AI detects a known anti-pattern MID-SESSION → alert immediately: "Warning: [pattern name] detected. Change approach?"
- Mid-session detection requires loading this skill at session start (not just at RETEX time). For always-on detection, reference this skill in your CLAUDE.md or project instructions.

---

## 4. Naming Convention

```
RETEX_{{Subject}}_{{dd-mm-yy}}.md
```

---

## 5. Metadata (mandatory end block)

```yaml
uri: {{conversation link or ID}}
date: {{dd-mm-yy}}
project: {{name}}
type: RETEX
subject: {{short description}}
iterations: {{number of failed attempts}}
error_duration: {{estimate}}
solution_duration: {{estimate}}
resolution: {{what finally worked}}
anti_patterns: {{count}} ({{names}})
vaccine: VACCINE_RETEX_{{NNN}}
```

---

## 6. Verification Checklist

Before delivering the RETEX:

- [ ] Re-read the ENTIRE conversation?
- [ ] Context is 3 lines or fewer?
- [ ] Every error has a named bias?
- [ ] Anti-patterns all have warning signals?
- [ ] Tipping point is formulated as "thought X / real problem Y"?
- [ ] Vaccine is understandable WITHOUT reading the rest?
- [ ] Metadata is complete?

---

## Why This Skill Exists

AI assistants repeat the same mistakes across sessions because they have no institutional memory for errors. Every time context resets, hard-won lessons vanish. The RETEX format creates **named, searchable, injectable immunity** — anti-patterns with memorable names get caught before they cause damage, and vaccine rules can be loaded into any future session's system prompt.

The naming convention for anti-patterns is deliberate: "The Fly Against the Window" sticks in memory (human and AI) far better than "avoid repeating failed approaches." Named patterns become part of the shared vocabulary between user and AI.
