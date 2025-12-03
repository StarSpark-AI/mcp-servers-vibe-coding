# Step 3: Python 3.11

## Overview

Python 3.11 is required for this workshop. We'll use the version installed via Conda.

## Installation

If you followed Step 2, Python 3.11 is already installed in your Conda environment.

```bash
# Activate the workshop environment
conda activate mcp-workshop

# Verify Python version
python --version
```

## Additional Python Tools

```bash
# Upgrade pip
pip install --upgrade pip

# Install common development tools
pip install ipython
```

## Verification

```bash
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
