# OpenCode Setup Replication Guide

Complete guide to replicate the AI-assisted development environment on a new machine.

## Prerequisites

- OpenCode installed
- Git configured
- GitHub Copilot subscription (for Claude models)
- Ollama Cloud subscription with access to: GLM-5, MiniMax M2.7, Kimi K2.5, Qwen 3.5

## Step 1: Install Plugins

### OhMyOpenAgent

```bash
# Install OhMyOpenAgent from GitHub
# Follow: https://github.com/code-yeongyu/oh-my-openagent/blob/dev/docs%2Fguide%2Finstallation.md
```

When prompted:
- **Claude**: No
- **OpenAI**: No
- **Gemini**: No
- **Copilot**: Yes
- **OpenCode Zen**: No
- **Z.ai**: Select "Ollama Cloud"

### opencode-browser

```bash
# Install from npm
npm install -g @different-ai/opencode-browser

# Build extension
cd ~/.opencode-browser/extension
npm install
npm run build

# Load in Chrome/Brave:
# 1. Go to chrome://extensions/
# 2. Enable "Developer mode"
# 3. Click "Load unpacked"
# 4. Select ~/.opencode-browser/extension
```

### Dynamic Context Pruning (DCP)

Add to `~/.config/opencode/opencode.json` in the plugins array:
```json
"@tarquinen/opencode-dcp"
```

## Step 2: Configure Skills Repository

```bash
# Clone this repository
git clone https://github.com/arielsand/my-opencode-skills.git ~/my-opencode-skills

# Create symlinks for custom skills
ln -s ~/my-opencode-skills/architecture-map ~/.config/opencode/skills/
ln -s ~/my-opencode-skills/coding-standards ~/.config/opencode/skills/
ln -s ~/my-opencode-skills/security-audit-expert ~/.config/opencode/skills/
ln -s ~/my-opencode-skills/time-tracker ~/.config/opencode/skills/
```

**Note:** The following skills come pre-installed with OpenCode superpowers (symlinked automatically):
- `algorithmic-art`
- `brainstorming`
- `browser-automation`
- `dispatching-parallel-agents`
- `documentation`
- `docx`
- `executing-plans`
- `figma-mcp`
- `finishing-a-development-branch`
- `frontend-design`
- `pdf`
- `postgres`
- `receiving-code-review`
- `requesting-code-review`
- `skill-creator`
- `subagent-driven-development`
- `systematic-debugging`
- `test-driven-development`
- `using-git-worktrees`
- `using-superpowers`
- `verification-before-completion`
- `writing-plans`
- `writing-skills`
- `xlsx`

## Step 3: Agent Model Configuration

Create `~/.config/opencode/oh-my-opencode.json`:

```json
{
  "$schema": "https://raw.githubusercontent.com/code-yeongyu/oh-my-openagent/dev/assets/oh-my-opencode.schema.json",
  "google_auth": false,
  "agents": {
    "sisyphus": {
      "model": "ollama/glm-5:cloud",
      "max_tokens_limit": 16000
    },
    "sisyphus-junior": {
      "model": "ollama/qwen3.5:cloud",
      "max_tokens_limit": 8000
    },
    "prometheus": {
      "model": "ollama/glm-5:cloud",
      "max_tokens_limit": 8000
    },
    "oracle": {
      "model": "ollama/glm-5:cloud",
      "max_tokens_limit": 12000
    },
    "metis": {
      "model": "ollama/glm-5:cloud",
      "max_tokens_limit": 8000
    },
    "momus": {
      "model": "ollama/glm-5:cloud",
      "max_tokens_limit": 8000
    },
    "hephaestus": {
      "model": "ollama/kimi-k2.5:cloud",
      "max_tokens_limit": 10000
    },
    "atlas": {
      "model": "ollama/minimax-m2.7:cloud",
      "max_tokens_limit": 6000
    },
    "multimodal-looker": {
      "model": "ollama/qwen3-vl:235b",
      "max_tokens_limit": 12000
    },
    "explore": {
      "model": "ollama/qwen3.5:cloud",
      "max_tokens_limit": 16000
    },
    "librarian": {
      "model": "ollama/qwen3.5:cloud",
      "max_tokens_limit": 16000
    }
  },
  "categories": {
    "visual-engineering": {
      "model": "ollama/qwen3-vl:235b"
    },
    "ultrabrain": {
      "model": "ollama/glm-5:cloud"
    },
    "deep": {
      "model": "ollama/kimi-k2.5:cloud"
    },
    "artistry": {
      "model": "ollama/minimax-m2.7:cloud"
    },
    "quick": {
      "model": "ollama/minimax-m2.5:cloud"
    },
    "unspecified-low": {
      "model": "ollama/qwen3.5:cloud"
    },
    "unspecified-high": {
      "model": "ollama/glm-5:cloud"
    },
    "writing": {
      "model": "ollama/minimax-m2.7:cloud"
    }
  }
}
```

### Agent Model IDs

