# Update Plus

A comprehensive backup, update, and restore tool for your entire OpenClaw/Moltbot/Clawdbot environment. Protect your config, workspace, and skills with automatic rollback, encrypted backups, and cloud sync.

**Auto-detect**: Works with `openclaw`, `moltbot`, and `clawdbot`.

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
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc  # or ~/.bashrc
source ~/.zshrc
ln -sf ~/.openclaw/skills/update-plus/bin/update-plus ~/bin/update-plus
```

### Dependencies

| Dependency | Required | Purpose |
|------------|----------|---------|
| `git` | Yes | Update skills from repositories |
| `jq` | Yes | Parse JSON configuration |
| `rsync` | Yes | Efficient file copying |
| `rclone` | No | Cloud storage sync |
| `gpg` | No | Backup encryption |

## Configuration

Create `~/.openclaw/update-plus.json`:

```json
{
  "backup_dir": "~/.openclaw/backups",
  "backup_before_update": true,
  "backup_count": 5,
  "backup_paths": [
    {"path": "~/.openclaw", "label": "config", "exclude": ["backups", "logs", "media"]},
    {"path": "~/clawd", "label": "workspace", "exclude": ["node_modules", ".venv"]}
  ],
  "skills_dirs": [
    {"path": "~/.openclaw/skills", "label": "prod", "update": true},
    {"path": "~/clawd/skills", "label": "dev", "update": false}
  ],
  "remote_storage": {
    "enabled": false,
    "rclone_remote": "gdrive:",
    "path": "openclaw-backups"
  },
  "encryption": {
    "enabled": false,
    "gpg_recipient": "your-email@example.com"
  },
  "notifications": {
    "enabled": false,
    "target": "+1234567890",
    "on_success": true,
    "on_error": true
  },
  "connection_retries": 3,
  "connection_retry_delay": 60
}
```

### Config file locations (searched in order)

1. `~/.openclaw/update-plus.json`
2. `~/.moltbot/update-plus.json`
3. `~/.clawdbot/update-plus.json`
4. `~/.openclaw/openclaw-update.json` (legacy)
5. `~/.moltbot/moltbot-update.json` (legacy)
6. `~/.clawdbot/clawdbot-update.json` (legacy)

## Connection Retry

For cron jobs running at night when the network might be unstable:

| Option | Default | Description |
|--------|---------|-------------|
| `connection_retries` | 3 | Number of retry attempts |
| `connection_retry_delay` | 60 | Seconds between retries |

## Commands

### `backup` — Create Full Backup

```bash
update-plus backup
```

### `list-backups` — List Available Backups

```bash
update-plus list-backups
```

### `update` — Update Everything

```bash
# Standard update (with automatic backup)
update-plus update

# Preview changes only
update-plus update --dry-run

# Skip backup
update-plus update --no-backup

# Force continue even if backup fails
update-plus update --force
```

### `restore` — Restore from Backup

```bash
# Restore everything
update-plus restore backup.tar.gz

# Restore only config
update-plus restore backup.tar.gz config

# Restore only workspace
update-plus restore backup.tar.gz workspace
```

### `check` — Check for Updates

```bash
update-plus check
```

### `install-cron` — Automatic Updates

```bash
# Install daily at 2 AM
update-plus install-cron

# Custom schedule
update-plus install-cron "0 3 * * 0"  # Sundays at 3 AM

# Remove
update-plus uninstall-cron
```

## Notifications

Get notified when updates complete or fail:

```json
"notifications": {
  "enabled": true,
  "target": "+1234567890",
  "on_success": true,
  "on_error": true
}
```

Target format determines channel:
- `+1234567890` → WhatsApp
- `@username` → Telegram
- `channel:123` → Discord

## Architecture (v3.1)

```
bin/
├── update-plus              # Main entry point
├── moltbot-update-plus      # Symlink (compatibility)
├── clawdbot-update-plus     # Symlink (compatibility)
└── lib/
    ├── utils.sh             # Logging, helpers, connection retry
    ├── config.sh            # Configuration (auto-detect bot)
    ├── backup.sh            # Backup functions
    ├── restore.sh           # Restore functions
    ├── update.sh            # Update functions
    ├── notify.sh            # Notifications
    └── cron.sh              # Cron management
```

## Changelog

### v3.1.0
- Added OpenClaw support (openclaw preferred, moltbot/clawdbot fallback)
- Config: ~/.openclaw/update-plus.json
- Auto-detect bot command and npm package

### v3.0.0
- Renamed to `update-plus` (simpler, bot-agnostic)
- Config file: `update-plus.json` (with legacy fallback)
- Auto-detect bot (moltbot preferred, clawdbot fallback)
- Connection retry with configurable attempts and delay
- Improved cron PATH handling for all package managers

### v2.x
- Fix pnpm launcher bug
- Smart package manager detection
- Complete architecture rewrite

## Author

Created by **hopyky**

## License

MIT
