# SOP: Linux User Account & File Permissions Administration

**Classification:** Security & Administration Standard Operating Procedure  
**Applies To:** Linux Workstations & Servers  
**Target Roles:** Linux Administrator, Junior SysAdmin

---

## 1. Overview
Enforcing strict user account management and principle-of-least-privilege permissions is critical for maintaining system integrity and safeguarding administrative access.

---

## 2. User & Group Account Lifecycle

### Creating a New Administrative User
```bash
# 1. Create user with home directory and default bash shell
sudo useradd -m -s /bin/bash sysadmin

# 2. Set strong initial password
sudo passwd sysadmin

# 3. Grant sudo privileges (wheel on Arch/RHEL, sudo on Debian/Ubuntu)
sudo usermod -aG wheel sysadmin    # Arch/RHEL
sudo usermod -aG sudo sysadmin     # Debian/Ubuntu
```

### Locking or Removing an Account
```bash
# Lock user account (disables password login)
sudo usermod -L username

# Remove user and their home directory
sudo userdel -r username
```

---

## 3. POSIX File Permission Management

### Permission Matrix Reference
| Octal | Binary | Permission | Description |
|:---:|:---:|---|---|
| `7` | `111` | `rwx` | Read, Write, and Execute |
| `6` | `110` | `rw-` | Read and Write |
| `5` | `101` | `r-x` | Read and Execute (typical for directories/scripts) |
| `4` | `100` | `r--` | Read only |
| `0` | `000` | `---` | No access |

### Hardening Sensitive File Locations
```bash
# Standard secure permission for user home directories (owner only)
chmod 700 /home/username

# Standard secure permission for SSH configuration directory
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
```

---

## 4. SSH Key-Based Authentication Deployment

Password-based remote SSH access should be disabled in favor of cryptographic keypairs:

1. **Generate Modern Ed25519 Keypair (on client):**
   ```bash
   ssh-keygen -t ed25519 -C "admin@workstation"
   ```
2. **Deploy Public Key to Target Server:**
   ```bash
   ssh-copy-id -i ~/.ssh/id_ed25519.pub user@server_ip
   ```
3. **Hardening `/etc/ssh/sshd_config` (on server):**
   ```text
   PermitRootLogin no
   PasswordAuthentication no
   PubkeyAuthentication yes
   X11Forwarding no
   ```
4. **Reload SSH Daemon:**
   ```bash
   sudo systemctl restart sshd
   ```
