<div align="center">

# ORBIT

**Observe, Refine, Build, Integrate, Test** — Structured AI-assisted development for Claude Code.

[![npm version](https://img.shields.io/npm/v/orbit-framework?style=for-the-badge&logo=npm&logoColor=white&color=CB3837)](https://www.npmjs.com/package/orbit-framework)
[![License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/ChristopherKahler/orbit?style=for-the-badge&logo=github&color=181717)](https://github.com/ChristopherKahler/orbit)

<br>

```bash
npx orbit-framework
```

**Works on Mac, Windows, and Linux.**

<br>

![ORBIT Install](assets/terminal.svg)

<br>

*"Quality over speed-for-speed's-sake. In-session context over subagent sprawl."*

<br>

[Why ORBIT](#why-orbit) · [Getting Started](#getting-started) · [The Loop](#the-loop) · [Commands](#commands) · [How It Works](#how-it-works)

</div>

---

## Why ORBIT

I build with Claude Code every day. It's incredibly powerful — when you give it the right context.

The problem? **Context rot.** As your session fills up, quality degrades. Subagents spawn with fresh context but return ~70% quality work that needs cleanup. Plans get created but never closed. State drifts. You end up debugging AI output instead of shipping features.

ORBIT fixes this with three principles:

1. **Loop integrity** — Every plan closes with UNIFY. No orphan plans. UNIFY reconciles what was planned vs what happened, updates state, logs decisions. This is the heartbeat.

2. **In-session context** — Subagents are expensive and produce lower quality for implementation work. ORBIT keeps development in-session with properly managed context. Subagents are reserved for discovery and research — their job IS to gather context.

3. **Acceptance-driven development** — Acceptance criteria are first-class citizens, not afterthoughts. Define done before starting. Every task references its AC. BDD format: `Given [precondition] / When [action] / Then [outcome]`.

The complexity is in the system, not your workflow. Behind the scenes: structured state management, XML task formatting, loop enforcement. What you see: a few commands that keep you on track.

---

## Who This Is For

**AI-assisted developers** who want structure without bureaucracy.

You describe what you want, Claude Code builds it, and ORBIT ensures:
- Plans have clear acceptance criteria
- Execution stays bounded
- Every unit of work gets closed properly
- State persists across sessions
- Decisions are logged for future reference

No sprint ceremonies. No story points. No enterprise theater. Just a system that keeps AI-assisted development reliable.

---

## Getting Started

```bash
npx orbit-framework
```

The installer prompts you to choose:
1. **Location** — Global (all projects) or local (current project only)

Verify with `/orbit:help` inside Claude Code.

### Quick Workflow

```bash
# 1. Initialize ORBIT in your project
/orbit:init

# 2. Create a plan for your work
/orbit:plan

# 3. Execute the approved plan
/orbit:apply

# 4. Close the loop (required!)
/orbit:unify

# 5. Check progress anytime
/orbit:progress
```

### Staying Updated

```bash
npx orbit-framework@latest
```

<details>
<summary><strong>Non-interactive Install</strong></summary>

```bash
npx orbit-framework --global   # Install to ~/.claude/
npx orbit-framework --local    # Install to ./.claude/
```

</details>

---

## The Loop

Every unit of work follows this cycle:

```
┌─────────────────────────────────────┐
│  PLAN ──▶ APPLY ──▶ UNIFY          │
│                                     │
│  Define    Execute    Reconcile     │
│  work      tasks      & close       │
└─────────────────────────────────────┘
```

### PLAN

Create an executable plan with:
- **Objective** — What you're building and why
- **Acceptance Criteria** — Given/When/Then definitions of done
- **Tasks** — Specific actions with files, verification, done criteria
- **Boundaries** — What NOT to change

### APPLY

Execute the approved plan:
- Tasks run sequentially
- Each task has verification
- Checkpoints pause for human input when needed
- Deviations are logged

### UNIFY

Close the loop (required!):
- Create SUMMARY.md documenting what was built
- Compare plan vs actual
- Record decisions and deferred issues
- Update STATE.md

**Never skip UNIFY.** Every plan needs closure. This is what separates structured development from chaos.

---

## Commands

ORBIT provides 26 commands organized by purpose. Run `/orbit:help` for the complete reference.

### Core Loop

| Command | What it does |
|---------|--------------|
| `/orbit:init` | Initialize ORBIT in a project |
| `/orbit:plan [phase]` | Create an executable plan |
| `/orbit:apply [path]` | Execute an approved plan |
| `/orbit:unify [path]` | Reconcile and close the loop |
| `/orbit:help` | Show command reference |
| `/orbit:status` | Show loop position *(deprecated — use progress)* |

### Session

| Command | What it does |
|---------|--------------|
| `/orbit:pause [reason]` | Create handoff for session break |
| `/orbit:resume [path]` | Restore context and continue |
| `/orbit:progress [context]` | Smart status + ONE next action |
| `/orbit:handoff [context]` | Generate comprehensive handoff |

### Roadmap

| Command | What it does |
|---------|--------------|
| `/orbit:add-phase <desc>` | Append phase to roadmap |
| `/orbit:remove-phase <N>` | Remove future phase |

### Milestone

| Command | What it does |
|---------|--------------|
| `/orbit:milestone <name>` | Create new milestone |
| `/orbit:complete-milestone` | Archive and tag milestone |
| `/orbit:discuss-milestone` | Articulate vision before starting |

### Pre-Planning

| Command | What it does |
|---------|--------------|
| `/orbit:discuss <phase>` | Capture decisions before planning |
| `/orbit:assumptions <phase>` | See Claude's intended approach |
| `/orbit:discover <topic>` | Explore options before planning |
| `/orbit:consider-issues` | Triage deferred issues |

### Research

| Command | What it does |
|---------|--------------|
| `/orbit:research <topic>` | Deploy research agents |
| `/orbit:research-phase <N>` | Research unknowns for a phase |

### Specialized

| Command | What it does |
|---------|--------------|
| `/orbit:flows` | Configure skill requirements |
| `/orbit:config` | View/modify ORBIT settings |
| `/orbit:map-codebase` | Generate codebase overview |

### Quality

| Command | What it does |
|---------|--------------|
| `/orbit:verify` | Guide manual acceptance testing |
| `/orbit:plan-fix` | Plan fixes for UAT issues |

---

## How It Works

### Project Structure

```
.orbit/
├── PROJECT.md           # Project context and requirements
├── ROADMAP.md           # Phase breakdown and milestones
├── STATE.md             # Loop position and session state
├── config.md            # Optional integrations
├── SPECIAL-FLOWS.md     # Optional skill requirements
└── phases/
    ├── 01-foundation/
    │   ├── 01-01-PLAN.md
    │   └── 01-01-SUMMARY.md
    └── 02-features/
        ├── 02-01-PLAN.md
        └── 02-01-SUMMARY.md
```

### State Management

**STATE.md** tracks:
- Current phase and plan
- Loop position (PLAN/APPLY/UNIFY markers)
- Session continuity (where you stopped, what's next)
- Accumulated decisions
- Blockers and deferred issues

When you resume work, `/orbit:resume` reads STATE.md and suggests exactly ONE next action. No decision fatigue.

### PLAN.md Structure

```markdown
---
phase: 01-foundation
plan: 01
type: execute
autonomous: true
---

<objective>
Goal, Purpose, Output
</objective>

<context>
@-references to relevant files
</context>

<acceptance_criteria>
## AC-1: Feature Works
Given [precondition]
When [action]
Then [outcome]
</acceptance_criteria>

<tasks>
<task type="auto">
  <name>Create login endpoint</name>
  <files>src/api/auth/login.ts</files>
  <action>Implementation details...</action>
  <verify>curl command returns 200</verify>
  <done>AC-1 satisfied</done>
</task>
</tasks>

<boundaries>
## DO NOT CHANGE
- database/migrations/*
- src/lib/auth.ts
</boundaries>
```

Every task has: files, action, verify, done. If you can't specify all four, the task is too vague.

### CARL Integration

ORBIT has a companion: **[CARL](https://github.com/ChristopherKahler/carl-core)** (Context Augmentation & Reinforcement Layer).

CARL is a dynamic rule injection system. Instead of bloating your context with static prompts, CARL loads rules just-in-time based on what you're doing:

| Trigger | Rules Loaded |
|---------|--------------|
| Working in `.orbit/` directory | ORBIT domain activates |
| Writing code | DEVELOPMENT rules load |
| Managing projects | PROJECTS rules load |

**ORBIT-specific rules CARL enforces:**
- Loop enforcement (PLAN → APPLY → UNIFY — no shortcuts)
- Boundary protection (DO NOT CHANGE sections are real)
- State consistency checks at phase transitions
- Verification requirements for every task
- Skill blocking (required skills must load before APPLY)

The ORBIT domain contains 14 rules that govern structured AI development. They load when you're in a ORBIT project, disappear when you're not. Your context stays lean.

**Without CARL:** You'd need massive static prompts in every session.
**With CARL:** Rules activate when relevant, disappear when not.

---

## Philosophy

### Acceptance-Driven Development (A.D.D.)

Acceptance criteria aren't afterthoughts — they're the foundation:

1. **AC defined before tasks** — Know what "done" means
2. **Tasks reference AC** — Every task links to AC-1, AC-2, etc.
3. **Verification required** — Every task needs a verify step
4. **BDD format** — Given/When/Then for testability

### In-Session Context

Why ORBIT minimizes subagents for development work:

| Issue | Impact |
|-------|--------|
| Launch cost | 2,000-3,000 tokens to spawn |
| Context gathering | Starts fresh, researches from scratch |
| Resynthesis | Results must be integrated back |
| Quality gap | ~70% compared to in-session work |
| Rework | Subagent output often needs cleanup |

**When ORBIT does use subagents:**
- **Discovery/exploration** — Codebase mapping, parallel exploration
- **Research** — Web searches, documentation gathering

For implementation, ORBIT keeps everything in-session with proper context management.

### Loop Integrity

The loop isn't optional:

```
PLAN ──▶ APPLY ──▶ UNIFY
  ✓        ✓        ✓     [Loop complete]
```

- **No orphan plans** — Every PLAN gets a SUMMARY
- **State reconciliation** — UNIFY catches drift
- **Decision logging** — Choices are recorded for future sessions

---

## Configuration

### Optional Integrations

ORBIT supports modular integrations configured in `.orbit/config.md`:

| Integration | Purpose |
|-------------|---------|
| SonarQube | Code quality metrics and issues |
| *Future* | Linting, CI/CD, test runners |

### SPECIAL-FLOWS

For projects with specialized requirements, `.orbit/SPECIAL-FLOWS.md` defines skills that must be loaded before execution:

```markdown
## Required Skills

| Skill | Work Type | Priority |
|-------|-----------|----------|
| /frontend-design | UI components | required |
| /revops-expert | Landing pages | required |
```

APPLY blocks until required skills are confirmed loaded.

---

## Troubleshooting

**Commands not found after install?**
- Restart Claude Code to reload slash commands
- Verify files exist in `~/.claude/commands/orbit/` (global) or `./.claude/commands/orbit/` (local)

**Commands not working as expected?**
- Run `/orbit:help` to verify installation
- Re-run `npx orbit-framework` to reinstall

**Loop position seems wrong?**
- Check `.orbit/STATE.md` for current state
- Run `/orbit:progress` for guided next action

**Resuming after a break?**
- Run `/orbit:resume` — it reads state and handoffs automatically

---

## Comparison

### vs. Ad-hoc AI Coding

| Ad-hoc | ORBIT |
|--------|------|
| No structure | Explicit planning gates |
| State drifts | STATE.md tracks everything |
| No closure | Mandatory UNIFY |
| Decisions lost | Decisions logged |

### vs. GSD

ORBIT takes a different approach from GSD:

| Aspect | GSD | ORBIT |
|--------|-----|------|
| Execution | Parallel subagents | In-session context |
| Loop | Optional closure | Mandatory UNIFY |
| Criteria | Embedded in tasks | First-class AC section |
| Rules | Static prompts | CARL dynamic loading |

Same comprehensive coverage, different philosophy. ORBIT prioritizes quality over speed-for-speed's-sake. See [ORBIT-VS-GSD.md](ORBIT-VS-GSD.md) for full comparison.

### vs. Traditional Planning

| Traditional | ORBIT |
|-------------|------|
| Documentation-first | Execution-first |
| Human-readable specs | AI-executable prompts |
| Separate from code | Colocated in .orbit/ |

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

## Author

**Chris Kahler** — [Chris AI Systems](https://github.com/ChristopherKahler)

Building tools for AI-assisted development.

---

<div align="center">

**Claude Code is powerful. ORBIT makes it reliable.**

</div>
