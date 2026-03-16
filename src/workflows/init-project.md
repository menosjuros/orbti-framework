<purpose>
Initialize ORBIT structure in a new project. Creates .orbit/ directory with PROJECT.md, ROADMAP.md, STATE.md, and phases/ directory. Gathers project context conversationally before routing to planning.
</purpose>

<when_to_use>
- Starting ORBIT in a project that doesn't have .orbit/ directory
- User explicitly requests project initialization
- Beginning a new project from scratch
</when_to_use>

<loop_context>
N/A - This is a setup workflow, not a loop phase.
After init, project is ready for first REFINE.
</loop_context>

<philosophy>
**Flow and momentum:** Init should feel like the natural start of work, not a chore.
- Ask questions conversationally
- Populate files from answers (user doesn't edit templates)
- End with ONE next action
- Build momentum into planning
</philosophy>

<references>
@src/templates/config.md
@src/references/sonarqube-integration.md
</references>

<process>

<step name="check_existing" priority="first">
1. Check if .orbit/ directory exists:
   ```bash
   ls .orbit/ 2>/dev/null
   ```
2. If exists:
   - "ORBIT already initialized in this project."
   - Route to `/orbit:resume` or `/orbit:progress`
   - Exit this workflow
3. If not exists: proceed with initialization
</step>

<step name="create_structure">
Create directories first (gives immediate feedback):
```bash
mkdir -p .orbit/phases
```

Display:
```
ORBIT structure created.

Before planning, I need to understand what you're building.
```
</step>

<step name="gather_core_value">
**Ask ONE question at a time. Wait for response before next question.**

**Question 1: Core Value**
```
What's the core value this project delivers?

(Example: "Users can track expenses and see spending patterns")
```

Wait for user response. Store as `core_value`.
</step>

<step name="gather_description">
**Question 2: What are you building?**
```
What are you building? (1-2 sentences)

(Example: "A CLI tool for managing Docker containers")
```

Wait for user response. Store as `description`.
</step>

<step name="gather_project_name">
**Question 3: Project name**

Infer from:
1. Directory name
2. package.json name field
3. Ask if unclear

If obvious, confirm:
```
Project name: [inferred-name]

Is this correct? (yes/different name)
```

Store as `project_name`.
</step>

<step name="create_project_md">
Create `.orbit/PROJECT.md` with gathered information:

```markdown
# Project: [project_name]

## Description
[description]

## Core Value
[core_value]

## Requirements

### Must Have
- [To be defined during planning]

### Should Have
- [To be defined during planning]

### Nice to Have
- [To be defined during planning]

## Constraints
- [To be identified during planning]

## Success Criteria
- [core_value] is achieved
- [To be refined during planning]

---
*Created: [timestamp]*
```

Note: Requirements and constraints are populated during planning, not init.
</step>

<step name="create_roadmap_md">
Create `.orbit/ROADMAP.md`:

```markdown
# Roadmap: [project_name]

## Overview
[description]

## Current Milestone
**v0.1 Initial Release** (v0.1.0)
Status: Not started
Phases: 0 of TBD complete

## Phases

| Phase | Name | Plans | Status | Completed |
|-------|------|-------|--------|-----------|
| 1 | TBD | TBD | Not started | - |

## Phase Details

Phases will be defined during `/orbit:refine`.

---
*Roadmap created: [timestamp]*
```

Note: Phase details are populated during planning, not init.
</step>

<step name="create_state_md">
Create `.orbit/STATE.md`:

```markdown
# Project State

## Project Reference

See: .orbit/PROJECT.md (updated [timestamp])

**Core value:** [core_value]
**Current focus:** Project initialized — ready for planning

## Current Position

Milestone: v0.1 Initial Release
Phase: Not yet defined
Plan: None yet
Status: Ready to create roadmap and first REFINE
Last activity: [timestamp] — Project initialized

Progress:
- Milestone: [░░░░░░░░░░] 0%

## Loop Position

Current loop state:
```
REFINE ──▶ BUILD ──▶ INTEGRATE
  ○        ○        ○     [Ready for first REFINE]
```

## Accumulated Context

### Decisions
None yet.

### Deferred Issues
None yet.

### Blockers/Concerns
None yet.

## Session Continuity

Last session: [timestamp]
Stopped at: Project initialization complete
Next action: Run /orbit:refine to define phases and first plan
Resume file: .orbit/PROJECT.md

---
*STATE.md — Updated after every significant action*
```
</step>

<step name="prompt_integrations">
**Ask about optional integrations:**

```
Optional integrations:

Would you like to enable SonarQube code quality scanning?
(Requires SonarQube server and MCP server - can enable later)

[1] Yes, enable SonarQube
[2] Skip for now
```

Wait for user response.

**If "1" or "yes" or "enable":**

1. Prompt for project key:
   ```
   SonarQube project key?
   (Press Enter to use: [project_name])
   ```

   - If user presses Enter: use `project_name`
   - Otherwise: use provided key

2. Store `sonarqube_enabled = true`

**If "2" or "skip" or "no":**

Store `sonarqube_enabled = false`

---

**After both SonarQube and testing prompts are complete, create `.orbit/config.md` if any integration was configured:**

If `sonarqube_enabled = true` OR `testing_configured = true`:

```markdown
# Project Config

**Project:** [project_name]
**Created:** [timestamp]

## Project Settings

```yaml
project:
  name: [project_name]
  version: 0.0.0
```

## Integrations

### SonarQube

```yaml
sonarqube:
  enabled: [true/false]
  project_key: [project_key]   # only if enabled
```

### Testing

```yaml
testing:
  automated: [true/false]
  type: [ui/api/both/none]
  base_url: [base_url]         # if ui or both
  api_base_url: [api_base_url] # if api or both
  evidence:
    video: [true/false]
    screenshot: [true/false]
    logs: true
```

## Preferences

```yaml
preferences:
  auto_commit: false
  verbose_output: false
```

---
*Config created: [timestamp]*
```

Store `integrations_enabled = true`

Otherwise, store `integrations_enabled = false` (config.md not created — user can add later via `/orbit:config`)
</step>

<step name="prompt_testing">
**Ask about automated testing:**

```
Automated testing:

Would you like ORBIT to run automated acceptance tests with /orbit:test?
(Uses Playwright — can enable/change later via /orbit:config)

[1] Yes — UI/Web project  (browser tests, screenshots, video)
[2] Yes — API/Backend     (HTTP request tests, response logs)
[3] Yes — Both            (browser + API tests)
[4] No  — Manual UAT only
```

Wait for user response. Store as `testing_type`.

---

**If "1" (UI):**

```
Evidence to collect per test:

[1] Video + Screenshot  (full recording + snapshot)
[2] Screenshot only     (snapshot, no video)
[3] Logs only           (no visual evidence — good for CI)
```

Store as `testing_evidence`. Map: 1→`video+screenshot`, 2→`screenshot`, 3→`logs`

---

**If "2" (API):**

```
Base API URL?
(e.g., http://localhost:3000/api — can change later)
```

Store as `api_base_url`. Evidence is always `logs` for API.

---

**If "3" (Both):**

Ask for evidence (same options as UI above) AND api_base_url.

---

**If "4" (No/Manual):**

Store `testing_type = none`. No further questions.

---

**Build testing config block** based on answers:

```yaml
testing:
  automated: [true/false]
  type: [ui/api/both/none]
  base_url: http://localhost:3000        # if ui or both
  api_base_url: http://localhost:3000/api # if api or both
  evidence:
    video: [true/false]       # true only if video+screenshot
    screenshot: [true/false]  # true if video+screenshot or screenshot
    logs: true
```

Store `testing_configured = true` if type != none.
</step>

<step name="check_specialized_flows">
**Ask about specialized skills:**

```
Do you have specialized skills or commands for this project?
(e.g., /revops-expert, /frontend-design, custom workflows)

[1] Yes, configure now
[2] Skip for now (add later via /orbit:flows)
```

Wait for user response.

**If "1" or "yes" or "configure":**

1. Store `specialized_flows_enabled = true`
2. Route to: @workflows/configure-special-flows.md
3. After completion, return to init confirmation
4. Store `skills_configured_count` from workflow output

**If "2" or "skip" or "no":**

Store `specialized_flows_enabled = false`
(User can add later via /orbit:flows)
</step>

<step name="confirm_and_route">
**Display confirmation with ONE next action:**

**Display based on enabled features:**

```
════════════════════════════════════════
ORBIT INITIALIZED
════════════════════════════════════════

Project: [project_name]
Core value: [core_value]

Created:
  .orbit/PROJECT.md    ✓
  .orbit/ROADMAP.md    ✓
  .orbit/STATE.md      ✓
  .orbit/config.md     ✓  (if integrations_enabled: show "SonarQube: on/off | Testing: ui/api/both/none — video/screenshot/logs")
  .orbit/SPECIAL-FLOWS.md  ✓  (if specialized_flows_enabled: "[N] skills configured")
  .orbit/phases/       ✓

────────────────────────────────────────
▶ NEXT: /orbit:refine
  Define your phases and create your first plan.
────────────────────────────────────────

Type "yes" to proceed, or ask questions first.
```

**Note:** Only show config.md and SPECIAL-FLOWS.md lines if those features were enabled.
If neither was enabled, show the minimal version without those lines.

**Do NOT suggest multiple next steps.** ONE action only.
</step>

</process>

<output>
- `.orbit/` directory structure
- `.orbit/PROJECT.md` (populated from conversation)
- `.orbit/ROADMAP.md` (skeleton for planning)
- `.orbit/STATE.md` (initialized state)
- `.orbit/config.md` (if integrations enabled)
- `.orbit/SPECIAL-FLOWS.md` (if specialized flows enabled)
- `.orbit/phases/` (empty directory)
- Clear routing to `/orbit:refine`
</output>

<error_handling>
**Permission denied:**
- Report filesystem error
- Ask user to check permissions

**User declines to answer:**
- Use "[TBD]" placeholder
- Note that planning will ask for this information

**Partial creation failure:**
- Report what was created vs failed
- Offer to retry or clean up
</error_handling>
