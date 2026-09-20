# SOP: Linux Service Management & Diagnostics with Systemd

**Classification:** Systems Administration Standard Operating Procedure  
**Applies To:** Linux Workstations & Servers (systemd-based: Arch/EndeavourOS, Debian/Ubuntu, RHEL)  
**Target Roles:** Linux Administrator, Junior SysAdmin, IT Support

---

## 1. Overview
In modern Linux systems, `systemd` is the init system (PID 1) responsible for managing user processes, system daemons, and system state. This runbook details standard procedures for inspecting, controlling, and troubleshooting service failures.

---

## 2. Core Service Control Commands (`systemctl`)

| Operational Task | Command Syntax | Description |
|---|---|---|
| **Check Status** | `systemctl status <service>` | Displays active state, PID, uptime, memory, and recent log tail |
| **Start Service** | `sudo systemctl start <service>` | Starts the daemon in the current session |
| **Stop Service** | `sudo systemctl stop <service>` | Stops the daemon cleanly |
| **Restart Service** | `sudo systemctl restart <service>` | Full stop followed by start (disruptive) |
| **Reload Config** | `sudo systemctl reload <service>` | Reloads configuration files without terminating active sessions |
| **Enable on Boot** | `sudo systemctl enable <service>` | Creates symlink in `/etc/systemd/system/` for startup |
| **Disable Boot** | `sudo systemctl disable <service>` | Removes startup symlink |
| **Check Failed** | `systemctl --failed` | Lists all units in an error/failed state |

---

## 3. Investigating Failed Daemons: Diagnostic Workflow

When a service reports `Active: failed (Result: exit-code)`:

### Step 1: Inspect Unit Status Output
```bash
systemctl status <service_name>.service
```
* Note the **Exit Code** (e.g., `code=exited, status=1/FAILURE`).
* Note the **Process Execution Path** (verify binary exists and has execute permissions).

### Step 2: Query Detailed Systemd Journal (`journalctl`)
The status command only shows the last few lines. Use `journalctl` for detailed logs:
```bash
# View all logs for a specific unit
sudo journalctl -u <service_name>.service --no-pager -n 50

# Follow service logs in real time during troubleshooting
sudo journalctl -u <service_name>.service -f

# Filter service logs from the current boot only
sudo journalctl -u <service_name>.service -b
```

### Step 3: Validate Configuration Syntax
Many services fail to start due to minor syntax errors in configuration files:
* **Web Server (Nginx):** `sudo nginx -t`
* **Web Server (Apache):** `sudo apachectl configtest`
* **SSH Server:** `sudo sshd -t`

### Step 4: Reload Systemd Daemon
Whenever modifying a `.service` unit file in `/etc/systemd/system/`:
```bash
sudo systemctl daemon-reload
sudo systemctl restart <service_name>
```

---

## 4. Boot-Time Profiling & Optimization

To diagnose prolonged workstation or server boot times:
```bash
# 1. Total boot duration breakdown (kernel vs userspace)
systemd-analyze

# 2. Identify the slowest-starting services
systemd-analyze blame | head -n 15

# 3. Critical-chain dependency graph
systemd-analyze critical-chain
```
