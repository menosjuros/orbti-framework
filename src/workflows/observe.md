<purpose>
Facilitate vision articulation before a project exists. Acts as a thinking partner to explore what the user wants to accomplish, then creates CONTEXT.md as handoff to /orbti:refine.

**Philosophy:** Goals first — everything else (approach, constraints, risks) derives from what the user wants to achieve.

**Model:** Observe runs before the project folder exists. No ROADMAP validation. Output feeds /orbti:refine which creates the project.
</purpose>

<when_to_use>
- Before planning, when goals and approach need exploration
- User has rough ideas but needs to articulate them
- Scope is unclear or has multiple possible approaches
</when_to_use>

<loop_context>
N/A - Pre-planning workflow. No project exists yet.
After discussion, routes to /orbti:refine which creates the project.
</loop_context>

<required_reading>
@.orbti/STATE.md
@.orbti/PROJECT.md
</required_reading>

<references>
@src/templates/CONTEXT.md (handoff format)
</references>

<process>

<step name="open_discussion">
**Present the topic and open discussion:**

If $ARGUMENTS provided:
```
════════════════════════════════════════
OBSERVE
════════════════════════════════════════

Topic: {arguments}

```

If no arguments:
```
════════════════════════════════════════
OBSERVE
════════════════════════════════════════

```

Then ask the core question:

```
What do you want to accomplish?

Don't worry about implementation details yet — describe what
success looks like and any specific goals you have in mind.
```

Wait for user response.
</step>

<step name="explore_goals">
**Follow up on goals:**

- "Any specific features or capabilities you're prioritizing?"
- "What's the most important outcome?"
- "Any concerns or risks you want to address?"

Store responses as `goals` list.
</step>

<step name="explore_approach">
**Ask about approach:**

```
How do you want to approach this?

Consider:
- Any specific patterns or libraries to use/avoid?
- Constraints or limitations?
- Dependencies on other work?
```

Wait for user response. Store as `approach` notes.
</step>

<step name="synthesize_context">
From the discussion, derive:

1. **Key goals** — synthesize main objectives
   - Confirm: "So the main goals are: {goals}. Sound right?"

2. **Approach notes** — capture technical direction and constraints

3. **Open questions** — anything still unclear, items to decide during planning

Confirm with user before writing.
</step>

<step name="write_context">
Create `.orbti/context/CONTEXT.md`:

Use CONTEXT.md template format.

Display:
```
Context saved to .orbti/context/CONTEXT.md

This file persists across /clear so you can take a break if needed.
/orbti:refine will pick it up automatically.
```
</step>

<step name="handoff">
```
════════════════════════════════════════
OBSERVE COMPLETE
════════════════════════════════════════

Goals: {goal_count}
Status: Ready for planning

────────────────────────────────────────
▶ NEXT: /orbti:refine
────────────────────────────────────────

Type "yes" to proceed, or continue discussing.
```

**Accept:** "yes", "go", "refine" → run `/orbti:refine`
</step>

</process>

<output>
- .orbti/context/CONTEXT.md created (handoff file for /orbti:refine)
- Goals and approach articulated
</output>

<success_criteria>
- [ ] Goals explored (user-driven)
- [ ] Approach discussed
- [ ] Context synthesized and confirmed
- [ ] CONTEXT.md written to .orbti/context/
- [ ] Clear handoff to /orbti:refine
</success_criteria>

<anti_patterns>
**Asking abstract questions first:**
DON'T: "What's the scope of this project?"
DO: "What do you want to accomplish?"

**Assuming approach before goals:**
DON'T: "What libraries will you use?"
DO: Derive approach from goals discussed.

**Requiring a project to exist:**
DON'T: Validate against ROADMAP.md or require a project number.
DO: This runs before the project exists. /orbti:refine creates it.

**Not persisting context:**
DON'T: End discussion without writing CONTEXT.md
DO: Always write the file so /clear doesn't lose progress.
</anti_patterns>

<error_handling>
**User unsure what to accomplish:**
- Reference PROJECT.md for overall goals
- Offer: "We can start broad and narrow down"

**Scope too large:**
- Note it during planning: "This sounds like multiple projects — we can split in /orbti:refine"

**User wants to skip discussion:**
- Route directly to /orbti:refine
- Note: "Going straight to planning — no discussion context will be available"
</error_handling>
