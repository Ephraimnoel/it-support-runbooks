# SOP: RAM Diagnostics & Physical Replacement Procedure

**Classification:** Hardware Maintenance & Triage Standard Operating Procedure  
**Applies To:** Desktop Workstations, Tower PCs, and Laptops  
**Target Roles:** IT Support Specialist, Desktop Hardware Technician

---

## 1. Purpose & Scope
This Standard Operating Procedure (SOP) defines the systematic process for identifying, isolating, testing, and replacing faulty Random Access Memory (RAM) modules in enterprise and office desktop environments.

---

## 2. Common Symptoms of Memory Degradation
Faulty, unseated, or degraded RAM typically presents with:
* **Spontaneous System Restarts:** Sudden reboot without blue-screen delay under moderate workload.
* **Windows Stop Errors (BSOD):** Frequent error bugchecks such as:
  * `MEMORY_MANAGEMENT` (`0x0000001A`)
  * `PAGE_FAULT_IN_NONPAGED_AREA` (`0x00000050`)
  * `SYSTEM_SERVICE_EXCEPTION` (`0x0000003B`)
* **POST Beep Codes:** Continuous beeping or repeated 3-beep sequences during startup before video initialization.
* **Application Crashes:** Sudden termination of memory-intensive applications with memory access violation errors (`0xc0000005`).
* **Mismatched Reported Capacity:** BIOS/UEFI or OS reports less RAM than physically installed (e.g., 8 GB installed, 4 GB usable).

---

## 3. Required Tools & Safety Equipment
* Anti-Static Wrist Strap (ESD protection) grounded to an unpainted metal chassis.
* Phillips Head #1 and #2 screwdrivers.
* Clean, lint-free microfiber cloth and 99% Isopropyl Alcohol (for contact cleaning).
* Compressed air canister.
* Bootable USB drive containing **MemTest86** (v9+ UEFI).

---

## 4. Diagnostic Procedure

### Step 4.1: Software Memory Testing (Non-Invasive)
Before disassembling the chassis:
1. **Windows Memory Diagnostic:**
   * Press `Win + R`, type `mdsched.exe`, select **Restart now and check for problems**.
   * Allow the system to reboot into diagnostic mode. Run the **Extended** test suite with at least 2 passes.
2. **MemTest86 Verification (Gold Standard):**
   * Boot the workstation from the prepared MemTest86 USB drive.
   * Run the default 4-pass test. Any reported error (red address lines) indicates physical hardware memory failure or slot defect.

### Step 4.2: Hardware Isolation (Single-Stick Method)
When errors are reported or the system fails to POST:
1. **Power Disconnection:** Disconnect AC power cord; press and hold the power button for 10 seconds to discharge residual capacitors.
2. **Attach ESD Strap:** Clip to chassis frame.
3. **Module Extraction:** Depress retention clips at both ends of the RAM slot and lift the module straight up by the edges.
4. **Single-Stick Test Sequence:**
   * Insert **Stick A** into primary slot (typically Slot DIMM_A2 or Slot 1 per motherboard manual).
   * Power on system. If POST succeeds, run 1 pass of memory test.
   * Repeat process with **Stick B** individually.
   * If Stick A passes and Stick B fails, Stick B is defective.
   * If both sticks fail in Slot 1 but pass in Slot 2, the motherboard DIMM slot is damaged.

---

## 5. Physical Replacement & Installation Procedure

1. **Verify Compatibility:**
   * Form Factor: DDR4 / DDR5 UDIMM (Desktop) or SO-DIMM (Laptop).
   * Voltage: Ensure matching voltage (e.g., 1.2V for standard DDR4).
   * Frequency: Match or exceed the motherboard's supported bus speed (e.g., DDR4-3200 MT/s). In dual-channel configurations, pair identical modules (matching capacity, latency, and speed).
2. **Clean Slot:** Blow compressed air across the DIMM slot to clear accumulated dust.
3. **Align Key Notch:** Align the off-center notch on the bottom edge of the memory stick with the ridge in the slot.
4. **Insert & Lock:**
   * Place the module into the slot perpendicular to the board.
   * Apply firm, even pressure with both thumbs on the outer top corners until both side retention latches click inward automatically.
5. **Dual-Channel Population Rule:**
   * On motherboards with 4 slots, install two sticks into alternate channels (typically Slots 2 and 4, labeled `DIMM_A2` and `DIMM_B2`) to enable 128-bit dual-channel bandwidth.

---

## 6. Post-Installation Verification
1. Power on workstation; enter BIOS/UEFI setup (`Del` or `F2`).
2. Verify:
   * Total installed memory matches expectations (e.g., 16384 MB).
   * Operating frequency matches module specification.
   * Dual-Channel mode is reported as `Active`.
3. Boot into OS; verify Task Manager -> **Performance** -> **Memory**:
   * Capacity confirmed.
   * Hardware Reserved memory is within normal bounds (< 150 MB).
4. Run a 30-minute stress test or 1 clean pass of MemTest86 before returning device to user.

---

## 7. Ticket Resolution Documentation Standard
```text
Action Taken: Replaced defective RAM module.
Initial State: Workstation suffering random reboots; MemTest86 reported errors in Bank 1.
Isolation: Single-stick testing confirmed Module B (8GB DDR4-2666) defective.
Replacement: Installed 1x 8GB DDR4-2666 UDIMM into Slot DIMM_A2.
Verification: BIOS and Windows Task Manager verify 16.0 GB total in Dual-Channel; 0 errors on 2-pass MemTest86.
```
