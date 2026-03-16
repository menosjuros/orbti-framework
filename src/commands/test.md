---
name: orbit:test
description: Execute automated tests with evidence collection based on project type
argument-hint: "[phase or plan number, e.g., '4' or '04-02']"
allowed-tools: [Read, Bash, Glob, Grep, Edit, Write, AskUserQuestion]
---

<objective>
Execute tests for the current phase and collect evidence. Behavior depends on project type configured in `.orbit/config.md`.

**Frontend projects:** Playwright CLI — runs tests, captures video/screenshot/log based on config.

**API projects:** Bash commands — executes verify commands from REFINE.md, captures output log.

**On failure:** Triggers `/orbit:observe` to reload context and restart the cycle.

**Evidence is always saved** to `.orbit/phases/XX-name/evidence/`.
</objective>

<execution_context>
@~/.claude/orbit-framework/workflows/verify-work.md
@~/.claude/orbit-framework/templates/TEST.md
</execution_context>

<context>
Scope: $ARGUMENTS (optional — phase or plan number)

@.orbit/config.md
@.orbit/STATE.md
@.orbit/ROADMAP.md
</context>

<process>
**Follow workflow: @~/.claude/orbit-framework/workflows/verify-work.md**
</process>

<success_criteria>
- [ ] Project type read from config.md
- [ ] Tests executed (Playwright or Bash)
- [ ] Evidence collected and saved to phase directory
- [ ] TEST.md created with results and evidence paths
- [ ] On failure: OBSERVE triggered to restart cycle
</success_criteria>
