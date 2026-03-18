---
name: create-skill
description: Create a new skill through a guided conversation. Use when a user wants help authoring a skill from scratch, choosing between quick or full guidance, collecting required and optional skill details, generating a standard skill folder with SKILL.md and README.md, and explaining how to make the new skill take effect.
---

# Create Skill

Guide the user through creating a new skill from scratch.

## Interaction Rules

### Input Style

- Always present choices as numbered options (1, 2, 3...).
- Never ask the user to type full words like "quick" or "full".
- Accept the number, the option text, or common abbreviations.

### Question Rhythm

- Ask ONE question at a time.
- Wait for the user's answer before asking the next question.
- Never dump multiple questions in a single message.

### Communication Style

- Never add self-explanatory filler text.
- Never explain what you are going to do before doing it.
- Keep all prompts as concise as possible.
- Use the same terse style as a real CLI tool.

## Workflow

Follow these steps in order:

1. Prompt for mode selection (1 or 2).
2. Prompt for skill purpose.
3. Prompt for trigger style.
4. Prompt for dependency type.
5. Prompt for install location.
6. Prompt for skill name.
7. Show summary.
8. Show preview.
9. Prompt for confirmation.
10. Write files.
11. Show completion with next steps.

## Step 1: Mode Selection

Output exactly:

```
Create a new skill.

1) Quick guidance
2) Full guidance

Choice [1]:
```

- Default to 1 (Quick) if user just presses Enter.
- If user enters invalid option, re-prompt.

## Step 2: Skill Purpose

In Quick mode, after mode is selected, ask:

```
What should this skill do?
```

Accept any natural language answer. Extract the core task from their response.

## Step 3: Trigger Style

After purpose is captured, ask:

```
How do users trigger it?

1) Chat naturally
2) As a command (/)
3) Not sure

Choice [1]:
```

## Step 4: Dependency Type

After trigger style, ask:

```
Does it need external tools?

1) No extra dependency
2) Built-in tools
3) Local CLI tool
4) External API

Choice [1]:
```

## Step 5: Install Location

After dependency type, ask:

```
Where to install?

1) Current workspace
2) Shared (all projects)

Choice [1]:
```

## Step 6: Skill Name

After install location, ask:

```
Skill name (lowercase, hyphenated):
```

- Propose a name based on the skill purpose if user is unsure.
- Validate format (lowercase, digits, hyphens only).

## Step 7: Summary

After all inputs are collected, show a brief summary:

```
Summary:
- Name: <name>
- Purpose: <purpose>
- Trigger: <trigger>
- Dependency: <dependency>
- Location: <workspace|shared>
- Files: SKILL.md, README.md

Continue? [y/n]:
```

## Step 8: Preview

If user confirms, generate the content and show a preview. Keep it brief - just the key parts.

## Step 9: Confirmation

After preview, ask:

```
Write files? [y/n]:
```

## Step 10: Write Files

If confirmed:
- Create the skill directory.
- Write SKILL.md.
- Write README.md.
- If directory exists, stop and ask whether to overwrite.

## Step 11: Completion

After writing, show:

```
Created: <path>

To activate:
- Restart the agent, or
- Run: refresh skills (if supported)

To test:
- "<test prompt>"

Done.
```

- Show the skill path.
- Show activation command (adapt to the agent's refresh mechanism).
- Show one natural test prompt.
- Keep it under 10 lines total.

## Full Guidance Mode

In Full mode, after step 1, follow the same sequence but ask additional optional questions between steps:

After purpose:
- "Target users? (optional, press Enter to skip)"

After trigger style:
- "User-invocable? [y/n]"

After dependency:
- "Required binaries? (comma separated, optional)"
- "Environment variables? (comma separated, optional)"
- "OS restrictions? (darwin/linux/win32, optional)"

After install location:
- "Homepage? (optional, press Enter to skip)"
- "Emoji? (optional, press Enter to skip)"

Skip any question if user indicates it's not needed.

## Default Values

When user skips or is unsure:

- trigger: 1 (Chat naturally)
- dependency: 1 (No extra dependency)
- location: 1 (Current workspace)
- name: auto-generate from purpose
- user-invocable: true

## Writing Rules

- Create `<skill-dir>/SKILL.md`
- Create `<skill-dir>/README.md`
- Do NOT create extra folders or scripts unless user explicitly asks.
- Do NOT edit any config files automatically.
- Do NOT run any activation commands automatically.

## Directory Targets

- Workspace skill: `<workspace>/skills/<skill-name>/`
- Shared skill: `~/.agent-skills/<skill-name>/` or the platform's shared skill directory

If workspace path is unclear, inspect the environment before writing.

## Generated Skill Format

The generated SKILL.md should follow the AgentSkills format:

```markdown
---
name: <skill-name>
description: <description>
---

# <Skill Name>

## Purpose

...

## When To Use

...

## How To Use

...
```

The generated README.md should be human-readable and include:
- What the skill does
- How to use it
- Dependencies
- How to activate it for the current agent

## Language

- Use English by default.
- If user clearly uses another language, switch to that language for all prompts and output.
