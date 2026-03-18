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
3) Tool dispatch (direct to tool)
4) Not sure

Choice [1]:
```

If user chooses 3 (Tool dispatch), then ask:

```
Tool name to dispatch to:
```

Accept the tool name. This is required for tool dispatch mode.

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
  - Tool: <tool-name> (if tool dispatch)
- Dependency: <dependency>
- Bins: <bins> (if any)
- Env: <env vars> (if any)
  - Primary: <primaryEnv> (if provided)
- Config: <config paths> (if any)
- OS: <os restrictions> (if any)
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

Dependencies:
- Bins: <list> (if any)
- Env: <list> (if any)
  - Primary: <primaryEnv> (if any)
- Config: <list> (if any)

To activate:
- Restart the agent, or
- Run: refresh skills (if supported)

To test:
- "<test prompt>"

Done.
```

- Show the skill path.
- If dependencies exist, list them.
- Show activation command (adapt to the agent's refresh mechanism).
- Show one natural test prompt.
- Keep it under 15 lines total.

## Full Guidance Mode

In Full mode, after step 1, follow the same sequence but ask additional optional questions between steps:

After purpose:
- "Target users? (optional, press Enter to skip)"

After trigger style:
- "User-invocable? [y/n]"
- If tool dispatch: "Tool name to dispatch to:"

After dependency:
- "Required binaries? (comma separated, optional)"
- "Environment variables? (comma separated, optional)"
- "Required config paths? (comma separated, optional)"
- "OS restrictions? (darwin/linux/win32, optional)"
- If external API: "Primary API env var name? (optional, e.g. OPENAI_API_KEY)"

After install location:
- "Homepage? (optional, press Enter to skip)"
- "Emoji? (optional, press Enter to skip)"

Skip any question if user indicates it's not needed.

## Default Values

When user skips or is unsure:

- trigger: 1 (Chat naturally)
- tool name: (none, only if tool dispatch selected)
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

### SKILL.md Frontmatter Rules

Always include at minimum:
- `name: <skill-name>`
- `description: <description>`

Include optional frontmatter only when relevant:
- `user-invocable: true` — only if the user confirms
- `disable-model-invocation: true` — only if the user confirms
- `command-dispatch: tool` — only if tool dispatch mode
- `command-tool: <name>` — only if tool dispatch mode
- `command-arg-mode: raw` — only if tool dispatch mode
- `homepage: <url>` — only if provided
- `emoji: <emoji>` — only if provided

If the skill has any gating requirements (bins, env, config, or OS), include metadata as a **single-line JSON object**:

```yaml
metadata: {"openclaw": {"requires": {"bins": ["cmd1"], "env": ["VAR_NAME"], "config": ["path.to.config"], "os": ["darwin"]}, "primaryEnv": "VAR_NAME"}}
```

The `primaryEnv` field should only be included if the user specifies a primary API key environment variable.

Format the metadata on a single line. Do not multi-line YAML objects.

### README.md Content

Always include:
- What the skill does
- How to use it
- Dependencies (if any) - including bins, env vars, config paths, OS restrictions
- If primaryEnv is set: note which env var is the primary API key
- How to activate it
- How to test it

If dependencies exist, include a "Requirements" section listing bins, env vars, config paths, and OS restrictions. Mark the primary API key env var if applicable.

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
<optional frontmatter>
---

# <Skill Name>

## Purpose

...

## When To Use

...

## How To Use

...
```

### Optional Frontmatter

Only include these if relevant:

- `user-invocable: true`
- `disable-model-invocation: true`
- `command-dispatch: tool` (only if tool dispatch mode)
- `command-tool: <tool-name>` (only if tool dispatch mode)
- `command-arg-mode: raw` (only if tool dispatch mode)

If the skill has dependencies (bins, env, config, or OS restrictions), include metadata:

```yaml
metadata: {"openclaw": {"requires": {"bins": [...], "env": [...], "config": [...], "os": [...]}}}
```

Write the body for an agent, not for an end user.

## Language

- Use English by default.
- If user clearly uses another language, switch to that language for all prompts and output.
