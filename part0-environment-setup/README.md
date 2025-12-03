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
| 2 | [Node.js](step2-nodejs/) | JavaScript runtime |
| 3 | [React](step3-react/) | Frontend framework |
| 4 | [Claude Code CLI](step4-claude-code/) | AI-assisted development |
| 5 | [Cursor](step5-cursor/) | AI-powered code editor |
| 6 | [PostgreSQL](step6-postgresql/) | Database server |
| 7 | [Conda](step7-conda/) | Python environment management |
| 8 | [Python 3.11](step8-python/) | In Conda environment |
| 9 | [Django](step9-django/) | In Conda environment |
| 10 | [Google Cloud CLI](step10-google-cloud-cli/) | Cloud services and authentication |

## Quick Verification

After completing all steps, run these commands to verify your setup:

```bash
# Warp - launch and verify it opens

# Node.js
node --version  # Should show 20.x

# React tools
npx --version

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
```

## Testing with Docker (Linux)

To test the setup instructions in a Linux environment:

```bash
cd part0-environment-setup

# Build and run the container
docker compose up -d

# Enter the container
docker compose exec workshop-setup bash

# You're now in a Linux environment - follow the setup steps!
# Instructions are in ~/setup-instructions/

# When done, exit and clean up
exit
docker compose down
```

## Troubleshooting

If you encounter issues with any step, refer to the detailed README in each step folder.
