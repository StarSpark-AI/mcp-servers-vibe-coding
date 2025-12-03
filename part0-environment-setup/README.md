# Part 0: Environment Setup

Complete these setup steps in order before the workshop.

## Prerequisites

- Code editor (VS Code recommended)
- Terminal/Command line access
- Internet connection

## Setup Steps

| Step | Tool | Description |
|------|------|-------------|
| 1 | [Google Cloud CLI](step1-google-cloud-cli/) | Cloud services and authentication |
| 2 | [Conda](step2-conda/) | Python environment management |
| 3 | [Python 3.11](step3-python/) | Via Conda environment |
| 4 | [PostgreSQL](step4-postgresql/) | Database server |
| 5 | [Django](step5-django/) | Python web framework |
| 6 | [Node.js](step6-nodejs/) | JavaScript runtime |
| 7 | [React](step7-react/) | Frontend framework |
| 8 | [Claude Code CLI](step8-claude-code/) | AI-assisted development |

## Quick Verification

After completing all steps, run these commands to verify your setup:

```bash
# Google Cloud CLI
gcloud --version

# Conda & Python
conda activate mcp-workshop
python --version  # Should show 3.11.x

# PostgreSQL
psql --version

# Django
python -m django --version

# Node.js
node --version  # Should show 20.x

# React tools
npx --version

# Claude Code
claude --version
```

## Troubleshooting

If you encounter issues with any step, refer to the detailed README in each step folder.
