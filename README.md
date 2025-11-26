# MacOverlay

A powerful macOS menu bar app that displays customizable overlay notifications triggered by webhooks. Perfect for smart home integrations, monitoring systems, and custom alerts.

> **Note:** This project is actively being developed. If you encounter any issues or have feature requests, please [open an issue](https://github.com/patrickohemeng/macoverlay-releases/issues) and I'll address them as soon as possible.

## Features

- **Webhook-Triggered Notifications** - Send POST requests to display beautiful overlay notifications
- **Live Video Previews** - Stream video content directly in notifications (HTTP/HLS)
- **Image Support** - Display images in notifications
- **Progress Bars** - Show progress indicators in notifications
- **Action Buttons** - Add clickable buttons that open URLs or trigger actions
- **Multi-Monitor Support** - Choose which display to show notifications on, or show on all displays
- **Fully Customizable** - Control every aspect of notification appearance and behavior
- **Home Assistant Integration** - Seamlessly integrate with your smart home
- **Multiple Sound Effects** - Built-in notification sounds
- **Global Hotkeys** - Quick access via customizable keyboard shortcuts
- **Video Shortcuts** - Trigger specific video streams with dedicated hotkeys
- **Launch on Startup** - Optionally start MacOverlay when you log in
- **Notification History** - Keep track of past notifications
- **Auto-Updates** - Stay current with Sparkle-powered automatic updates

## Installation

1. Download the latest `MacOverlay.dmg` from [Releases](https://github.com/patrickohemeng/macoverlay-releases/releases)
2. Open the DMG and drag MacOverlay to your Applications folder
3. Launch MacOverlay from Applications or Launchpad

## Quick Start

MacOverlay listens for webhook requests on `http://localhost:5053/notify` by default.

**Send your first notification:**
```bash
curl -X POST http://localhost:5053/notify \
  -H "Content-Type: application/json" \
  -d '{"title": "Hello", "message": "MacOverlay is working!"}'
```

## Webhook API

### Endpoint
```
POST http://localhost:5053/notify
Content-Type: application/json
```

### Payload Options

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `title` | string | *required* | Notification title |
| `message` | string | *required* | Main message content |
| `subtitle` | string | - | Optional subtitle below the title |
| `color` | string | "#007AFF" | Accent color (hex format) |
| `duration` | number | 5 | Display duration in seconds |
| `sound` | string | - | Sound effect: `glass`, `ping`, `tink`, `basso`, `purr` |
| `position` | string | "top-right" | Screen position (see below) |
| `imageUrl` | string | - | HTTP URL for an image to display |
| `videoUrl` | string | - | HTTP/HLS URL for live video stream |
| `progress` | number | - | Progress bar value (0.0 - 1.0) |
| `actions` | array | - | Action buttons (see below) |

### Position Options
- `top-right` (default)
- `top-left`
- `bottom-right`
- `bottom-left`
- `center`

### Action Buttons
Add clickable buttons to notifications:
```json
{
  "title": "Download Complete",
  "message": "Your file is ready",
  "actions": [
    {"title": "Open File", "url": "file:///path/to/file"},
    {"title": "Open Folder", "url": "file:///path/to/folder"}
  ]
}
```

### Progress Bar
Show a progress indicator:
```json
{
  "title": "Uploading",
  "message": "backup.zip",
  "progress": 0.75
}
```

## Customization Settings

Access settings via the menu bar icon, Dock right-click menu, or your configured hotkey.

### Display
- **Target Display** - Choose which monitor to show notifications on
- **Show on All Displays** - Display notifications on every connected monitor
- **Screen Position** - Where notifications appear on screen

### Appearance
- **Accent Color** - Highlight and border color
- **Corner Radius** - Rounded corner size
- **Opacity** - Window transparency level (0.0 - 1.0)
- **Shadow** - Enable/disable window shadow
- **Title Position** - Show title at top or bottom of notification

### Animation
- **Animation Style** - Choose from: Slide, Fade, Scale, or None
- **Animation Duration** - How fast animations play

### Behavior
- **Default Duration** - How long notifications display
- **Click to Dismiss** - Dismiss notification by clicking on it
- **Pause on Hover** - Pause auto-dismiss timer when mouse hovers over notification
- **Notification History** - Keep track of past notifications
- **Max History Items** - How many notifications to remember

### Sound
- **Sound Enabled** - Play sounds for notifications
- **Default Sound** - Sound effect for all notifications (glass, ping, tink, basso, purr)

### Network
- **Port** - Webhook listener port (default: 5053)

### Startup
- **Launch on Login** - Automatically start MacOverlay when you log in

### Hotkeys
- **Settings Hotkey** - Global keyboard shortcut to open settings (default: Cmd+Option+O)
- **Video Shortcuts** - Create custom hotkeys to trigger specific video streams instantly

## Use Cases

### 1. Doorbell Camera Preview
When someone rings your doorbell, display a live video preview on your Mac:

```yaml
# Home Assistant automation
automation:
  - alias: "Doorbell Ring - Show on Mac"
    trigger:
      - platform: state
        entity_id: binary_sensor.doorbell_button
        to: "on"
    action:
      - service: rest_command.mac_notification
        data:
          title: "Someone at the door"
          message: "Doorbell camera"
          videoUrl: "http://192.168.1.100:8080/stream"
          duration: 30
          sound: "glass"
```

### 2. Security Camera Alerts
Get instant video alerts when motion is detected:

```yaml
automation:
  - alias: "Motion Detected - Backyard"
    trigger:
      - platform: state
        entity_id: binary_sensor.backyard_motion
        to: "on"
    action:
      - service: rest_command.mac_notification
        data:
          title: "Motion Detected"
          message: "Backyard camera"
          videoUrl: "http://192.168.1.101:8080/stream"
          color: "#FF5722"
          duration: 15
```

### 3. Smart Home Status Updates
Notify when devices change state:

```yaml
automation:
  - alias: "Garage Door Opened"
    trigger:
      - platform: state
        entity_id: cover.garage_door
        to: "open"
    action:
      - service: rest_command.mac_notification
        data:
          title: "Garage Door"
          message: "The garage door was opened"
          color: "#FFC107"
          sound: "tink"
```

### 4. Package Delivery Notifications
Alert when packages arrive:

```yaml
automation:
  - alias: "Package Delivered"
    trigger:
      - platform: state
        entity_id: binary_sensor.mailbox
        to: "on"
    action:
      - service: rest_command.mac_notification
        data:
          title: "Package Delivered"
          message: "Check your mailbox!"
          videoUrl: "http://192.168.1.102:8080/stream"
          color: "#4CAF50"
          sound: "glass"
          duration: 20
```

### 5. Download/Upload Progress
Show progress for long-running tasks:

```yaml
automation:
  - alias: "Backup Progress"
    trigger:
      - platform: state
        entity_id: sensor.backup_progress
    action:
      - service: rest_command.mac_notification
        data:
          title: "Backup in Progress"
          message: "{{ states('sensor.backup_status') }}"
          progress: "{{ states('sensor.backup_progress') | float / 100 }}"
          duration: 3
```

### 6. Temperature/Climate Alerts
Get notified about environmental changes:

```yaml
automation:
  - alias: "Temperature Too High"
    trigger:
      - platform: numeric_state
        entity_id: sensor.indoor_temperature
        above: 80
    action:
      - service: rest_command.mac_notification
        data:
          title: "Temperature Alert"
          message: "Indoor temperature is {{ states('sensor.indoor_temperature') }}°F"
          color: "#F44336"
          sound: "basso"
```

### 7. Calendar Reminders
Custom reminders for upcoming events:

```yaml
automation:
  - alias: "Meeting Reminder"
    trigger:
      - platform: calendar
        event: start
        entity_id: calendar.work
        offset: "-00:05:00"
    action:
      - service: rest_command.mac_notification
        data:
          title: "Meeting in 5 minutes"
          message: "{{ trigger.calendar_event.summary }}"
          color: "#2196F3"
          sound: "ping"
          duration: 10
```

### 8. Actionable Notifications
Include buttons users can click:

```yaml
automation:
  - alias: "Washer Done"
    trigger:
      - platform: state
        entity_id: sensor.washer_status
        to: "complete"
    action:
      - service: rest_command.mac_notification_actions
        data:
          title: "Laundry Complete"
          message: "The washing machine has finished"
          sound: "glass"
```

## Home Assistant Setup

### 1. Add REST Command
Add this to your `configuration.yaml`:

```yaml
rest_command:
  mac_notification:
    url: "http://YOUR_MAC_IP:5053/notify"
    method: POST
    headers:
      Content-Type: "application/json"
    payload: >
      {
        "title": "{{ title }}",
        "message": "{{ message }}"
        {% if subtitle is defined %}, "subtitle": "{{ subtitle }}"{% endif %}
        {% if color is defined %}, "color": "{{ color }}"{% endif %}
        {% if duration is defined %}, "duration": {{ duration }}{% endif %}
        {% if sound is defined %}, "sound": "{{ sound }}"{% endif %}
        {% if position is defined %}, "position": "{{ position }}"{% endif %}
        {% if imageUrl is defined %}, "imageUrl": "{{ imageUrl }}"{% endif %}
        {% if videoUrl is defined %}, "videoUrl": "{{ videoUrl }}"{% endif %}
        {% if progress is defined %}, "progress": {{ progress }}{% endif %}
      }
```

### 2. Find Your Mac's IP
```bash
# On your Mac, run:
ipconfig getifaddr en0
```

### 3. Test the Integration
```yaml
# In Home Assistant Developer Tools > Services
service: rest_command.mac_notification
data:
  title: "Test from Home Assistant"
  message: "Integration is working!"
  color: "#4CAF50"
  sound: "glass"
```

## Video Streaming

MacOverlay supports HTTP video streams for live camera previews:

### Supported Formats
- HTTP video streams (MP4, WebM)
- HLS streams (.m3u8)
- Motion JPEG streams

### Camera Integration Examples

**Frigate NVR:**
```yaml
videoUrl: "http://frigate:5000/api/front_door/latest.jpg"
```

**UniFi Protect:**
```yaml
videoUrl: "https://unifi:7443/proxy/protect/api/cameras/CAMERA_ID/snapshot"
```

**Generic RTSP via go2rtc:**
```yaml
videoUrl: "http://go2rtc:1984/api/stream.mp4?src=doorbell"
```

## Multi-Monitor Support

MacOverlay can display notifications on any connected monitor:

1. Open **Settings**
2. Go to the **Display** section
3. Choose your target display from the dropdown
4. Or enable **"Show on All Displays"** to show notifications on every monitor

This is especially useful for:
- Multi-monitor setups where you want alerts on a specific screen
- Ultrawide monitors where you want notifications in a particular location
- Showing security camera feeds on a dedicated monitor

## Accessing the App

### Menu Bar
Click the bell icon in the menu bar for quick access to:
- Settings
- Send Test Notification
- Check for Updates

### Dock Menu
Right-click the MacOverlay icon in the Dock:
- Settings
- Send Test Notification
- Check for Updates

### Help Menu
MacOverlay menu bar > Help > Check for Updates

### Global Hotkey
Default: `Cmd + Option + O` (customizable in Settings)

## System Requirements

- macOS 13.0 (Ventura) or later
- Apple Silicon or Intel Mac

## Privacy

MacOverlay runs entirely locally on your Mac:
- No data is sent to external servers
- No analytics or tracking
- Webhook listener only accepts local network connections
- Video streams are rendered locally

## Troubleshooting

### Notifications not appearing?
1. Check MacOverlay is running (bell icon in menu bar or Dock)
2. Verify the port in Settings matches your webhook URL
3. Test with: `curl -X POST http://localhost:5053/notify -H "Content-Type: application/json" -d '{"title":"Test","message":"Hello"}'`

### Video not playing?
1. Ensure the video URL is accessible from your Mac
2. Check the URL uses HTTP (not RTSP directly)
3. Try opening the video URL in Safari first

### Menu bar icon not visible?
If your menu bar is full, use the Dock menu or Help menu instead. Right-click the MacOverlay icon in the Dock.

### Notifications on wrong monitor?
Open Settings and select the correct display from the Display dropdown.

## Contributing

Found a bug or have a feature request? Please [open an issue](https://github.com/patrickohemeng/macoverlay-releases/issues) - I actively monitor and respond to feedback.

## License

Copyright 2024 MacOverlay. All rights reserved.

---

**Note:** This repository contains release binaries only.
