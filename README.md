# IT Support & Systems Operations Runbooks

[![Status: Active](https://img.shields.io/badge/Status-Active%20Documentation-success?style=flat&logo=git)](README.md)
[![Domain: IT Support & SysAdmin](https://img.shields.io/badge/Domain-IT%20Support%20%7C%20SysAdmin-blue?style=flat)](README.md)
[![Environment: Windows & Linux](https://img.shields.io/badge/Environment-Windows%20%7C%20Linux-orange?style=flat)](README.md)

A comprehensive collection of practical Standard Operating Procedures (SOPs), hardware diagnostic runbooks, desktop support workflows, and Linux administration guides. 

This repository reflects practical IT field experience and systematic problem-solving methods—originating from real-world IT attachment work (desktop deployments, hardware diagnostics, and enterprise client setup) and daily Linux administration on physical hardware.

> **Integrity & Professional Context:**  
> These runbooks are written as standardized, repeatable engineering documentation for IT Support Specialists, Desktop Engineers, and Junior Systems Administrators. All procedures are genericized and sanitized: no proprietary corporate data, client credentials, or private organizational records are contained herein.

---

## Table of Contents
* [Runbook Index](#runbook-index)
  * [Hardware Diagnostics & Maintenance](#1-hardware-diagnostics--maintenance)
  * [Desktop Support & Windows Client Workflows](#2-desktop-support--windows-client-workflows)
  * [Linux Systems Administration & CLI Triage](#3-linux-systems-administration--cli-triage)
  * [Operational Templates & Ticket Formats](#4-operational-templates--ticket-formats)
* [Diagnostic Philosophy & Triage Workflow](#diagnostic-philosophy--triage-workflow)
* [Career Alignment](#career-alignment)

---

## Runbook Index

### 1. Hardware Diagnostics & Maintenance
Structured procedures for diagnosing physical workstation components, resolving boot failures, and performing verified hardware replacements:
* [`hardware/01-ram-diagnostics-replacement.md`](hardware/01-ram-diagnostics-replacement.md) — Comprehensive RAM diagnostic procedure, MemTest86/Windows Memory Diagnostic workflows, dual-channel pairing, and electrostatic discharge (ESD) precautions.
* [`hardware/02-post-failure-troubleshooting.md`](hardware/02-post-failure-troubleshooting.md) — Systematic root-cause isolation for Power-On Self-Test (POST) failures, motherboard beep code interpretation, power supply testing, and storage health checks (SMART).

### 2. Desktop Support & Windows Client Workflows
Standard operating procedures for endpoint deployment, profile provisioning, and application-level troubleshooting:
* [`desktop-support/01-windows-os-clean-installation-imaging.md`](desktop-support/01-windows-os-clean-installation-imaging.md) — Standardized clean operating system installation, UEFI/GPT disk partitioning, driver staging, local user provisioning, and initial baseline security hardening.
* [`desktop-support/02-outlook-client-troubleshooting.md`](desktop-support/02-outlook-client-troubleshooting.md) — Diagnostic workflow for Microsoft Outlook client connectivity, Autodiscover resolution, cached credential clearing, profile recreation, and data file repair (`SCANPST.EXE`).
* [`desktop-support/03-network-adapter-ip-connectivity.md`](desktop-support/03-network-adapter-ip-connectivity.md) — Triage steps for endpoint network failures: resolving APIPA (`169.254.x.x`) states, DHCP lease renewal, DNS cache flushing, and TCP/IP stack resets via `netsh`.

### 3. Linux Systems Administration & CLI Triage
Standard command-line procedures for managing Linux workstations and headless servers:
* [`linux-admin/01-linux-service-management-systemd.md`](linux-admin/01-linux-service-management-systemd.md) — Service lifecycle management with `systemctl`, troubleshooting degraded states, and inspecting systemd journals (`journalctl`).
* [`linux-admin/02-linux-network-triage-cli.md`](linux-admin/02-linux-network-triage-cli.md) — Network state inspection and connectivity diagnosis using modern Linux CLI tools (`ip`, `ss`, `dig`, `tcpdump`, and `ufw` firewall inspection).
* [`linux-admin/03-file-permissions-user-management.md`](linux-admin/03-file-permissions-user-management.md) — User account lifecycle, sudo privilege delegation, standard POSIX permission management (`chmod`/`chown`), and SSH key-based authentication deployment.

### 4. Operational Templates & Ticket Formats
Standardized documentation formats to maintain professional IT service management (ITSM) hygiene:
* [`templates/incident-ticket-template.md`](templates/incident-ticket-template.md) — Professional ticketing format with structured fields for symptoms, reproduction steps, technical root cause, and verification.
* [`templates/standard-operating-procedure-template.md`](templates/standard-operating-procedure-template.md) — Standard template for authoring organizational IT procedures.

---

## Diagnostic Philosophy & Triage Workflow

All runbooks enforce a structured diagnostic model to avoid trial-and-error changes in live environments:

```text
[1. User Symptom] ──> [2. Scope Assessment] ──> [3. Physical/Link Check]
                                                        │
[6. Verification] <── [5. Targeted Fix] <── [4. Hypothesis & Isolation]
       │
[7. Ticket Documentation]
```

1. **Verify the Symptom:** Replicate the user's reported error directly rather than acting on assumptions.
2. **Determine Scope:** Identify whether the failure affects an individual endpoint, a specific subnet/department, or an entire facility.
3. **Layer-by-Layer Isolation:** Eliminate hardware and Layer 1 physical issues before diagnosing operating system, driver, or network service configurations.
4. **Single-Variable Change:** Apply exactly one remediation at a time to verify cause and effect.
5. **Positive & Negative Verification:** Confirm that the primary issue is resolved without introducing secondary service regressions.

---

## Career Alignment

This repository documents foundational, reproducible technical skills aligned with:
* **IT Support Specialist / Desktop Support Technician:** Hardware component replacement, Windows client installation, email protocol configuration, end-user problem resolution.
* **Junior Systems Administrator:** Linux CLI administration, service debugging, permission auditing, network stack diagnostics.
* **IT Operations & Technical Services:** Standard operating procedure authoring, structured incident documentation, escalation hygiene.
