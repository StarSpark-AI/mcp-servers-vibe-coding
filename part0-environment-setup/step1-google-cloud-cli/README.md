# Step 1: Google Cloud CLI

## Overview

Google Cloud CLI (gcloud) provides command-line access to Google Cloud services.

## Installation

### macOS

```bash
# Using Homebrew
brew install --cask google-cloud-sdk
```

### Windows

Download and run the installer from: https://cloud.google.com/sdk/docs/install

### Linux

```bash
# Add the Cloud SDK distribution URI as a package source
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list

# Import the Google Cloud public key
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -

# Update and install
sudo apt-get update && sudo apt-get install google-cloud-cli
```

## Configuration

```bash
# Initialize gcloud
gcloud init

# Login to your Google account
gcloud auth login

# Set your default project
gcloud config set project YOUR_PROJECT_ID
```

## Verification

```bash
gcloud --version
gcloud config list
```

You should see output showing your gcloud version and configuration.
