# SOP: Endpoint Network Adapter & IP Connectivity Triage

**Classification:** Network Diagnostics Standard Operating Procedure  
**Applies To:** Windows 10/11 & Linux Workstations  
**Target Roles:** IT Support Specialist, Network Support Technician

---

## 1. Problem Overview
Endpoint connectivity failures typically present as:
* "No Internet, secured" or yellow warning triangle over network icon.
* Windows assigning an Automatic Private IP Addressing (APIPA) address: `169.254.x.x`.
* Endpoint can ping internal servers by IP but cannot resolve domain names (DNS failure).
* Intermittent connection drops or physical link flaps.

---

## 2. Systematic Troubleshooting Stages

### Stage 1: Physical & Data Link Verification (Layer 1 & 2)
1. **Physical Cable & NIC Check:**
   * Inspect RJ-45 Ethernet cable at both workstation NIC and wall jack.
   * Verify link LEDs:
     * Solid Green/Amber: Physical link established.
     * Unlit / Dark: No physical link (cable severed, switchport shutdown, or wall jack unpatched).
2. **Adapter State:**
   * Open Network Connections (`ncpa.cpl`).
   * Confirm the adapter is not **Disabled**. If disabled, right-click and select **Enable**.

### Stage 2: IP Configuration Inspection (Layer 3)
Open Command Prompt as Administrator:
```cmd
ipconfig /all
```

#### Diagnostic Evaluation:
* **Case A: IP is `169.254.x.x` (APIPA)**
  * *Meaning:* The workstation broadcasted a DHCP Discover message, but received no DHCP Offer from the server.
  * *Action:* Check if switchport is placed in the correct VLAN; verify DHCP pool utilization on router; test with static test IP.
* **Case B: Default Gateway is Missing**
  * *Meaning:* Host received partial DHCP lease or manual configuration omitted gateway.
  * *Action:* Verify DHCP scope options on router.
* **Case C: Valid Subnet IP Present**
  * *Meaning:* Local addressing is functional; proceed to connectivity testing.

### Stage 3: Step-by-Step ICMP Reachability Testing
Follow the strict outward diagnostic sequence:
1. **Loopback Address:**
   ```cmd
   ping 127.0.0.1
   ```
   * *If fails:* Local TCP/IP stack is corrupt.
2. **Local NIC IP:**
   ```cmd
   ping [Local_Host_IP]
   ```
   * *If fails:* Network adapter driver or hardware defect.
3. **Default Gateway:**
   ```cmd
   ping [Default_Gateway_IP]
   ```
   * *If fails:* Physical cable, access port VLAN, or router subinterface issue.
4. **Remote IP (Bypassing DNS):**
   ```cmd
   ping 1.1.1.1
   ```
   * *If succeeds:* Internet routing is working.
5. **Domain Name (DNS Test):**
   ```cmd
   ping google.com
   ```
   * *If ping 1.1.1.1 works but ping google.com fails:* DNS failure.

---

## 3. Standard Network Remediation Commands

### Windows Network Stack Reset
Run in elevated Command Prompt:
```cmd
:: 1. Release existing and request new DHCP lease
ipconfig /release
ipconfig /renew

:: 2. Flush and re-register local DNS resolver cache
ipconfig /flushdns
ipconfig /registerdns

:: 3. Reset Winsock catalog and TCP/IP stack
netsh winsock reset
netsh int ip reset

:: 4. Reboot workstation to finalize stack reset
shutdown /r /t 0
```

### Linux Network Stack Verification
Run in Linux terminal:
```bash
# 1. Check link and addressing state
ip link show
ip addr show

# 2. Inspect kernel routing table
ip route show

# 3. Test DNS resolution
dig google.com +short

# 4. Restart NetworkManager daemon
sudo systemctl restart NetworkManager
```
