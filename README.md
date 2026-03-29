# My OpenCode Skills

Custom skills for OpenCode AI assistant that I've developed and use across my projects.

## Skills Included

| Skill | Description |
|-------|-------------|
| `architecture-map` | Generates `PROJECT_ARCHITECTURE.md` - a comprehensive architectural map for AI context |
| `coding-standards` | Generates `CODING_STANDARDS.md` - coding conventions and best practices for teams |
| `dev-time-tracker` | Tracks development session time and updates Excel timesheets with session details |

## Installation

### Option 1: Clone and symlink (recommended)

```bash
# Clone this repository
git clone https://github.com/arielsand/my-opencode-skills.git ~/my-opencode-skills

# Create symlinks to OpenCode skills directory
ln -s ~/my-opencode-skills/architecture-map ~/.config/opencode/skills/
ln -s ~/my-opencode-skills/coding-standards ~/.config/opencode/skills/
ln -s ~/my-opencode-skills/dev-time-tracker ~/.config/opencode/skills/

# Restart OpenCode to load the skills
```

### Option 2: Manual copy

```bash
# Clone and copy
git clone https://github.com/arielsand/my-opencode-skills.git
cp -r my-opencode-skills/architecture-map ~/.config/opencode/skills/
cp -r my-opencode-skills/coding-standards ~/.config/opencode/skills/
```

## Usage

### architecture-map

Run on any project to generate `PROJECT_ARCHITECTURE.md`:

```
Use the architecture-map skill on this project
```

Or simply start working on a new project and it will trigger automatically.

**What it does:**
- Analyzes project configuration (package.json, composer.json, etc.)
- Maps directory structure and identifies patterns
- Recognizes naming conventions and architecture patterns
- Documents data flow and critical business logic locations
- Updates AGENTS.md to reference the architecture map

### coding-standards

Run to generate `CODING_STANDARDS.md`:

```
Use the coding-standards skill on this project
```

**What it does:**
- Discovers tech stack from config files
- Analyzes existing code patterns (naming, imports, styles)
- Asks user for preferences on non-inferable decisions
- Generates comprehensive standards document (15+ sections)
- Updates AGENTS.md to reference coding standards

### dev-time-tracker

Track development sessions and update Excel timesheets:

```
Log my development time for this session
```

Or just mention:
```
I finished working, record my time
```

**What it does:**
- Calculates session duration from conversation context
- Extracts deliverables and focus from the work done
- Appends entry to `{project}/docs/development_time.xlsx`
- Creates new timesheet if one doesn't exist
- Shows summary of logged time

## Skills Structure

Each skill follows this structure:

```
skill-name/
├── SKILL.md          # Skill definition (loaded by OpenCode)
└── README.md         # Documentation (optional)
```

## Syncing Across Machines

If you use the symlink method (Option 1), keeping skills updated is simple:

```bash
cd ~/my-opencode-skills
git pull
```

## Contributing

Feel free to fork and adapt these skills to your needs. If you make improvements, PRs are welcome!

## Full Setup Guide

For complete OpenCode environment replication, see [SETUP.md](SETUP.md) which includes:
- Plugin installation (OhMyOpenAgent, opencode-browser, DCP)
- Agent model configuration (GLM-5, MiniMax M2.7, Kimi K2.5, Qwen 3.5)
- Skills repository setup
- Troubleshooting guide

## License

MIT License - feel free to use and modify as you wish.