Use these IDs in the config:
| Agent | Model ID |
|-------|----------|
| GLM-5 | `ollama/glm-5:cloud` |
| Kimi K2.5 | `ollama/kimi-k2.5:cloud` |
| Qwen 3.5 | `ollama/qwen3.5:cloud` |
| MiniMax M2.7 | `ollama/minimax-m2.7:cloud` |
| MiniMax M2.5 | `ollama/minimax-m2.5:cloud` |
| Qwen VL | `ollama/qwen3-vl:235b` |

## Step 4: DCP Configuration

Create `~/.config/opencode/dcp.jsonc`:

```jsonc
{
  "$schema": "https://raw.githubusercontent.com/Opencode-DCP/opencode-dynamic-context-pruning/master/dcp.schema.json",
  "enabled": true,
  "maxContextPercentage": 60,
  "minContextPercentage": 25,
  "includeInMaxTokens": true,
  "debugLogs": false
}
```

## Model Capabilities Reference

| Model | Active Params | SWE-Bench | Cost (per 1M tokens) | Best For |
|-------|---------------|-----------|---------------------|----------|
| **GLM-5** | 44B | 77.8% | $1.00 | Deep reasoning, complex coding, architecture decisions |
| **MiniMax M2.7** | 10B | ~78% | $0.30 | Cost efficiency, quick tasks, high intelligence |
| **MiniMax M2.5** | 10B | ~77% | $0.25 | Fast, cheap, good for quick tasks |
| **Kimi K2.5** | 32B | 76.8% | $0.60 | Multimodal tasks, agent coordination, UI work |
| **Qwen 3.5** | 17B | 76.4% | $0.18 | Efficiency, large context windows (1M tokens) |
| **Qwen VL** | 235B | - | - | Vision/multimodal tasks |

## Agent Assignments Rationale

### Primary Orchestration Layer
- **Sisyphus (GLM-5 max)** - Maximum context for complex multi-agent orchestration
- **Prometheus (GLM-5)** - Deep planning with sophisticated reasoning

### Consultation Layer
- **Oracle (GLM-5)** - Read-only high-IQ consultation for architecture/debugging
- **Metis (GLM-5)** - Pre-planning analysis, ambiguity resolution
- **Momus (GLM-5)** - Work plan evaluation and quality review

### Execution Layer
- **Hephaestus (Kimi K2.5)** - Autonomous deep work with goal-oriented execution
- **Atlas (MiniMax M2.7)** - Fast parallel dispatch for quick tasks
- **Explore (Qwen 3.5)** - Efficient codebase exploration with 1M context
- **Multimodal-Looker (Qwen VL)** - Visual and multimodal analysis

### Helper Layer
- **Sisyphus-Junior (Qwen 3.5)** - Lightweight coordination assistance
- **Librarian (Qwen 3.5)** - Information retrieval and research

## Category Assignments

| Category | Model | Rationale |
|----------|-------|-----------|
| visual-engineering | Qwen VL | Multimodal excellence for UI/UX |
| ultrabrain | GLM-5 | Maximum reasoning for hard problems |
| deep | Kimi K2.5 | Goal-oriented autonomous problem-solving |
| artistry | MiniMax M2.7 | Creative with multimodal capabilities |
| quick | MiniMax M2.5 | Cheapest per token, very fast |
| unspecified-low | Qwen 3.5 | Efficient for simple tasks |
| unspecified-high | GLM-5 | Complex tasks need deep reasoning |
| writing | MiniMax M2.7 | Efficient for documentation |

### Category Descriptions

| Category | Purpose |
|----------|---------|
| **visual-engineering** | Frontend, UI/UX, design, styling, animation |
| **ultrabrain** | Genuinely hard, logic-heavy tasks - deep reasoning |
| **deep** | Goal-oriented autonomous problem-solving, thorough research before action |
| **artistry** | Complex problem-solving with unconventional, creative approaches |
| **quick** | Trivial tasks - single file changes, typo fixes, simple modifications |
| **unspecified-low** | Tasks that don't fit other categories, low effort required |
| **unspecified-high** | Tasks that don't fit other categories, high effort required |
| **writing** | Documentation, prose, technical writing |

## Verification

After setup, verify everything works:

```bash
# Check OpenCode config
cat ~/.config/opencode/opencode.json

# Check agent configuration
cat ~/.config/opencode/oh-my-opencode.json

# Check DCP configuration
cat ~/.config/opencode/dcp.jsonc

# List installed skills
ls -la ~/.config/opencode/skills/

# Test a skill
# In OpenCode: "Use the architecture-map skill on this project"
```

## Troubleshooting

### Skills not loading
- Verify symlinks point to correct directories
- Restart OpenCode completely
- Check file permissions

### Agent model not found
- Verify model IDs match the format `ollama/{model}:cloud`
- Check Ollama Cloud subscription is active
- Try the model in OpenCode settings first

### Browser extension not working
- Verify extension is loaded in chrome://extensions/
- Check Developer mode is enabled
- Try reloading the extension

## Updates

To update skills and configuration:

```bash
cd ~/my-opencode-skills
git pull
```

For plugin updates, check the respective repositories:
- https://github.com/code-yeongyu/oh-my-openagent
- https://github.com/different-ai/opencode-browser
- https://github.com/Opencode-DCP/opencode-dynamic-context-pruning