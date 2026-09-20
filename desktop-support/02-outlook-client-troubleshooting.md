# SOP: Microsoft Outlook Client Troubleshooting & Repair

**Classification:** Application Support Standard Operating Procedure  
**Applies To:** Microsoft Outlook (Office 365, 2021, 2019)  
**Target Roles:** IT Support Specialist, Desktop Support Technician

---

## 1. Problem Overview & Scope
Email client failures represent one of the highest-volume IT support requests. Issues typically manifest as:
* Continuous credential/password prompts.
* Status showing "Disconnected" or "Trying to connect...".
* Outlook freezing during "Loading Profile...".
* Send/Receive errors (`0x80042108`, `0x8004010F`, `0x800CCC0E`).
* Search indexing failures or missing mail items.

---

## 2. Standard Triage Flowchart

```text
[Outlook Fails / Disconnected]
            │
            ▼
[Test Network & Webmail Access] ──(Fails)──> [Resolve Network / DNS Issue First]
            │ (Works)
            ▼
[Clear Windows Credential Manager]
            │
            ▼
[Launch in Safe Mode: outlook.exe /safe] ──(Fixes it)──> [Disable Conflicting Add-in]
            │ (Still Fails)
            ▼
[Run Scanpst.exe on Data File] ──(Still Fails)──> [Create New Mail Profile]
```

---

## 3. Step-by-Step Diagnostic Procedures

### Phase 1: Verify Account & Network Foundation
1. Verify internet connectivity on the endpoint (e.g., browse to webmail portal).
2. Log in to Webmail via browser to confirm:
   * User password is valid (not expired or locked).
   * Mailbox is not exceeding quota limits.
   * Multi-Factor Authentication (MFA) prompts are behaving normally.

### Phase 2: Clear Corrupt Credential Cache
Stale cached tokens in Windows Credential Manager are the primary cause of repeated authentication prompts:
1. Close Outlook and all Office applications completely.
2. Open **Credential Manager** (`control /name Microsoft.CredentialManager`).
3. Select **Windows Credentials**.
4. Under **Generic Credentials**, expand and remove all entries referencing:
   * `MicrosoftOffice16_Data:...`
   * `ADAL:...`
   * `MS.Outlook:...`
5. Relaunch Outlook; user will be prompted for fresh authentication.

### Phase 3: Add-In Isolation (Safe Mode)
Third-party add-ins (antivirus email scanners, CRM plugins, PDF toolbars) frequently crash the Outlook MAPI subsystem:
1. Launch Outlook in Safe Mode:
   * Press `Win + R`, type: `outlook.exe /safe`, press Enter.
2. If Outlook starts normally without freezing:
   * Go to **File** -> **Options** -> **Add-ins**.
   * At the bottom, select **Manage: COM Add-ins** -> click **Go...**.
   * Uncheck all non-Microsoft add-ins.
   * Relaunch Outlook normally, re-enabling add-ins one by one to identify the culprit.

### Phase 4: Repairing Corrupt Data Files (`SCANPST.EXE`)
When Outlook reports data file corruption or cannot open mailbox folders:
1. Close Outlook completely.
2. Locate the Inbox Repair Tool (`SCANPST.EXE`):
   * Typical 64-bit path: `C:\Program Files\Microsoft Office\root\Office16\SCANPST.EXE`
   * Typical 32-bit path: `C:\Program Files (x86)\Microsoft Office\root\Office16\SCANPST.EXE`
3. Launch `SCANPST.EXE`, click **Browse**, select the user's `.ost` or `.pst` file:
   * Default path: `%localappdata%\Microsoft\Outlook\`
4. Click **Start** to analyze file structure.
5. Check **Make a backup of scanned file before repairing**, then click **Repair**.

### Phase 5: Complete Mail Profile Recreation (Clean Slate)
If data corruption persists, recreate the Outlook profile:
1. Open Control Panel, set view to Small icons, click **Mail (Microsoft Outlook)**.
2. Click **Show Profiles...** -> click **Add...**.
3. Enter new profile name (e.g., `Outlook-Clean`).
4. Enter the user's name, email address, and credentials. Complete Autodiscover setup.
5. In the Mail dialog under "When starting Microsoft Outlook, use this profile", set to **Always use this profile: Outlook-Clean**.
6. Launch Outlook; allow mailbox cache to rebuild from server.

---

## 4. Verification & Sign-Off
* [ ] Status bar displays **Connected** or **Connected to: Microsoft Exchange**.
* [ ] Send/Receive completes with 0 errors.
* [ ] New incoming test message arrives in Inbox within 30 seconds.
* [ ] Outgoing test message reaches recipient successfully.
