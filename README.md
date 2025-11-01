# vpes

A GitHub Actions workflow that provides automated RDP access via Tailscale with auto-restart every 6 hours.

## Features

- 🔄 Automatic workflow restart every 6 hours
- 💾 Session state caching (credentials and Tailscale config)
- 🔐 Persistent RDP credentials across runs
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
- Restores your previous session state (RDP credentials, Tailscale config)
- Maintains RDP connection for ~5h 50m
- Saves session state before terminating
- Restarts automatically on the next schedule
