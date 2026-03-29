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

# Create symlinks
ln -s ~/my-opencode-skills/architecture-map ~/.config/opencode/skills/
ln -s ~/my-opencode-skills/coding-standards ~/.config/opencode/skills/
ln -s ~/my-opencode-skills/dev-time-tracker ~/.config/opencode/skills/
ln -s ~/my-opencode-skills/frontend-design ~/.config/opencode/skills/
ln -s ~/my-opencode-skills/docx ~/.config/opencode/skills/
ln -s ~/my-opencode-skills/pdf ~/.config/opencode/skills/
ln -s ~/my-opencode-skills/xlsx ~/.config/opencode/skills/
ln -s ~/my-opencode-skills/skill-creator ~/.config/opencode/skills/
```

## Step 3: Agent Model Configuration

Create `~/.config/opencode/oh-my-opencode.json`:

```json
{
  "agents": {
    "Sisyphus": {
      "provider": "ollama-cloud",
      "model": "glm-5",
      "max_tokens_limit": 16000
    },
    "Sisyphus-Junior": {
      "provider": "ollama-cloud",
      "model": "qwen3.5:397b",
      "max_tokens_limit": 8000
    },
    "Prometheus": {
      "provider": "ollama-cloud",
      "model": "glm-5",
      "max_tokens_limit": 8000
    },
    "Oracle": {
      "provider": "ollama-cloud",
      "model": "glm-5",
      "max_tokens_limit": 12000
    },
    "Metis": {
      "provider": "ollama-cloud",
      "model": "glm-5",
      "max_tokens_limit": 8000
    },
    "Momus": {
      "provider": "ollama-cloud",
      "model": "glm-5",
      "max_tokens_limit": 8000
    },
    "Hephaestus": {
      "provider": "ollama-cloud",
      "model": "kimi-k2.5",
      "max_tokens_limit": 10000
    },
    "Atlas": {
      "provider": "ollama-cloud",
      "model": "minimax-m2.7",
      "max_tokens_limit": 6000
    },
    "Multimodal-Looker": {
      "provider": "ollama-cloud",
      "model": "kimi-k2.5",
      "max_tokens_limit": 12000
    },
    "Explore": {
      "provider": "ollama-cloud",
      "model": "qwen3.5:397b",
      "max_tokens_limit": 16000
    }
  },
  "categories": {
    "visual-engineering": {
      "provider": "ollama-cloud",
      "model": "kimi-k2.5"
    },
    "ultrabrain": {
      "provider": "ollama-cloud",
      "model": "glm-5"
    },
    "artistry": {
      "provider": "ollama-cloud",
      "model": "kimi-k2.5"
    },
    "quick": {
      "provider": "ollama-cloud",
      "model": "minimax-m2.7"
    },
    "unspecified-low": {
      "provider": "ollama-cloud",
      "model": "qwen3.5:397b"
    },
    "unspecified-high": {
      "provider": "ollama-cloud",
      "model": "glm-5"
    },
    "writing": {
      "provider": "ollama-cloud",
      "model": "qwen3.5:397b"
    }
  }
}
```

## Step 4: DCP Configuration

Create `~/.config/opencode/dcp.jsonc`:

```jsonc
{
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
| **Kimi K2.5** | 32B | 76.8% | $0.60 | Multimodal tasks, agent coordination, UI work |
| **Qwen 3.5** | 17B | 76.4% | $0.18 | Efficiency, large context windows (1M tokens) |

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
- **Multimodal-Looker (Kimi K2.5)** - Visual and multimodal analysis

### Helper Layer
- **Sisyphus-Junior (Qwen 3.5)** - Lightweight coordination assistance

## Category Assignments

| Category | Model | Rationale |
|----------|-------|-----------|
| visual-engineering | Kimi K2.5 | Multimodal excellence for UI/UX |
| ultrabrain | GLM-5 | Maximum reasoning for hard problems |
| artistry | Kimi K2.5 | Creative with multimodal capabilities |
| quick | MiniMax M2.7 | Cheapest per token, very intelligent |
| unspecified-low | Qwen 3.5 | Efficient for simple tasks |
| unspecified-high | GLM-5 | Complex tasks need deep reasoning |
| writing | Qwen 3.5 | Efficient for documentation |

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
- Model IDs in Ollama Cloud:
  - `ollama-cloud/glm-5`
  - `ollama-cloud/kimi-k2.5`
  - `ollama-cloud/qwen3.5:397b` (note: qwen3.5, not qwen-3.5)
  - `ollama-cloud/minimax-m2.7`

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