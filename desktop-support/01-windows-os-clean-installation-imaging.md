# SOP: Windows 10/11 Clean Installation & Baseline Deployment

**Classification:** Operating System Deployment Standard Operating Procedure  
**Applies To:** Workstation Provisioning & Re-imaging  
**Target Roles:** IT Support Specialist, Desktop Deployment Engineer

---

## 1. Purpose
Define the standard operational workflow for deploying a secure, bloatware-free clean installation of Windows 10/11 Pro on enterprise workstations, adhering to institutional naming, partition, and security baseline standards.

---

## 2. Pre-Installation Preparation
1. **Data Backup Verification:** If re-imaging an existing user machine, verify that all user documents, desktop items, and browser bookmarks have been synchronized to cloud storage or an external backup drive.
2. **UEFI Firmware Configuration:**
   * Enter BIOS/UEFI setup during startup (`F2` or `Del`).
   * Verify **SATA Mode** is set to `AHCI` (or `NVMe` native).
   * Verify **Boot Mode** is set to `UEFI Only` (disable Legacy/CSM).
   * Verify **Secure Boot** is set to `Enabled`.
   * Verify **TPM 2.0 / PTT** is set to `Enabled`.
3. **Installation Media:** Bootable USB drive prepared using the official Microsoft Media Creation Tool or Rufus (configured for GPT partition scheme for UEFI target).

---

## 3. Installation Step-by-Step

### Step 3.1: Drive Clean & Partitioning
1. Boot from USB installation media (`F12` or `F11` boot menu).
2. Select language, time, and keyboard layout; click **Install Now**.
3. Accept licensing terms; select **Custom: Install Windows only (advanced)**.
4. **Drive Sanitization:**
   * If re-imaging a drive, delete all existing partitions on Drive 0 until only unallocated space remains:
     ```text
     Drive 0 Unallocated Space (Total: xxx.x GB)
     ```
   * Select the unallocated space and click **Next**. Windows Setup will automatically generate the required GPT partition structure:
     * Recovery Partition (~500 MB)
     * EFI System Partition (~100 MB, FAT32)
     * Microsoft Reserved (MSR) Partition (16 MB)
     * Primary OS Partition (NTFS)

### Step 3.2: Out-Of-Box Experience (OOBE) Configuration
1. Select Region and Keyboard layout.
2. **Account Provisioning:**
   * Domain-joined environment: Join via Azure AD / Entra ID or local staging administrator account (`AdminLocal`).
   * Standalone environment: Create standard user account; assign local administrative rights only to the authorized IT administrator account.

---

## 4. Post-Installation Configuration Checklist

### Task 4.1: Hostname Standard Naming
Rename computer to adhere to institutional asset-tracking convention:
* Convention: `[DEPT]-[TYPE]-[ASSET_TAG]` (e.g., `ADM-PC-0104`, `FIN-LT-0205`).
* PowerShell command:
  ```powershell
  Rename-Computer -NewName "ADM-PC-0104" -Restart
  ```

### Task 4.2: Driver Installation & Windows Updates
1. Open Device Manager (`devmgmt.msc`).
2. Verify zero devices have missing drivers (yellow exclamation marks).
3. Install manufacturer-specific drivers (Chipset, Network/LAN, Audio, Graphics) from vendor support portal.
4. Execute Windows Update until system reports "You're up to date."

### Task 4.3: Baseline Security Hardening
* **BitLocker Drive Encryption:**
  * Verify TPM is active (`tpm.msc`).
  * Enable BitLocker on C: drive; backup recovery key to administrative safe or Active Directory.
* **Remote Desktop:** Enable only if explicitly required by IT management; configure NLA (Network Level Authentication).
* **System Restore:** Verify System Restore is enabled on OS drive with 5% allocation.

---

## 5. Post-Deployment Verification
* [ ] Device boots cleanly in under 20 seconds.
* [ ] All network adapters acquire DHCP address correctly.
* [ ] Antivirus / EDR agent installed and active.
* [ ] Standard productivity suite (Office/Outlook, browser) installed and licensed.
