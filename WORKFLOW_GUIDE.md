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
- **Tailscale configuration** (`~\AppData\Local\Tailscale`) - Maintains VPN connection settings
- **RDP user credentials** (`~\.rdp-session\credentials.txt`) - Persistent login password
- **Desktop files** (`~\Desktop`) - Any files you save to the Desktop
- **Documents** (`~\Documents`) - Files in the Documents folder
- **Downloads** (`~\Downloads`) - Downloaded files
- **User-installed applications** (`~\AppData\Local\Programs`) - Apps installed for the current user
- **Application settings** (`~\AppData\Roaming`) - Application configurations and user data
- **System-wide program data** (`C:\ProgramData`) - Shared application data

**How it works:**
- **On workflow start**: Restores the previous session cache
- **On workflow end**: Saves the current session state (even if the workflow is cancelled)

**Important Notes:**
- GitHub Actions cache has a 10GB limit per repository
- Large installations in `C:\Program Files` are NOT cached due to size constraints
- For large applications, consider portable versions or user-level installations
- Cache expires after 7 days of no access

### 3. Persistent RDP Credentials
The RDP password remains consistent across workflow runs:
- **First run**: Generates a random secure password and caches it
- **Subsequent runs**: Restores the same password from cache
- This ensures you can maintain the same RDP connection credentials

### 4. User Data Persistence
All files and applications stored in the following locations are automatically preserved:
- Desktop, Documents, and Downloads folders
- Applications installed in `AppData\Local\Programs` (user-level installs)
- Application settings in `AppData\Roaming`
- System program data in `ProgramData`

**Tips for maximizing persistence:**
- Save your work files to Desktop, Documents, or Downloads
- Use portable applications when possible (stored in Downloads or Desktop)
- Install applications for "current user only" rather than "all users" when given the option
- Avoid installing large applications that go to `C:\Program Files` (not cached due to size)

### 5. Graceful Shutdown
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
- Remember: Cache expires after 7 days of no access

### Password Changes
- If the cache expires or is cleared, a new password will be generated
- Check the workflow output for the new credentials

### Installed Applications Missing
- Check if the application was installed to `C:\Program Files` (not cached)
- Reinstall to user-level location or use portable versions
- Verify cache didn't exceed 10GB limit (check workflow logs for cache warnings)

### Files Not Restored
- Ensure files were saved to Desktop, Documents, or Downloads
- Files in other locations (like C:\ root) are not cached
- Check workflow logs for any cache save/restore errors

### Cache Size Issues
- If cache exceeds 10GB, older cache entries may be evicted
- GitHub Actions will show warnings in the workflow logs
- Consider cleaning up large files or moving them outside cached directories

### Workflow Not Running Automatically
- Ensure the repository is not archived
- Check that the default branch has the workflow file
- Verify GitHub Actions is enabled for the repository
