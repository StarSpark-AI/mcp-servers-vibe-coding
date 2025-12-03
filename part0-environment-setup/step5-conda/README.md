# Step 5: Conda

## Overview

Conda is a package and environment manager that simplifies Python dependency management.

## Installation

### macOS / Linux

```bash
# Download Miniconda installer
curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-arm64.sh  # Apple Silicon
# OR
curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh  # Intel Mac
# OR
curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh  # Linux

# Run installer
bash Miniconda3-latest-*.sh

# Follow prompts and restart terminal
```

### Windows

Download and run the installer from: https://docs.conda.io/en/latest/miniconda.html

## Configuration

```bash
# Initialize conda for your shell
conda init

# Restart your terminal, then update conda
conda update conda
```

## Create Workshop Environment

```bash
# Create a new environment for this workshop with Python 3.11
conda create -n mcp-workshop python=3.11

# Activate the environment
conda activate mcp-workshop
```

## Verification

```bash
conda --version
conda env list
```

You should see the `mcp-workshop` environment in the list.

## Next Steps

Continue to Step 6 (Python) and Step 7 (Django) to complete your Python environment setup.
