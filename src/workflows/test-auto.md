<purpose>
Auto-detect the project's test runner, write integration tests for any acceptance criteria without coverage, run tests, and map results to ACs.

Falls back to manual UAT if no test runner is found.
</purpose>

<test_runner_detection>
Detect test runner by inspecting project files — use what's already there, install nothing new.

```bash
# Node / JavaScript
cat package.json 2>/dev/null | grep -E '"test"|"jest"|"vitest"|"mocha"'

# Python
ls pytest.ini pyproject.toml setup.cfg conftest.py 2>/dev/null

# Go
ls go.mod 2>/dev/null && find . -name "*_test.go" -not -path "*/vendor/*" | head -3

# Rust
ls Cargo.toml 2>/dev/null && grep -r "#\[test\]" src/ --include="*.rs" | head -3

# Other
ls Makefile 2>/dev/null && grep -E "^test:" Makefile
```

**Decision matrix:**

| Found | Runner | Run command |
|-------|--------|-------------|
| `jest` in package.json | Jest | `npx jest --testPathPattern=<pattern>` |
| `vitest` in package.json | Vitest | `npx vitest run` |
| `pytest.ini` or `conftest.py` | Pytest | `python -m pytest` |
| `*_test.go` files | Go test | `go test ./...` |
| `#[test]` in Rust | Cargo | `cargo test` |
| `test:` in Makefile | Make | `make test` |
| None found | — | Fall back to manual UAT |

</test_runner_detection>

<process>

<step name="identify_scope">
**Determine what to test:**

If $ARGUMENTS provided: find corresponding LOOP.md and INTEGRATE.md
If not: use most recently modified LOOP.md

Read LOOP.md to extract:
- All acceptance criteria (AC-1, AC-2, ...)
- Files created/modified (from INTEGRATE.md if exists)
- Boundaries (what was NOT changed)
</step>

<step name="detect_runner">
**Auto-detect test runner:**

Run the detection commands above.

If no runner found:
```
No test runner detected in this project.

Falling back to manual UAT.
```
→ Switch to: @~/.claude/orbit-framework/workflows/verify-work.md
</step>

<step name="map_acs_to_tests">
**Verify tests were written during BUILD:**

Tests should already exist — they are written during BUILD alongside the implementation.

For each AC in the LOOP.md, confirm a test exists:
```bash
grep -r "AC-[0-9]" tests/ spec/ __tests__/ test/ 2>/dev/null
```

If any AC has no test:
- Warn: "AC-N has no test — was BUILD completed? Writing test now."
- Write the missing test before running (same rules as BUILD: one test per AC, behavior not implementation, no new dependencies)
</step>

<step name="run_tests">
**Run the test suite:**

Run only tests related to the current plan's scope when possible.
If the runner supports file filtering, prefer that over running the full suite.

Capture full output. Look for:
- Pass / fail counts
- Which specific tests failed
- Error messages for failures
</step>

<step name="map_results_to_acs">
**Map test results back to ACs:**

Build a results table:

```
AC-1: [description] ........... PASS
AC-2: [description] ........... FAIL — [error summary]
AC-3: [description] ........... PASS
```

For each FAIL:
- Extract the error message
- Note which test file and line
- Categorize: assertion error / exception / timeout / setup failure
</step>


<step name="report_and_route">
**Present results and route:**

```
════════════════════════════════════════
TEST RESULTS: [Plan Name]
════════════════════════════════════════

Runner: [detected runner]
Tests written: [N new] / [M existing]

AC-1: ✓ PASS
AC-2: ✗ FAIL — [error]
AC-3: ✓ PASS

Tests: [total] | Passed: [N] | Failed: [N]

════════════════════════════════════════
Verdict: [ALL PASS | FAILURES FOUND]
════════════════════════════════════════
```

**If ALL PASS:**
→ Proceed to `/orbit:integrate`

**If FAILURES FOUND:**
- Log each failing AC to `.orbit/projects/XX-name/{plan}-UAT.md`
- Offer: "Run /orbit:plan-fix to address failing tests before integrating"
- User can override and integrate anyway (with issues logged)
</step>

</process>

<success_criteria>
- [ ] Test runner detected (or manual fallback triggered)
- [ ] ACs extracted from LOOP.md
- [ ] Existing test coverage mapped
- [ ] Missing tests written (one per uncovered AC)
- [ ] Tests executed
- [ ] Results mapped to AC-1, AC-2...
- [ ] Issues logged if any failures
- [ ] User routed to integrate or plan-fix
</success_criteria>
