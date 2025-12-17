Here's a list of Docker and Docker Compose commands to help troubleshoot:

### Basic Docker Commands

1. **Check running containers**:
   ```powershell
   docker ps
   ```

2. **Check all containers (including stopped ones)**:
   ```powershell
   docker ps -a
   ```

3. **View container logs**:
   ```powershell
   docker logs [container_id or container_name]
   
   # Follow logs in real-time
   docker logs -f [container_id or container_name]
   ```

4. **Inspect container details**:
   ```powershell
   docker inspect [container_id or container_name]
   ```

5. **Execute commands inside container**:
   ```powershell
   docker exec -it [container_id or container_name] powershell
   # or
   docker exec -it [container_id or container_name] cmd
   ```

6. **Check container resource usage**:
   ```powershell
   docker stats
   ```

7. **Remove a container**:
   ```powershell
   docker rm [container_id or container_name]
   # Force remove a running container
   docker rm -f [container_id or container_name]
   ```

8. **List all Docker networks:**
```powershell
docker network ls
```

9. **Remove a Docker network by name or ID:**
```powershell
docker network rm <network_name_or_id>
```

### Docker Compose Commands

1. **Start services in background**:
   ```powershell
   docker-compose up -d
   ```

2. **Start services with rebuilding**:
   ```powershell
   docker-compose up -d --build
   ```

3. **Stop services**:
   ```powershell
   docker-compose down
   ```

4. **View logs of all services**:
   ```powershell
   docker-compose logs
   
   # Follow logs
   docker-compose logs -f
   
   # Logs for specific service
   docker-compose logs [service_name]
   ```

5. **Check service status**:
   ```powershell
   docker-compose ps
   ```

6. **Run a command inside a service container**:
   ```powershell
   docker-compose exec [service_name] powershell
   ```

7. **View details of the compose config**:
   ```powershell
   docker-compose config
   ```

8. **Restart services**:
   ```powershell
   docker-compose restart
   ```

---
# Powershell Commands

# How to See Who Else Is Logged On (Interactive Sessions)

1. **`query user`** (CMD or PowerShell)

```
query user
```
   
   or

```
quser
```

This lists all user sessions, their session IDs, and whether they’re active, disconnected, etc. For example:

```
USERNAME              SESSIONNAME        ID  STATE   IDLE TIME  LOGON TIME
john.doe              rdp-tcp#2           2  Active      .       7/18/2023 9:05 AM
```

2. `**qwinsta**` (similar to `query session`)

```
qwinsta
```

Also shows the session ID, user, and session name.

If you see a session that’s currently active, you can either:

- **Log them off** (e.g., `logoff <session_id>`) if you have admin rights, or

- Contact them to let them know you’re rebooting.

---
# Force a Restart Regardless of Logged-On Users

## 1) PowerShell: `Restart-Computer -Force`

From **PowerShell** on the **remote** server (or with `-ComputerName` if running from your local machine):

```
Restart-Computer -ComputerName WCW-DAPP-02 -Force -Confirm:$false
```

- `**-Force**` ignores user sessions and forces an immediate restart.

- **Note**: If you’re physically RDP’d in as well, your session will drop.