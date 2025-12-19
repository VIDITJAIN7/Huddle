# Detailed Setup Guide

This guide provides step-by-step instructions for setting up the Huddle app for development and deployment.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Environment Setup](#environment-setup)
- [Project Installation](#project-installation)
- [Forge Configuration](#forge-configuration)
- [Development Workflow](#development-workflow)
- [Deployment](#deployment)
- [Troubleshooting](#troubleshooting)

---

## Prerequisites

### Required Software

| Software | Version | Purpose |
|----------|---------|---------|
| Node.js | 20.x or 22.x | JavaScript runtime |
| npm | 8.x+ | Package manager |
| Forge CLI | Latest | Atlassian development platform |
| Git | Any | Version control |

### Atlassian Requirements

- **Atlassian Account**: [Create one here](https://id.atlassian.com/signup)
- **Atlassian Cloud Site**: With Confluence access
- **API Token**: For Forge CLI authentication

---

## Environment Setup

### 1. Install Node.js

Download and install Node.js from [nodejs.org](https://nodejs.org/):

```bash
# Verify installation
node --version  # Should be v20.x or v22.x
npm --version   # Should be 8.x or higher
```

### 2. Install Forge CLI

```bash
npm install -g @forge/cli

# Verify installation
forge --version
```

### 3. Authenticate with Atlassian

```bash
forge login
```

This will open a browser window to authenticate. Follow the prompts to:
1. Log in to your Atlassian account
2. Create an API token
3. Paste the token back into the CLI

---

## Project Installation

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/huddle.git
cd huddle
```

### 2. Install Root Dependencies

```bash
npm install
```

This installs the Forge runtime dependencies.

### 3. Install Frontend Dependencies

```bash
cd frontend
npm install
```

### 4. Build the Frontend

```bash
npm run build
```

This compiles the React app to `static/hello-world/build/`.

---

## Forge Configuration

### Understanding manifest.yml

The `manifest.yml` file defines your Forge app configuration:

```yaml
modules:
  macro:
    - key: huddle-hello-world
      resource: main
      resolver:
        function: resolver
      title: Huddle
      description: Start a video huddle with live transcription
  function:
    - key: resolver
      handler: index.handler

resources:
  - key: main
    path: static/hello-world/build

permissions:
  scopes:
    - storage:app                    # Store meeting state
    - read:confluence-content.all    # Read page info
    - write:confluence-content       # Add comments/content
  external:
    fetch:
      backend:
        - '*.atlassian.net'          # Confluence API
      client:
        - 'https://jitsi.riot.im'    # Jitsi video
        - 'https://*.riot.im'
    frames:
      - 'https://jitsi.riot.im'      # Embed Jitsi iframe
      - 'https://*.riot.im'
```

### Registering a New App

If starting fresh (not cloning):

```bash
forge register
```

This creates a new app ID in `manifest.yml`.

---

## Development Workflow

### Local Development with Tunnel

The Forge tunnel redirects cloud requests to your local machine:

```bash
# Terminal 1: Watch frontend changes
cd frontend
npm run dev

# Terminal 2: Run the tunnel
cd ..
forge tunnel
```

> **Note**: After frontend changes, rebuild with `npm run build` for changes to appear in the tunnel.

### Making Changes

1. **Frontend Changes**: Edit files in `frontend/src/`
2. **Backend Changes**: Edit `src/index.js`
3. **Manifest Changes**: Edit `manifest.yml` (requires redeploy)

### Testing the App

1. Go to your Confluence site
2. Open any page in edit mode
3. Type `/Huddle` to insert the macro
4. Publish the page
5. Test the huddle functionality

---

## Deployment

### Development Environment

```bash
# Build frontend
cd frontend && npm run build && cd ..

# Deploy
forge deploy
```

### First Installation

```bash
forge install
```

Select:
- Product: **Confluence**
- Site: Your Atlassian site

### Upgrading Existing Installations

After changing permissions in `manifest.yml`:

```bash
forge install --upgrade
```

### Production Deployment

```bash
# Deploy to production
forge deploy -e production

# Install on production site
forge install -e production
```

---

## Troubleshooting

### Common Issues

#### "localhost refused to connect"

The tunnel has stopped. Restart it:

```bash
forge tunnel
```

#### CSP Errors in Console

External resources are blocked. Ensure `manifest.yml` has correct permissions:

```yaml
permissions:
  external:
    frames:
      - 'https://jitsi.riot.im'
    fetch:
      client:
        - 'https://jitsi.riot.im'
```

Then redeploy and upgrade:

```bash
forge deploy
forge install --upgrade
```

#### Speech Recognition Not Working

- **Check browser**: Only Chrome, Edge, Safari support Web Speech API
- **Check microphone permissions**: Browser must have mic access
- **Check HTTPS**: Speech API requires secure context

#### "Scope does not match" Error

The app needs upgraded permissions:

```bash
forge install --upgrade
```

### Viewing Logs

```bash
# View tunnel logs (real-time during development)
forge tunnel

# View deployed app logs
forge logs
```

### Resetting App State

```bash
# Delete and reinstall
forge uninstall
forge install
```

---

## Environment Variables

No environment variables are required. All configuration is in `manifest.yml`.

---

## Next Steps

- Read [ARCHITECTURE.md](ARCHITECTURE.md) to understand the codebase
- Check the [README.md](README.md) for feature overview
- Visit [Forge Documentation](https://developer.atlassian.com/platform/forge/) for advanced topics
