<purpose>
Execute automated tests for the current phase and collect evidence. Routes to Playwright (frontend) or Bash (API) based on project type in config.md. On failure, triggers /orbit:observe to restart the cycle.
</purpose>

<when_to_use>
- After INTEGRATE — validating what was built
- When /orbit:test is invoked
</when_to_use>

<process>

<step name="identify_scope" priority="first">
**Determine what to test:**

If $ARGUMENTS provided:
- Parse as phase number (e.g., "4") or plan number (e.g., "04-02")
- Find corresponding INTEGRATE.md

If no arguments:
- Find most recently modified INTEGRATE.md:
```bash
find .orbit/phases -name "*INTEGRATE.md" -type f | xargs ls -t | head -1
```

Read INTEGRATE.md to extract acceptance criteria and verify commands.
</step>

<step name="read_config">
**Read project type from `.orbit/config.md`:**

```bash
cat .orbit/config.md 2>/dev/null
```

Extract:
- `testing.type` → `frontend` or `api`
- `testing.frontend.evidence` → `video` | `screenshot` | `log`

If config.md missing or `testing.type` not set:
```
No test configuration found in .orbit/config.md.

What type of project is this?
[1] Frontend (Playwright)
[2] API (Bash commands)
```
Ask user, then update config.md.
</step>

<step name="prepare_evidence_dir">
**Create evidence directory for this phase:**

```bash
mkdir -p .orbit/phases/{NN}-{name}/evidence
```
</step>

<!-- ============================================================ -->
<!-- BRANCH A: FRONTEND — PLAYWRIGHT                              -->
<!-- ============================================================ -->

<step name="frontend_setup" condition="testing.type == frontend">
**Check Playwright is available:**

```bash
npx playwright --version 2>/dev/null
```

If not installed:
```
Playwright not found. Installing...
npm install --save-dev @playwright/test
npx playwright install
```

**Configure evidence collection** based on `testing.frontend.evidence`:

- `video` → ensure `playwright.config` has `use: { video: 'on' }`
- `screenshot` → ensure `use: { screenshot: 'on' }`
- `log` → no extra config needed, capture stdout only

If `playwright.config.ts` / `playwright.config.js` doesn't exist, create minimal config:

```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  use: {
    video: '[video_setting]',       // 'on' | 'off'
    screenshot: '[screenshot_setting]', // 'on' | 'off'
    outputFolder: '.orbit/phases/{NN}-{name}/evidence',
  },
});
```
</step>

<step name="frontend_run" condition="testing.type == frontend">
**Run Playwright tests:**

```bash
npx playwright test --reporter=list 2>&1 | tee .orbit/phases/{NN}-{name}/evidence/test-log.txt
```

Capture exit code. Store as `test_exit_code`.
</step>

<step name="frontend_collect_evidence" condition="testing.type == frontend">
**Collect and organize evidence** based on config:

- `video` → move Playwright videos to `.orbit/phases/{NN}-{name}/evidence/videos/`
- `screenshot` → move screenshots to `.orbit/phases/{NN}-{name}/evidence/screenshots/`
- `log` → `test-log.txt` already saved

List evidence collected:
```bash
ls -la .orbit/phases/{NN}-{name}/evidence/
```
</step>

<!-- ============================================================ -->
<!-- BRANCH B: API — BASH                                         -->
<!-- ============================================================ -->

<step name="api_run" condition="testing.type == api">
**Extract verify commands from REFINE.md:**

Read `.orbit/phases/{NN}-{name}/{plan}-REFINE.md` and extract all `<verify>` sections from tasks.

**Execute each command and capture output:**

```bash
# For each verify command:
echo "=== [task name] ===" >> .orbit/phases/{NN}-{name}/evidence/test-log.txt
[verify command] >> .orbit/phases/{NN}-{name}/evidence/test-log.txt 2>&1
echo "Exit: $?" >> .orbit/phases/{NN}-{name}/evidence/test-log.txt
echo "" >> .orbit/phases/{NN}-{name}/evidence/test-log.txt
```

Track each command's exit code. Store as list of `{task, command, exit_code, output}`.
</step>

<!-- ============================================================ -->
<!-- SHARED: RESULTS + EVIDENCE + FAILURE CYCLE                  -->
<!-- ============================================================ -->

<step name="evaluate_results">
**Parse results:**

- Count passed (exit code 0) vs failed (exit code != 0)
- List any failures with output snippets

Display:
```
════════════════════════════════════════
TEST RESULTS
════════════════════════════════════════

Type:     [frontend / api]
Passed:   [N]
Failed:   [N]

Evidence: .orbit/phases/{NN}-{name}/evidence/

[If failed:]
Failures:
  ✗ [task/test name] — [brief error]
  ✗ ...
════════════════════════════════════════
```
</step>

<step name="write_test_md">
**Create TEST.md for this phase:**

Path: `.orbit/phases/{NN}-{name}/{plan}-TEST.md`

```markdown
# Test Results: Phase [N] Plan [N]

**Date:** [timestamp]
**Type:** [frontend / api]
**Verdict:** [PASS / FAIL]

## Results

| Test | Status |
|------|--------|
| [name] | ✓ Pass / ✗ Fail |

## Evidence

- Log: `.orbit/phases/{NN}-{name}/evidence/test-log.txt`
- [Videos: `.orbit/phases/{NN}-{name}/evidence/videos/`]
- [Screenshots: `.orbit/phases/{NN}-{name}/evidence/screenshots/`]

## Failures

[If any, list with error details]
```
</step>

<step name="on_failure_restart_cycle">
**If any tests failed:**

```
Tests failed. Restarting cycle from OBSERVE to incorporate failure context.

Failures will be added to OBSERVE context so the plan can be corrected.

────────────────────────────────────────
▶ RESTARTING: /orbit:observe
  Context: test failures from [plan]
────────────────────────────────────────
```

Trigger `/orbit:observe` passing failure context:
- Which tests failed
- Error output snippets
- Evidence paths

The cycle restarts: OBSERVE → REFINE → BUILD → INTEGRATE → TEST

**If all tests passed:**

```
════════════════════════════════════════
ALL TESTS PASSED ✓
════════════════════════════════════════

Evidence saved to: .orbit/phases/{NN}-{name}/evidence/

────────────────────────────────────────
▶ NEXT: /orbit:refine (next phase)
────────────────────────────────────────
```
</step>

</process>

<success_criteria>
- [ ] Project type determined from config.md
- [ ] Tests executed (Playwright or Bash)
- [ ] Evidence collected in phase evidence directory
- [ ] TEST.md created with results and evidence paths
- [ ] On failure: /orbit:observe triggered with failure context
- [ ] On pass: routed to next phase
</success_criteria>
