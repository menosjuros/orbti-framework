---
name: orbit:observe
description: Load all context needed before planning — technical, business, and codebase
argument-hint: "<phase or topic>"
allowed-tools: [Read, Bash, Glob, Grep, WebSearch, WebFetch, Task, AskUserQuestion]
---

<objective>
Load everything needed into context before planning a phase. Covers technical options, business rules, existing codebase patterns, and relevant decisions.

**Always runs before REFINE** — either called directly or triggered automatically by `/orbit:refine` as a dependency.

If OBSERVE.md already exists for the phase, `/orbit:refine` skips this step.
</objective>

<execution_context>
@~/.claude/orbit-framework/workflows/discovery.md
@~/.claude/orbit-framework/templates/OBSERVE.md
</execution_context>

<context>
$ARGUMENTS (phase number or topic)

@.orbit/PROJECT.md
@.orbit/STATE.md
@.orbit/ROADMAP.md
</context>

<process>
**Follow workflow: @~/.claude/orbit-framework/workflows/discovery.md**

Load context across all relevant dimensions:
1. **Technical** — options, libraries, architecture decisions
2. **Business** — domain rules, expected behavior, constraints
3. **Codebase** — existing patterns, relevant implementations, decisions already made

Output: OBSERVE.md with full context picture, ready for /orbit:refine to consume.
</process>

<success_criteria>
- [ ] Technical context loaded
- [ ] Business rules and domain logic identified
- [ ] Relevant codebase patterns surfaced
- [ ] OBSERVE.md created in phase directory
- [ ] Ready for /orbit:refine
</success_criteria>
