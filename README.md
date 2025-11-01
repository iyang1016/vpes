# vpes

A GitHub Actions workflow that provides automated RDP access via Tailscale with auto-restart every 6 hours.

## Features

- 🔄 Automatic workflow restart every 6 hours
- 💾 Session state caching (credentials, Tailscale config, and user data)
- 🔐 Persistent RDP credentials across runs
- 📁 Automatic backup of Desktop, Documents, Downloads, and installed apps
- 🌐 Secure access via Tailscale VPN

## Documentation

See [WORKFLOW_GUIDE.md](WORKFLOW_GUIDE.md) for detailed information about the auto-restart and caching features.

## Quick Start

1. Set up the `TAILSCALE_AUTH_KEY` secret in your repository settings
2. Trigger the workflow manually or wait for the scheduled run
3. Access your RDP session using the credentials displayed in the workflow logs

## How It Works

The workflow automatically:
- Runs every 6 hours on a schedule
- Restores your previous session state:
  - RDP credentials
  - Tailscale configuration
  - Files from Desktop, Documents, Downloads
  - User-installed applications and settings
- Maintains RDP connection for ~5h 50m
- Saves session state before terminating
- Restarts automatically on the next schedule

## What Gets Saved Between Sessions

✅ **Saved automatically:**
- Desktop, Documents, Downloads folders
- Applications installed in AppData (user-level installs)
- Application settings and configurations
- RDP password (stays the same)

❌ **Not saved (size limitations):**
- Applications in `C:\Program Files` or `C:\Program Files (x86)`
- Very large files (cache limit is 10GB total)

💡 **Tip:** Use portable applications or install for "current user only" to ensure they persist between sessions!
