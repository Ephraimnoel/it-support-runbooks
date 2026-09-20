# SOP: Power-On Self-Test (POST) & Boot Failure Triage

**Classification:** Hardware Triage Standard Operating Procedure  
**Applies To:** Desktop PCs and Workstations  
**Target Roles:** IT Support Specialist, Desktop Maintenance Engineer

---

## 1. Overview & Diagnostic Logic

Power-On Self-Test (POST) is the initial diagnostic routine executed by system firmware (BIOS/UEFI) immediately upon power delivery. A POST failure occurs before video initialization or operating system loading.

```text
[Power Button Pressed]
        │
        ▼
[Power Supply Active?] ──(No)──> [Check AC cable, PSU switch, Paperclip test]
        │ (Yes)
        ▼
[Fans Spin / LEDs on?]
        │
        ▼
[POST Beep / Diagnostic LED?] ──(Error Code)──> [Consult Motherboard Code Table]
        │ (No Code / Black Screen)
        ▼
[Perform Minimal Hardware Breadboarding]
```

---

## 2. Preliminary Symptom Classification

| Symptom Category | Observable Behavior | Primary Suspect Component |
|---|---|---|
| **No Power (Dead System)** | No fan spin, no chassis LEDs, zero response to power switch | AC cable, power strip, PSU, front-panel switch wire |
| **Power Cycling (Looping)** | Fans spin for 2–3 seconds, system shuts off and reboots endlessly | RAM unseated, corrupt BIOS, short circuit |
| **Fans Full Speed, No Video** | Power remains on continuously, no display signal, no keyboard NumLock toggle | CPU 4/8-pin power unseated, RAM failure, GPU unseated |
| **POST Beep Code / LED Error** | Audible pattern of beeps or motherboard debug LED illuminated | Component identified by manufacturer code (RAM, VGA, CPU) |

---

## 3. Step-by-Step Triage Procedure

### Phase 1: Electrical & Power Verification
1. Verify AC wall outlet functionality with an independent known-good device.
2. Confirm the power supply rear rocker switch is set to `|` (ON).
3. Reseat the 3-prong IEC power cable firmly into the PSU socket.
4. **Residual Drain:** Disconnect AC cable, hold front power button down for 15 seconds to discharge motherboard capacitors, reconnect power, and retest.
5. **Front Panel Switch Bypass:** If zero response occurs, disconnect the front-panel `POWER SW` motherboard jumper. Momentarily bridge the two power switch pins using a flathead screwdriver. If system powers on, the front chassis switch is defective.

### Phase 2: CMOS / Firmware Reset
Firmware settings corruption or improper memory timings frequently prevent POST:
1. Disconnect AC power cord.
2. Locate the circular 3V CR2032 lithium battery on the motherboard.
3. Gently release the retaining latch, remove the battery, and leave out for 5 minutes.
4. Alternatively, short the `CLR_CMOS` jumper pins for 10 seconds.
5. Reinsert battery, reconnect power, and attempt boot.

### Phase 3: Minimal Boot Configuration (Breadboarding)
Isolate external and secondary component interference by stripping the system to absolute bare essentials:
1. **Disconnect External Peripherals:** Remove all USB drives, printers, external adapters, leaving only 1 monitor connected to onboard/GPU video.
2. **Disconnect Internal Peripherals:**
   * Disconnect SATA power and data cables from all SSDs, HDDs, and optical drives.
   * Disconnect front-panel USB and audio headers.
   * Remove auxiliary PCIe expansion cards (Wi-Fi, sound, capture cards).
3. **Core Verification:** The system now contains ONLY:
   * Power Supply (24-pin ATX + 4/8-pin CPU 12V connected).
   * Motherboard.
   * CPU + Cooler.
   * 1 known-good stick of RAM in the primary slot.
4. Power on. If the system now posts to BIOS/UEFI, reconnect components one by one to identify the faulty device.

### Phase 4: Storage Health Verification (Post-Boot)
If the system completes POST but fails to load the OS (e.g., "No bootable device found"):
1. Enter BIOS/UEFI setup.
2. Verify whether the internal storage drive (NVMe or SATA SSD) is detected in the boot order.
3. If detected, boot into diagnostic environment and check **S.M.A.R.T.** attributes:
   * Reallocated Sectors Count.
   * Current Pending Sector Count.
   * Uncorrectable Sector Count.
4. Any non-zero value on critical raw attributes mandates immediate drive replacement.

---

## 4. Standard Motherboard Beep Code Quick Reference (AMI BIOS)

* **1 Short Beep:** Normal POST completed successfully.
* **1 Long, 2 or 3 Short Beeps:** Video subsystem failure (reseat or replace graphics card).
* **Continuous Beeping:** Memory error (reseat or test RAM modules per SOP `01-ram-diagnostics-replacement.md`).
* **5 Short Beeps:** CPU failure or CPU 12V power connector unseated.
