---
name: update-plus
description: Full backup, update, and restore for OpenClaw/Moltbot/Clawdbot - config, workspace, and skills with auto-rollback
version: 3.1.0
metadata: {"clawdbot":{"emoji":"🔄","requires":{"bins":["git","jq","rsync"]}}}
---

# 🔄 Update Plus

A comprehensive backup, update, and restore tool for your entire OpenClaw/Moltbot/Clawdbot environment. Protect your config, workspace, and skills with automatic rollback, encrypted backups, and cloud sync.

**Auto-detect**: Works with `openclaw`, `moltbot`, and `clawdbot` (auto-detected).

## Quick Start

```bash
# Check for available updates
update-plus check

# Create a full backup
update-plus backup

# Update everything (creates backup first)
update-plus update

# Preview changes (no modifications)
update-plus update --dry-run

# Restore from backup
update-plus restore update-plus-2026-01-25-12:00:00.tar.gz
```

## Features

| Feature | Description |
|---------|-------------|
| **Full Backup** | Backup entire environment (config, workspace, skills) |
| **Auto Backup** | Creates backup before every update |
| **Auto Rollback** | Reverts to previous commit if update fails |
| **Smart Restore** | Restore everything or specific parts (config, workspace) |
| **Multi-Directory** | Separate prod/dev skills with independent update settings |
| **Encrypted Backups** | Optional GPG encryption |
| **Cloud Sync** | Upload backups to Google Drive, S3, Dropbox via rclone |
| **Notifications** | Get notified via WhatsApp, Telegram, or Discord |
| **Connection Retry** | Auto-retry on network failure (configurable) |
| **Bot Auto-detect** | Works with openclaw, moltbot, and clawdbot |

## Installation

```bash
# Clone manually
git clone https://github.com/hopyky/update-plus.git ~/.openclaw/skills/update-plus
```

### Add to PATH

```bash
mkdir -p ~/bin
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
ln -sf ~/.openclaw/skills/update-plus/bin/update-plus ~/bin/update-plus
```

## Configuration

Create `~/.openclaw/update-plus.json`:

```json
{
  "backup_dir": "~/.openclaw/backups",
  "backup_before_update": true,
  "backup_count": 5,
  "backup_paths": [
    {"path": "~/.openclaw", "label": "config", "exclude": ["backups", "logs"]},
    {"path": "~/clawd", "label": "workspace", "exclude": ["node_modules", ".venv"]}
  ],
  "skills_dirs": [
    {"path": "~/.openclaw/skills", "label": "prod", "update": true}
  ],
  "notifications": {
    "enabled": false,
    "target": "+1234567890"
  },
  "connection_retries": 3,
  "connection_retry_delay": 60
}
```

### Config file locations (searched in order)

1. `~/.openclaw/update-plus.json`
2. `~/.moltbot/update-plus.json`
3. `~/.clawdbot/update-plus.json`
4. Legacy names (openclaw-update.json, moltbot-update.json, clawdbot-update.json)

## Commands

| Command | Description |
|---------|-------------|
| `update-plus check` | Check for available updates |
| `update-plus backup` | Create a full backup |
| `update-plus update` | Update bot and all skills |
| `update-plus update --dry-run` | Preview changes |
| `update-plus restore <file>` | Restore from backup |
| `update-plus install-cron` | Install automatic updates (daily 2 AM) |
| `update-plus uninstall-cron` | Remove cron job |

## Changelog

### v3.1.0
- Added OpenClaw support (openclaw preferred, moltbot/clawdbot fallback)
- Config: ~/.openclaw/update-plus.json
- Auto-detect bot command and npm package

### v3.0.0
- Renamed to update-plus
- Added moltbot support
- Connection retry for cron jobs

## Author

Created by **hopyky**

## License

MIT
