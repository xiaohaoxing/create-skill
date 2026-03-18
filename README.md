# create-skill

Create a new skill through an interactive CLI-style flow.

## Quick Start

```
You: Help me create a skill
Agent: Create a new skill.

1) Quick guidance
2) Full guidance

Choice [1]:
```

## Flow

1. Choose guidance mode (1 or 2)
2. Answer questions one by one
3. Confirm summary
4. Confirm file creation
5. Get activation instructions

## Mode

- **Quick**: Minimal questions, sensible defaults
- **Full**: More options to configure

## Compatible Agents

Works with any AgentSkills-compatible agent:
- OpenClaw
- Claude Code
- Codex
- Pi
- Other agents with skill support

## Output

Creates in your chosen location:
- `SKILL.md` (AgentSkills format)
- `README.md` (human-readable)

## Activation

After creation, the agent shows:
- Where the skill was written
- How to activate it (restart or refresh)
- A test prompt to try
