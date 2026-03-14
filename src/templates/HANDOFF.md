# ORBIT Handoff

**Date:** {{timestamp}}
**Session:** {{session_id}}
**Status:** {{status}}

---

## READ THIS FIRST

You have no prior context. This document tells you everything.

**Project:** {{project_name}}
**Core value:** {{core_value}}

---

## Current State

**Version:** {{version}}
**Phase:** {{phase_number}} of {{total_phases}} — {{phase_name}}
**Plan:** {{plan_id}} — {{plan_status}}

**Loop Position:**
```
PLAN ──▶ APPLY ──▶ UNIFY
  {{plan_mark}}        {{apply_mark}}        {{unify_mark}}
```

---

## What Was Done

{{accomplished_list}}

---

## What's In Progress

{{in_progress_list}}

---

## What's Next

**Immediate:** {{next_action}}

**After that:** {{following_action}}

---

## Key Files

| File | Purpose |
|------|---------|
| `.orbit/STATE.md` | Live project state |
| `.orbit/ROADMAP.md` | Phase overview |
| {{current_plan_path}} | {{plan_purpose}} |

---

## Resume Instructions

1. Read `.orbit/STATE.md` for latest position
2. Check if PLAN exists for current phase
3. Based on loop position:
   - `○○○` (fresh) → Run `/orbit:plan`
   - `✓○○` (planned) → Review plan, then `/orbit:apply`
   - `✓✓○` (applied) → Run `/orbit:unify`
   - `✓✓✓` (complete) → Ready for next phase

**Or simply run:** `/orbit:resume`

---

*Handoff created: {{timestamp}}*
*This file is the single entry point for fresh sessions*
