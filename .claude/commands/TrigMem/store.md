---
name: store
description: |
  Stores content in the appropriate Claude Code memory location.
  Analyzes content and routes to CLAUDE.md, Rules, Skills, Commands, or Agents.
disable-model-invocation: true
argument-hint: "[content to store]"
allowed-tools: Read, Write, Grep, Glob
---

<store-command>

<templates>

<template id="command-file" path=".claude/commands/{name}.md">
Every command file MUST match this structure exactly — substitute only the bracketed values:
---
description: |
  {What this command does. Use when ...}
---

# {Command Name}

{command instructions}
</template>

<template id="skill-file" path=".claude/skills/{name}/SKILL.md">
Every skill file MUST match this structure exactly — substitute only the bracketed values:
---
name: {kebab-case-skill-name}
description: |
  {What this skill does. Use when ...}
allowed-tools: Read, Grep, Glob
---

# {Skill Name}

{skill instructions}
</template>

<template id="rule-file" path=".claude/rules/{name}.md">
---
globs: {glob-pattern}
---

{rule content}
</template>

</templates>

<constraint id="frontmatter-required" severity="CRITICAL">
Every Command and Skill file written by the Write tool MUST begin with --- on line 1 and contain YAML frontmatter.
The content string passed to Write MUST start with "---\n".
If you are about to call Write for a command or skill and the content does not start with ---, STOP and fix it before writing.
Claude Code will FAIL to discover files without frontmatter.
</constraint>

<workflow>

<step id="1" name="parse-input">
The user's argument is the content to store. If no argument, ask: "What content would you like to store?"
</step>

<step id="2" name="classify">
Invoke the TrigMem:decision skill with the user's content. It returns one of:
- Clear: single destination with high confidence
- Ambiguous: multiple plausible destinations
- Decomposition: content spans multiple categories (see step 6)
Use the skill's structured output (Destination, Operation, Section, File fields) to drive step 3.
</step>

<step id="3" name="present-decision">
Check the decision output for the Operation: field (update vs create).

<case type="clear">
Present: "Decision: [destination] | Operation: [create|update] | File path: [path] | Rationale: [why]"
Show a content preview. For Command/Skill files, the preview MUST start with --- frontmatter matching the template above.
</case>

<case type="ambiguous">
Present numbered options: "1. [Destination A] ([path]) — [create|update] — [rationale]"
State your recommendation. Wait for user response before proceeding.
</case>

<case type="decomposition">See step 6.</case>
</step>

<step id="4" name="confirm">
Before writing, show the file path and full content preview. For Command/Skill files, the preview MUST match the template starting with ---.
Ask: "Shall I create this file?" Wait for explicit confirmation. Do NOT write without confirmation.
</step>

<step id="5" name="write-file">

