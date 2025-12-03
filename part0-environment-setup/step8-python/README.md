# Step 8: Python 3.11 (in Conda Environment)

## Overview

Python 3.11 is required for this workshop. We use the version installed via Conda.

## Installation

Python 3.11 was installed when you created the Conda environment in Step 7.

```bash
# Activate the workshop environment
conda activate mcp-workshop

# Verify Python version
python --version
```

## Additional Python Tools

```bash
# Make sure environment is activated
conda activate mcp-workshop

# Upgrade pip
pip install --upgrade pip

# Install common development tools
pip install ipython

# Install PostgreSQL driver
pip install psycopg2-binary
```

## Verification

```bash
# Activate environment first
conda activate mcp-workshop

# Check Python version (should show 3.11.x)
python --version

# Check pip
pip --version

# Test Python REPL
python -c "print('Python is working!')"
```

## Troubleshooting

If `python` points to a different version:

```bash
# Make sure conda environment is activated
conda activate mcp-workshop

# Check which python is being used
which python
```

The path should point to your Conda environment, e.g.:
`/Users/YOUR_USER/miniconda3/envs/mcp-workshop/bin/python`
