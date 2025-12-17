## Using the Scripts

Here's how to use each script:

### 1. Initial Setup

```powershell
# Navigate to the Caddy scripts directory
cd C:\DockerScripts\Caddy

# Run the startup script manually to test
.\StartCaddyService.ps1

# If successful, create the scheduled task
.\ConfigureCaddyScheduledStartupTask.ps1
```

The `ConfigureCaddyScheduledStartupTask.ps1` script will create a scheduled task that will automatically start the Caddy container when the server boots.

### 2. When Needed: Removing the Task

If you ever need to remove the scheduled task:

```powershell
# Navigate to the Caddy scripts directory
cd C:\DockerScripts\Caddy

# Run the removal script
.\RemoveCaddyScheduledStartupTask.ps1
```

### 3. Testing Auto-Restart

You can verify that the scheduled task works correctly by:

1. Stopping the Docker service
2. Manually running the task
3. Verifying that both Docker and Caddy are now running

```powershell
# Stop Docker service
Stop-Service -Name docker

# Run the task (needs to be done as SYSTEM, so let's use the Task Scheduler)
Start-ScheduledTask -TaskName "StartCaddyService"

# Check if the services are running
Get-Service -Name docker
docker ps | Select-String "caddy2_reverse_proxy"
```