<destination type="claudemd">
<create>Create CLAUDE.md with sections: # {Project Name} / {description} / ## Stack / ## Structure / ## Commands / ## Docs. Place content per the decision skill's Section: field.</create>
<update>Read existing CLAUDE.md. Match Section: against headings using fuzzy matching (Stack → "Stack"/"Tech Stack"/"Technologies", Structure → "Project Structure"/"Directory Structure", Commands → "Commands"/"Dev Commands"/"Scripts", Docs → "Docs"/"Documentation"/"References", Critical Constraints → "Constraints"/"Important"/"Rules"/"Warnings", Header → top of file before first ##). Insert at end of matching section. If no match, create new section at template-ordered position (Stack → Structure → Commands → Docs → Critical Constraints). Show diff.</update>
</destination>

<destination type="rules" template="rule-file">
<create>Ask for glob pattern. Remind: broad patterns like ** provide no token savings over CLAUDE.md. Write .claude/rules/{name}.md using the rule-file template.</create>
<update>Read existing rule at Existing file: path. Show existing vs proposed. Ask: update existing or create new? Execute with preview.</update>
</destination>

<destination type="subfile">
<create>IMPORTANT: Only use when NO Skill exists for the same topic. If a Skill exists, use skill-companion instead.
Create docs/{name}.md with full content. Append pointer to CLAUDE.md: "See docs/{name}.md for [brief description]." Confirm both writes.</create>
<update>Read existing doc. Ask: append, merge into sections, or create new doc? If pointer already in CLAUDE.md, skip duplicate.</update>
</destination>

<destination type="commands" template="command-file">
<create>
Assemble the file content using the command-file template. The content string you pass to Write MUST be:
Line 1: ---
Line 2: description: |
Line 3:   {what this command does — Use when ...}
Line 4: ---
Line 5: (blank line)
Line 6: # {Command Name}
Line 7+: {command instructions}

Call Write to create .claude/commands/{name}.md with this content.
IMMEDIATELY after Write: Read the file back. If line 1 is not ---, rewrite with correct frontmatter prepended, then Read again to confirm.
If reusable knowledge is present, suggest extracting to a skill.
</create>
<update>Read existing command at Existing file: path. Show existing content. Ask: merge new steps into existing command, or create variant ({name}-v2.md)? Execute with preview.</update>
</destination>

<destination type="skills" template="skill-file">
<create>
Assemble the file content using the skill-file template. The content string you pass to Write MUST be:
Line 1: ---
Line 2: name: {kebab-case-skill-name}
Line 3: description: |
Line 4:   {what this skill does — Use when ...}
Line 5: allowed-tools: Read, Grep, Glob
Line 6: ---
Line 7: (blank line)
Line 8: # {Skill Name}
Line 9+: {skill instructions}

Call Write to create .claude/skills/{name}/SKILL.md with this content.
IMMEDIATELY after Write: Read the file back. If line 1 is not --- OR content does not contain name: and description:, rewrite with correct frontmatter, then Read again to confirm.
If project-specific paths mixed with universal pattern, suggest configurable skill pattern. For generic skills, suggest ~/.claude/skills/ for portability.
</create>
<update>Read existing SKILL.md at Existing file: path. Show existing content. Ask: append new section, replace section, or create variant skill? Execute with preview.</update>
</destination>

<destination type="skill-companion">
<create>Create `.claude/skills/{name}/reference.md` with plain markdown content (NO frontmatter — this is NOT a skill or command file).
Before creating: verify the skill directory exists by checking for `.claude/skills/{name}/SKILL.md`. If SKILL.md does not exist, create it first using the skill-file template, then create the companion.
After creating the companion: update CLAUDE.md to reference the skill path (`.claude/skills/{name}/SKILL.md`), NOT a `docs/` subfile.
In SKILL.md, add a reference: "See `reference.md` in this directory for detailed documentation."</create>
<update>Read existing `.claude/skills/{name}/reference.md`. Show existing content. Ask: append new material, replace existing content, or create an additional companion file (e.g., `reference-{topic}.md`)?</update>
</destination>

<destination type="subagents">
<create>Do NOT auto-create. Provide guidance: recommend .claude/agents/{name}.md with description, skills: frontmatter, task instructions. Note: sub-agents don't inherit CLAUDE.md, skills, or rules.</create>
<update>Read existing agent. Ask: merge responsibilities or create separate agent? Execute with preview.</update>
</destination>

</step>

<step id="6" name="decomposition">
When content spans multiple categories, present a plan:
"This content spans multiple categories: 1. [aspect] → [destination] ([path]) — [rationale] ..."
Then handle each component through steps 4-5, confirming each write individually.

Common patterns:
- "We use X technology" → CLAUDE.md (identity) + Skill (how-to) + Rules (conventions)
- "Run/Build/Deploy" → Command (action) + Skill (knowledge) + CLAUDE.md (prereqs)
- "Coding standard" → Skill (universal) + Rules (specific) + Skill companion (reference)
- "Architectural standard" → Skill (pattern) + Rule (conventions) + Sub-agent (applies it)

<substep id="6b" name="rule-candidate-extraction">
After presenting the decomposition plan, check the decision output for a `Rule candidates detected` section.

**If Rule candidates are present:**
Display them and ask the user:
"I detected enforceable constraints that could be extracted as separate Rules:
[list each candidate with constraint text and suggested glob]
Would you like me to:
1. Extract them as Rules (recommended)
2. Keep as-is"

If the user picks option 1: for each candidate, create `.claude/rules/{name}.md` using the rule-file template with the candidate's glob pattern in `globs:` frontmatter and the constraint text as rule content.

**If no Rule candidates:** skip this prompt entirely (zero cost, backward compatible).
</substep>
</step>

<step id="7" name="summary">
After all files created/updated:
"Stored successfully: Created [path] ([type]) / Updated [path] ([type], section: [section])"
Note: Placement is iterative — move it later if it doesn't work well in practice.
</step>

</workflow>

</store-command>
