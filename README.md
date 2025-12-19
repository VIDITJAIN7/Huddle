# Huddle - Video Conferencing for Confluence

![Huddle Banner](https://img.shields.io/badge/Atlassian-Forge-blue?style=for-the-badge&logo=atlassian)
![React](https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react)
![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?style=for-the-badge&logo=typescript)

A Forge-powered Confluence macro that enables **instant video meetings** with **live transcription** directly within your Confluence pages.

## ✨ Features

- 🎥 **Embedded Video Conferencing** - Jitsi Meet integration within Confluence
- 📝 **Live Transcription** - Real-time speech-to-text using Web Speech API
- 💬 **In-Meeting Chat** - Send messages during the huddle
- ⏱️ **Meeting Timer** - Track meeting duration
- 🔗 **External Link** - Open meeting in a new tab for full controls
- 👥 **Join/Start Flow** - See when a huddle is active and join with one click

## � Documentation

| Document | Description |
|----------|-------------|
| [README.md](README.md) | Overview and quick start |
| [SETUP.md](SETUP.md) | Detailed setup and installation guide |
| [ARCHITECTURE.md](ARCHITECTURE.md) | Technical architecture and design decisions |

## �📋 Prerequisites

- [Node.js](https://nodejs.org/) v20.x or v22.x
- [Forge CLI](https://developer.atlassian.com/platform/forge/getting-started/) installed globally
- An Atlassian Cloud site with Confluence

## 🚀 Quick Start

### 1. Install Dependencies

```bash
# Root directory
npm install

# Frontend directory
cd frontend
npm install
```

### 2. Build the Frontend

```bash
cd frontend
npm run build
```

### 3. Deploy to Forge

```bash
forge deploy
```

### 4. Install on Your Site

```bash
forge install
```

Select your Confluence site when prompted.

### 5. Use the Macro

1. Open any Confluence page
2. Type `/Huddle` to insert the macro
3. Click **Start Huddle** to begin a meeting

## 🛠️ Development

### Local Development with Tunnel

```bash
# Build frontend first
cd frontend
npm run build

# Start the tunnel from root directory
cd ..
forge tunnel
```

The tunnel redirects requests to your local machine for real-time development.

### Project Structure

```
Huddle/
├── frontend/                 # React frontend (Custom UI)
│   ├── src/
│   │   ├── components/
│   │   │   ├── HuddleDashboard.tsx    # Main meeting interface
│   │   │   ├── HuddleInvitation.tsx   # Start/Join screen
│   │   │   └── ui/                    # UI components
│   │   ├── hooks/
│   │   │   └── useSpeechRecognition.ts # Speech-to-text hook
│   │   ├── pages/
│   │   │   └── Index.tsx              # Main page with state management
│   │   └── App.tsx
│   └── vite.config.ts
├── src/
│   └── index.js              # Forge backend resolvers
├── manifest.yml              # Forge app configuration
└── package.json
```

### Key Files

| File | Purpose |
|------|---------|
| `manifest.yml` | Forge app configuration, permissions, and modules |
| `src/index.js` | Backend resolver functions (startMeeting, stopMeeting, getMeetingStatus) |
| `frontend/src/pages/Index.tsx` | Main app logic and state management |
| `frontend/src/components/HuddleDashboard.tsx` | Video meeting interface with Jitsi iframe |
| `frontend/src/hooks/useSpeechRecognition.ts` | Web Speech API integration for transcription |

## ⚙️ Configuration

### Permissions (manifest.yml)

```yaml
permissions:
  scopes:
    - storage:app              # Store meeting state
    - read:confluence-content.all
    - write:confluence-content
  external:
    fetch:
      client:
        - 'https://jitsi.riot.im'
        - 'https://*.riot.im'
    frames:
      - 'https://jitsi.riot.im'
      - 'https://*.riot.im'
```

## 🔧 How It Works

1. **Start Huddle**: Creates a meeting entry in Forge Storage with a unique room name
2. **Jitsi Integration**: Embeds `jitsi.riot.im` via iframe for video conferencing
3. **Transcription**: Uses browser's Web Speech Recognition API (Chrome/Edge/Safari)
4. **State Sync**: Other users see "Join Huddle" when a meeting is active
5. **End Huddle**: Clears meeting from storage

## ⚠️ Known Limitations

- **Transcription Independence**: Speech recognition uses a separate microphone stream from Jitsi. Muting in Jitsi doesn't pause transcription (use the manual toggle).
- **Browser Support**: Live transcription requires Chrome, Edge, or Safari. Firefox has limited support.
- **Jitsi Controls**: Due to Forge CSP restrictions, custom buttons cannot control Jitsi directly. Use Jitsi's built-in controls.

## 📦 Deployment

```bash
# Build frontend
cd frontend && npm run build

# Deploy to development
forge deploy

# Deploy to production
forge deploy -e production

# Upgrade existing installations
forge install --upgrade
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Commit your changes: `git commit -m 'Add amazing feature'`
4. Push to the branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Resources

- [Atlassian Forge Documentation](https://developer.atlassian.com/platform/forge/)
- [Forge Custom UI](https://developer.atlassian.com/platform/forge/custom-ui/)
- [Jitsi Meet](https://jitsi.org/)
- [Web Speech API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API)
