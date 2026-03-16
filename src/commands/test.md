---
name: orbit:test
description: Run acceptance tests — automated via Playwright if available, otherwise guided manual UAT
argument-hint: "[optional: phase or plan number, e.g., '4' or '04-02']"
allowed-tools: [Read, Bash, Glob, Grep, Edit, Write, AskUserQuestion]
---

<objective>
Validate that what was built actually works against the acceptance criteria.

**Two modes — auto-detected:**
1. **Playwright mode** — Playwright detected in project → generates spec from ACs, runs tests, collects screenshot/trace evidence
2. **Manual mode** — No Playwright → guides user through checklist, captures results via AskUserQuestion

Both modes log issues to the phase-scoped UAT file and produce a verdict.
</objective>

<execution_context>
@~/.claude/orbit-framework/workflows/playwright-test.md
@~/.claude/orbit-framework/workflows/verify-work.md
@~/.claude/orbit-framework/templates/UAT-ISSUES.md
@~/.claude/orbit-framework/templates/PLAYWRIGHT-EVIDENCE.md
</execution_context>

<context>
Scope: $ARGUMENTS (optional)
- If provided: Test specific phase or plan (e.g., "4" or "04-02")
- If not provided: Test most recently completed plan

@.orbit/STATE.md
@.orbit/ROADMAP.md
</context>

<process>

<step name="detect_mode">
**Step 1 — Read config:**
```bash
cat .orbit/config.md 2>/dev/null | grep -A 10 "testing:"
```

Extract:
- `automated` — true/false
- `type` — ui / api / both / none (default: ui)
- `evidence.video`, `evidence.screenshot`

**Step 2 — Route based on config:**

| Config state | Action |
|---|---|
| `automated: false` or no config | Manual → `verify-work.md` |
| `automated: true, type: ui` | UI tests → `playwright-test.md` |
| `automated: true, type: api` | API tests → `api-test.md` |
| `automated: true, type: both` | UI tests first → `playwright-test.md`, then API → `api-test.md` |
| Playwright not installed | Warn, fallback to manual |

**Step 3 — Check Playwright if needed (ui/api/both):**
```bash
npx playwright --version 2>/dev/null && echo "FOUND" || echo "NOT_FOUND"
```

If NOT_FOUND and automated: true:
```
Playwright not found. To enable automated testing:
  npm init playwright@latest

Falling back to manual testing.
```
→ Route to verify-work.md
</step>

</process>

<success_criteria>
- [ ] Test scope identified from SUMMARY.md
- [ ] Mode detected (Playwright or manual)
- [ ] **Playwright mode:** spec generated, tests executed, evidence collected
- [ ] **Manual mode:** user guided through checklist, results captured
- [ ] Issues logged to `.orbit/phases/XX-name/{plan}-UAT.md`
- [ ] Evidence report created (Playwright mode only)
- [ ] Verdict presented
- [ ] User knows next steps
</success_criteria>

<anti_patterns>
- Don't skip evidence collection — screenshots and traces are required in Playwright mode
- Don't make assumptions about test results in manual mode — USER reports outcomes
- Don't fix issues during testing — capture for later via /orbit:plan-fix
- Don't dismiss minor issues — log everything
</anti_patterns>
