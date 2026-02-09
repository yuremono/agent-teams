<div align="center">

# Agent Teams + tmux Infrastructure

**Orchestrate multiple AI agents with Claude Code's official Agent Teams feature.**

A multi-agent parallel development infrastructure combining Claude Code's Agent Teams with tmux notifications.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Built_for-Claude_Code-blueviolet)](https://code.claude.com)
[![tmux](https://img.shields.io/badge/tmux-optional-green)](https://github.com/tmux/tmux)

[English](README.md) | [日本語](README_ja.md)

</div>

---

## What is this?

**Agent Teams + tmux Infrastructure** is a multi-agent parallel development platform that combines Claude Code's official Agent Teams feature with tmux notifications.

**Why use it?**
- Flexible team composition with Agent Teams
- Notify other sessions/users via tmux
- Role separation with custom agent definitions
- Cross-session knowledge accumulation with Memory MCP

```
      You (User)
           │
           ▼ Commands
    ┌─────────────┐
    │   Leader    │  ← Team composition, task assignment
    └──────┬──────┘
           │ Task tool
      ┌────▼────┐
      │  Agent  │  ← researcher, implementer, reviewer, general...
      │  Team   │
      └────┬────┘
           │
           ▼ tmux notification (optional)
        Notify other teams/users
```

---

## Architecture

### Flat Structure (Team-Based)

| Role | Description |
|------|-------------|
| **Leader** | Team composition, task assignment, progress management |
| **researcher** | Research, information gathering, analysis |
| **implementer** | Implementation, coding |
| **reviewer** | Review, verification |
| **general** | General tasks |

### Team Composition Examples

| Type | Recommended Members |
|------|---------------------|
| Research & Analysis | researcher + general |
| Development | implementer + reviewer |
| Full Cycle | researcher + implementer + reviewer + general |

---

## Quick Start

### Prerequisites

- [Claude Code](https://claude.ai) installed
- tmux installed (optional)

### Step 1: Clone Repository

```bash
git clone <repository-url>
cd 0205multi-agent-shogun
```

### Step 2: Setup

```bash
./setup.sh
```

### Step 3: Verify Custom Agents

```bash
# Check for agent definitions in ~/.claude/agents/
ls ~/.claude/agents/
# researcher.md, implementer.md, reviewer.md, general.md, etc.
```

### Step 4: Create Team and Run Tasks

Inside Claude Code:

```python
# Create a team
TeamCreate(
    team_name="my-project",
    description="My project team"
)

# Add members (parallel execution)
Task(
    subagent_type="researcher",
    team_name="my-project",
    description="Research task",
    prompt="Research about..."
)

Task(
    subagent_type="implementer",
    team_name="my-project",
    description="Implementation task",
    prompt="Implement..."
)
```

---

## Project Management

Projects are managed in `WORKS/{MMDD}{ProjectName}/` format.

```
config/projects.yaml       # Project list
WORKS/                      # Project root
WORKS/0209ExampleProject/   # Example project
  ├── project.yaml         # Project details
  ├── src/                 # Source code
  └── docs/                # Documentation
```

---

## tmux Integration (Optional)

When sending notifications from agents within Agent Teams to other tmux panes:

### send-keys Rule

```bash
# [1st] Send message
tmux send-keys -t session:pane 'message'
# [2nd] Send Enter
tmux send-keys -t session:pane Enter
```

### Notification Script

```bash
bin/notify-team "Task completed"
```

---

## Configuration

### config/settings.yaml

```yaml
language: standard  # standard, ja
```

### CLAUDE.md

The `CLAUDE.md` in the project root is **for the Leader (main agent) only**.

- Team composition rules
- tmux integration methods
- Project management rules

Team members should read `~/.claude/agents/{name}.md`.

---

## MCP Tools

Available MCPs:
- **Memory**: Cross-session knowledge accumulation
- **Notion**: Notion integration
- **Playwright**: E2E testing
- **GitHub**: GitHub integration
- **Sequential Thinking**: Complex reasoning

---

## Directory Structure

```
├── CLAUDE.md              # Leader-only rules
├── README.md              # This file
├── config/                # Configuration files
├── context/               # Project-specific context
├── bin/                   # Utility scripts
├── WORKS/                 # Project directory (not in Git)
└── .claude/               # Project-specific skills/commands
```

---

## Troubleshooting

See [TROUBLESHOOTING.md](TROUBLESHOOTING.md) for details.

---

## License

[MIT License](LICENSE)
