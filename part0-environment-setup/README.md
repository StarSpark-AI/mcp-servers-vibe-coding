# Part 0: Environment Setup

Complete these setup steps in order before the workshop.

## Prerequisites

- macOS, Windows, or Linux
- Admin/sudo access for installations
- Internet connection

## Setup Steps

| Step | Tool | Description |
|------|------|-------------|
| 1 | [Warp](step1-warp/) | Modern AI-powered terminal |
| 2 | [Claude Code CLI](step2-claude-code/) | AI-assisted development |
| 3 | [Cursor](step3-cursor/) | AI-powered code editor |
| 4 | [PostgreSQL](step4-postgresql/) | Database server |
| 5 | [Conda](step5-conda/) | Python environment management |
| 6 | [Python 3.11](step6-python/) | In Conda environment |
| 7 | [Django](step7-django/) | In Conda environment |
| 8 | [Google Cloud CLI](step8-google-cloud-cli/) | Cloud services and authentication |
| 9 | [Node.js](step9-nodejs/) | JavaScript runtime |
| 10 | [React](step10-react/) | Frontend framework |

## Quick Verification

After completing all steps, run these commands to verify your setup:

```bash
# Warp - launch and verify it opens

# Claude Code
claude --version

# Cursor - launch and verify it opens

# PostgreSQL
psql --version

# Conda & Python
conda activate mcp-workshop
python --version  # Should show 3.11.x

# Django
python -m django --version

# Google Cloud CLI
gcloud --version

# Node.js
node --version  # Should show 20.x

# React tools
npx --version
```

## Troubleshooting

If you encounter issues with any step, refer to the detailed README in each step folder.
