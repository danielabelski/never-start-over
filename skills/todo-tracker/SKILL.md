---
name: todo-tracker
description: >
  Automatic extraction of tasks, decisions, open questions, and blockers at the
  end of each session. Triggers on "TODO", "to do", or alongside a handover.
  Scans the entire conversation using a 6-category checklist to ensure nothing
  is missed. Outputs a human-readable summary + structured JSON.
---

# Skill: TODO Tracker

> **Version**: 1.0  
> **Part of**: [never-start-over](../../README.md)  
> **Companion skills**: [handover](../handover/SKILL.md) (project state), [retex](../retex/SKILL.md) (error post-mortem)

---

## 1. Mission

When the user asks for a TODO:

1. Scan the ENTIRE conversation (not just the last 10 messages)
2. Extract EVERYTHING that deserves tracking
3. Generate a human-readable summary + structured JSON
4. Assign ownership, priority, and attack order

---

## 2. Extraction Checklist (anti-forgetting)

The AI MUST review these 6 categories:

> **Note**: Trigger phrases below are in English. Adapt to the conversation's language — the patterns matter, not the exact words.

### A. EXPLICIT TASKS

Trigger phrases:
- "We need to...", "I'll...", "You should...", "Next step..."
- "Create...", "Modify...", "Test...", "Verify..."
- "Don't forget to..."

### B. DECISIONS MADE

Trigger phrases:
- "OK let's go with...", "Validated", "We'll keep..."
- "Option X wins", "Final answer..."
- Any explicit commitment to an approach

→ Becomes a task IF it requires a follow-up action.

### C. OPEN QUESTIONS

Trigger phrases:
- "To be verified...", "To be confirmed..."
- "I'm not sure yet...", "Need to check..."
- "?" without a clear answer in the conversation

### D. MENTIONED BUT UNRESOLVED TOPICS

Indicators:
- Topic mentioned then conversation drifted
- "We'll come back to this", "Worth exploring"
- Topic without an explicit conclusion

### E. BLOCKERS

Trigger phrases:
- "Doesn't work", "Bug", "Error"
- "Blocked by...", "Waiting for..."
- "Can't move forward without..."

### F. INSIGHTS / DISCOVERIES

Trigger phrases:
- "I just found out...", "Actually..."
- "Good news", "Bad news"
- New information that changes the game

---

## 3. Ownership

| Tag | Meaning | Example |
|-----|---------|---------|
| `owner: user` | User handles it | "Test the script on mobile" |
| `owner: ai` | AI handles next session | "Code the dashboard" |
| `owner: external` | Depends on a third party | "Wait for IT response" |
| `owner: both` | Collaboration | "Validate specs together" |

---

## 4. Priorities

| Level | Label | Criteria |
|-------|-------|----------|
| P0 | Critical | Blocking, urgent, deadline approaching |
| P1 | Important | Necessary but not urgent |
| P2 | Normal | Do when possible |
| P3 | Nice-to-have | Optional, improvement |

Priority signals:
- "ASAP", "urgent", "critical" → P0
- "This week", "Soon" → P1
- "When you can", "At some point" → P2
- "Would be nice", "Bonus" → P3

---

## 5. JSON Schema

```json
{
  "project": "PROJECT_NAME",
  "generated_at": "2026-04-17T18:30:00",
  "session_uri": "conversation-link-or-id",
  "summary": "2-3 sentence session summary",
  "tasks": [
    {
      "id": "PROJECT-001",
      "text": "Clear task description",
      "priority": "P0",
      "status": "open",
      "owner": "user",
      "category": "action",
      "tags": ["tag1", "tag2"],
      "context": "Why this task exists (1 line)",
      "deadline": null,
      "created_at": "2026-04-17",
      "done_at": null
    }
  ],
  "decisions": [
    {
      "text": "Going with local Python server",
      "reason": "Enables dynamic folder scanning"
    }
  ],
  "open_questions": [
    "How to handle merge conflicts?"
  ],
  "attack_order": ["PROJECT-001", "PROJECT-003", "PROJECT-002"],
  "next_session_focus": "Code the server + dashboard"
}
```

---

## 6. Task Categories

| Category | Description |
|----------|-------------|
| `action` | Concrete task to execute |
| `decision` | Decision requiring follow-up |
| `question` | Question to resolve |
| `blocker` | Blocking problem |
| `insight` | Discovery to retain |
| `idea` | Idea to explore later |

---

## 7. Naming Convention

```
TODO_{{PROJECT}}_{{YYYYMMDD}}_{{HHhMM}}.json
```

---

## 8. Storage

Save the JSON wherever your project stores session data. Suggested locations:
- `.session/` directory in project root
- A dedicated `todos/` folder in your knowledge base
- Same directory as your HANDOVER files for co-location

---

## 9. Cross-Session Review

At the start of a new session, if a previous TODO exists:

1. Load the previous TODO JSON
2. Review each task with the user: done, still open, or dropped
3. Mark completed tasks (`"status": "done"`, `"done_at": "date"`)
4. Carry forward open tasks into the new TODO
5. This prevents task drift — things that were "P1 important" don't silently disappear

---

## 10. Output Format

When the user triggers "TODO":

### 1. Human summary (3-5 lines)
What was done, key decisions, next steps.

### 2. Extracted tasks (readable table)

| # | Task | Priority | Owner |
|---|------|----------|-------|
| 1 | ... | P0 | user |

### 3. Open questions
List if applicable.

### 4. Attack order
Recommended sequence for tackling the tasks.

### 5. Full JSON
Code block with JSON ready to save.

---

## 11. Anti-Patterns (do NOT extract)

| Pattern | Why |
|---------|-----|
| "We should have..." | Past, not actionable |
| "It would be nice to..." (vague) | Too fuzzy — ask for precision |
| "I did X" | Already done |
| Off-topic conversation | Not project-related |
| Jokes, digressions | Not relevant |

---

## 12. Verification Checklist

Before generating the JSON:

- [ ] Re-read the ENTIRE conversation?
- [ ] Captured explicit tasks?
- [ ] Captured decisions made?
- [ ] Any unanswered questions?
- [ ] Any topics mentioned then dropped?
- [ ] Any blockers mentioned?
- [ ] Every task assigned an owner?
- [ ] Priorities assigned?
- [ ] Attack order is logical?

---

## 13. Quality Criteria

A TODO extraction is good when it passes these tests:

**Completeness test**: Read through the conversation one more time after generating the TODO. Did you miss any action item, question, or blocker? If you find even one, the extraction failed.

**Actionability test**: Could someone execute each task from its description alone? "Fix the bug" fails. "Fix the race condition in WebSocket reconnect logic (server.py line 47)" passes.

**Ownership test**: Every task has exactly one owner. "Both" is acceptable only for tasks requiring real-time collaboration. If unsure, assign to the user — they can reassign.

**Priority test**: Do the priorities reflect the conversation's actual urgency signals, not just the AI's guess? Re-read the user's exact words before assigning P0.

**Attack order test**: Does the sequence respect dependencies? A task that depends on another must come after it. Does it front-load blockers?

---

## Why This Skill Exists

At the end of a productive AI session, there are always tasks scattered across the conversation — some explicit ("we need to test this"), some implicit (a decision that requires follow-up), some invisible (a question asked but never answered). Without systematic extraction, these tasks fall through the cracks.

The 6-category checklist ensures nothing is missed, and the attack order transforms a flat list into an actionable plan.
