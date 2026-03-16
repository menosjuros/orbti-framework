<objective>
Manage ORBIT project configuration and integrations. Create or update .orbit/config.md at any point in the project lifecycle.
</objective>

<when_to_use>
- Enable SonarQube after project init
- Disable an integration
- View current configuration
- Change project settings
</when_to_use>

<process>

**Step 1: Check for existing config**

```bash
ls .orbit/config.md 2>/dev/null
```

**If config exists:**
```
Current configuration:

[Display config.md contents]

What would you like to do?
[1] Enable/disable integration
[2] View full config
[3] Reset to defaults
```

**If config doesn't exist:**
```
No configuration found.

Would you like to set up project configuration?
[1] Yes, create config
[2] Cancel
```

**Step 2: Handle user choice**

**For new config or "Enable/disable integration":**

```
Available integrations:

[1] Testing  - Automated acceptance tests (Playwright)
    Status: [automated: on/off | type: ui/api/both | evidence: video+screenshot/screenshot/logs]

[2] SonarQube - Code quality scanning
    Status: [enabled/disabled/not configured]

[3] Done - save and exit
```

**If user selects Testing:**

```
Testing configuration:

Current: automated=[true/false]  type=[ui/api/both/none]

[1] Toggle automated on/off
[2] Change type (ui / api / both / none)
[3] Change evidence (video+screenshot / screenshot / logs only)
[4] Change base URL
[5] Back
```

**Toggle automated:**
- Flip `automated` true↔false in config.md

**Change type:**
```
Project type:
[1] UI/Web    — browser tests, screenshots, video
[2] API/Backend — HTTP tests, response logs only
[3] Both      — browser + API
[4] None      — disable automated testing
```
Update `type` in config.md.

**Change evidence (UI/Both only):**
```
Evidence to collect:
[1] Video + Screenshot  (full recording + snapshot)
[2] Screenshot only
[3] Logs only           (no visual evidence)
```
Update `evidence.video` and `evidence.screenshot` in config.md.

**Change base URL:**
- Prompt for new URL, update `base_url` or `api_base_url` as appropriate.

---

**If user selects SonarQube:**

```
SonarQube integration:

Current: [enabled/disabled/not configured]

[1] Enable
[2] Disable
[3] Back
```

**If enabling:**
- Prompt for project_key (default: directory name)
- Create/update config.md with sonarqube.enabled = true

**If disabling:**
- Update config.md with sonarqube.enabled = false

**Step 3: Write config**

Create or update `.orbit/config.md`:

```markdown
# Project Config

**Project:** [project_name]
**Updated:** [timestamp]

## Project Settings

```yaml
project:
  name: [project_name]
  version: [version or "0.0.0"]
```

## Integrations

### SonarQube

```yaml
sonarqube:
  enabled: [true/false]
  project_key: [key]
```

## Preferences

```yaml
preferences:
  auto_commit: false
  verbose_output: false
```

---
*Config updated: [timestamp]*
```

**Step 4: Confirm**

```
════════════════════════════════════════
CONFIG UPDATED
════════════════════════════════════════

Integrations:
  Testing:   automated=[on/off] type=[ui/api/both/none] evidence=[video+screenshot/screenshot/logs]
  SonarQube: [enabled/disabled]

Config saved to: .orbit/config.md

────────────────────────────────────────
[If SonarQube just enabled:]
▶ NEXT: /orbit:quality-gate
  Run your first code quality scan.

[Otherwise:]
Configuration complete.
────────────────────────────────────────
```

</process>

<output>
- `.orbit/config.md` created or updated
- Integration status changed as requested
- Clear next steps if applicable
</output>
