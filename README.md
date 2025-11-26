# MacOverlay

A lightweight macOS menu bar app that displays customizable overlay notifications triggered by webhooks.

## Features

- **Webhook-Triggered Notifications** - Send POST requests to display beautiful overlay notifications
- **Customizable Appearance** - Control colors, position, duration, opacity, and more
- **Video Support** - Display video content in notifications via HTTP streaming
- **Sound Effects** - Multiple built-in notification sounds
- **Global Hotkeys** - Quick access via customizable keyboard shortcuts
- **Auto-Updates** - Stay current with Sparkle-powered automatic updates

## Installation

1. Download the latest `MacOverlay.dmg` from [Releases](https://github.com/patrickohemeng/macoverlay-releases/releases)
2. Open the DMG and drag MacOverlay to your Applications folder
3. Launch MacOverlay from Applications or Launchpad

## Usage

### Sending Notifications

MacOverlay listens for webhook requests on `http://localhost:9999/notify` (port configurable).

**Simple notification:**
```bash
curl -X POST http://localhost:9999/notify \
  -H "Content-Type: application/json" \
  -d '{"title": "Hello", "message": "World"}'
```

**Full-featured notification:**
```bash
curl -X POST http://localhost:9999/notify \
  -H "Content-Type: application/json" \
  -d '{
    "title": "New Message",
    "message": "You have a new notification",
    "subtitle": "From MacOverlay",
    "color": "#4CAF50",
    "duration": 5,
    "sound": "glass",
    "position": "top-right"
  }'
```

### Webhook Payload Options

| Field | Type | Description |
|-------|------|-------------|
| `title` | string | **Required.** Notification title |
| `message` | string | **Required.** Main message content |
| `subtitle` | string | Optional subtitle |
| `color` | string | Hex color for accent (e.g., "#FF5733") |
| `duration` | number | Display duration in seconds |
| `sound` | string | Sound effect: "glass", "ping", "tink", "basso", "purr" |
| `position` | string | Screen position: "top-right", "top-left", "bottom-right", "bottom-left", "center" |
| `opacity` | number | Window opacity (0.0 - 1.0) |
| `videoUrl` | string | HTTP URL for video content |

### Accessing Settings

- **Menu Bar:** Click the bell icon in the menu bar
- **Dock:** Right-click the MacOverlay icon in the Dock
- **Help Menu:** MacOverlay > Help > Check for Updates
- **Keyboard:** Use your configured global hotkey (default: Cmd+Shift+O)

## System Requirements

- macOS 13.0 (Ventura) or later
- Apple Silicon or Intel Mac

## Auto-Updates

MacOverlay automatically checks for updates and notifies you when a new version is available. You can also manually check via:
- Menu bar > Check for Updates
- Dock right-click menu > Check for Updates
- Help menu > Check for Updates

## Privacy

MacOverlay runs entirely locally on your Mac. No data is sent to external servers. The webhook listener only accepts connections from localhost by default.

## License

Copyright 2024 MacOverlay. All rights reserved.

---

**Note:** This repository contains only release binaries. The source code is maintained in a private repository.
