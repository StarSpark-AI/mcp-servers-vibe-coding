# Step 10: Google Cloud CLI

## Overview

Google Cloud CLI (gcloud) provides command-line access to Google Cloud services.

## Prerequisites

You need a Google Cloud account. If you don't have one:

1. Visit https://cloud.google.com/
2. Click "Get started for free"
3. Sign in with your Google account
4. New accounts get **$300 in free credits** valid for 90 days

> **Note:** A credit card is required for verification, but you won't be charged until you manually upgrade to a paid account.

For more details, see [Google Cloud Free Program](https://cloud.google.com/free).

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

### Browser-less Authentication (Docker/Remote Servers)

If you're on a machine without a browser (e.g., Docker, SSH):

1. Run `gcloud auth login --no-browser`
2. A URL will be printed - copy and paste it into a browser on another machine
3. Complete the authentication in the browser
4. A verification code will be displayed - copy it
5. Paste the code back into the terminal

## Verification

```bash
gcloud --version
gcloud config list
```

You should see output showing your gcloud version and configuration.
