# GitHub Workflow Auto-Restart Guide

## Overview

This workflow has been configured to automatically restart every 6 hours to work around GitHub Actions' 6-hour session limit. The workflow maintains a persistent RDP session with automatic session restoration.

## Key Features

### 1. Automatic Scheduling
The workflow runs automatically every 6 hours using GitHub Actions cron schedule:
```yaml
schedule:
  - cron: '0 */6 * * *'
```

This means the workflow will start at:
- 00:00 UTC
- 06:00 UTC
- 12:00 UTC
- 18:00 UTC

### 2. Session State Caching
The workflow saves and restores session state between runs using GitHub Actions cache:

**Cached Items:**
- Tailscale configuration (`~\AppData\Local\Tailscale`)
- RDP user credentials (`~\.rdp-session\credentials.txt`)

**How it works:**
- **On workflow start**: Restores the previous session cache
- **On workflow end**: Saves the current session state (even if the workflow is cancelled)

### 3. Persistent RDP Credentials
The RDP password remains consistent across workflow runs:
- **First run**: Generates a random secure password and caches it
- **Subsequent runs**: Restores the same password from cache
- This ensures you can maintain the same RDP connection credentials

### 4. Graceful Shutdown
The workflow is configured to:
- Run for 5 hours and 50 minutes (350 minutes)
- Stop gracefully 10 minutes before the GitHub Actions timeout
- Save cache before termination

## Manual Triggers

You can still manually trigger the workflow using the "workflow_dispatch" event:
1. Go to the Actions tab in your repository
2. Select the "RH" workflow
3. Click "Run workflow"

## Important Notes

- **Timeout**: The workflow timeout is set to 350 minutes (~5h 50m) to ensure graceful shutdown
- **Cache Duration**: GitHub Actions cache expires after 7 days of no access
- **Tailscale Hostname**: Each run uses a unique hostname based on the GitHub run ID
- **Automatic Recovery**: If a run fails, the next scheduled run will restore the previous state

## Monitoring

To monitor your workflow runs:
1. Navigate to the Actions tab in your repository
2. Check the workflow run history
3. Each run will show the RDP credentials in the "Maintain Connection" step output

## Troubleshooting

### Cache Not Restored
- Check if the cache key pattern matches in the workflow logs
- Verify that previous runs completed successfully and saved the cache

### Password Changes
- If the cache expires or is cleared, a new password will be generated
- Check the workflow output for the new credentials

### Workflow Not Running Automatically
- Ensure the repository is not archived
- Check that the default branch has the workflow file
- Verify GitHub Actions is enabled for the repository
