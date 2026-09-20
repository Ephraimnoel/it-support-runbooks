# SOP: Linux Network Triage & Diagnostic CLI Toolkit

**Classification:** Systems Administration Standard Operating Procedure  
**Applies To:** Linux Workstations & Servers  
**Target Roles:** Linux Systems Administrator, Network Support Technician

---

## 1. Overview
Modern Linux distributions have deprecated legacy tools (`ifconfig`, `netstat`, `route`) in favor of the **iproute2** suite (`ip`, `ss`). This SOP provides the standardized commands for diagnosing network interfaces, routing tables, active listening sockets, and local firewall states.

---

## 2. Interface & IP Address Triage

### Inspect Physical & Logical Interfaces
```bash
# Display all interfaces, link status, and MAC addresses
ip link show

# Display all IPv4 and IPv6 addresses assigned to interfaces
ip -brief addr show

# Bring an interface up or down
sudo ip link set dev eth0 up
sudo ip link set dev eth0 down
```

### Routing Table Verification
```bash
# View active kernel routing table
ip route show

# Identify which interface and gateway will handle a specific destination IP
ip route get 8.8.8.8
```

---

## 3. Active Socket & Port Triage (`ss`)

The `ss` (Socket Statistics) command inspects open network ports and active connections:

```bash
# Display all listening TCP and UDP sockets with numeric ports and owning process names
sudo ss -tulpn

# Filter for active connections on a specific port (e.g., port 22 for SSH)
sudo ss -tan state established '( dport = :22 or sport = :22 )'
```

---

## 4. Name Resolution & DNS Triage

```bash
# Query DNS records using dig (DNS Lookup Utility)
dig google.com +short

# Query specific nameserver (e.g., test if local gateway handles DNS)
dig @10.10.10.1 northstar.local

# Reverse DNS lookup (IP to hostname)
dig -x 10.10.10.1 +short

# Inspect active DNS servers used by systemd-resolved
resolvectl status
```

---

## 5. Local Firewall Inspection

### UFW (Uncomplicated Firewall — Ubuntu/Debian)
```bash
# Check status and active rules
sudo ufw status verbose

# Allow incoming SSH from specific subnet
sudo ufw allow from 10.10.30.0/27 to any port 22 proto tcp
```

### Iptables / Nftables
```bash
# List all active IPv4 iptables filter rules with line numbers
sudo iptables -L -n -v --line-numbers
```

---

## 6. Real-Time Packet Capture (`tcpdump`)

When ICMP tests fail and deeper Layer 2/3 inspection is required:
```bash
# Capture first 10 packets on interface eth0
sudo tcpdump -i eth0 -c 10 -n

# Filter exclusively for ICMP traffic (pings)
sudo tcpdump -i eth0 -n icmp

# Filter for traffic to/from a specific IP
sudo tcpdump -i eth0 -n host 10.10.10.1
```
