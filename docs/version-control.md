
# Home Lab Network — Change Log

This document tracks all changes, updates, configurations, and maintenance performed on the home lab network.

Each version includes detailed documentation of the components affected, changes made, testing performed, and additional notes.

For a summarized version history, see the README.md.

### Version Types

| Version | Change Type | Description | Example |
|---------|-------------|-------------|---------|
| X.0.0 | Major | Significant infrastructure changes, network redesigns, or major architectural modifications. | New subnet, VLAN implementation, network restructuring |
| X.Y.0 | Minor | Addition of new services, devices, VMs, or network capabilities without major infrastructure changes. | Deploying Wazuh, Pi-hole, or a new Proxmox VM |
| X.Y.Z | Patch | Minor configuration adjustments, bug fixes, security patches, and routine maintenance. | Updating firewall rules, fixing DNS, updating packages |

---
## Version 1.5.0 — Wazuh and Pihole

**Date:**  2026-11-4
**Version:** 1.5.0
**Change Type:** Installation and Configuration 
**Status:** Completed

### Components Affected

| Component | Hostname | IP Address |
|-----------|----------|------------|
| Proxmox Server | proxmox | `192.168.137.4` |
| Wazuh VM | wazuh | `192.168.137.8` |
| DNS-Server VM | dns-server | `192.168.137.9` |
| Kail Linux VM | kali | `10.10.10.2` |
| Tailscale VM | tailscale | `192.168.137.7` |
| Raspberry Pi | raspberrypi | `192.168.137.120` |


### Changes Made

1. Set up Wazuh on a Ubuntu-server on Proxmox
2. Deployed agents on rbpi, kali, and tailscale
3. Setup Pihole on Ubuntu-server on proxmox

### Notes

- Still need to configure router to talk to DNS-Server

---

## Version 1.4.0 — Tailscale

**Date:**  2026-11-2
**Version:** 1.4.0
**Change Type:** Installation and Configuration
**Status:** Completed

### Components Affected

| Component | Hostname | IP Address |
|-----------|----------|------------|
| Proxmox Server | proxmox | `192.168.137.4` |
| Tailscale VM | tailscale | `192.168.137.7` |
| Laptop | zoro-HP-OmniBook-X-Flip-Laptop-14-fk0xxx | `DHCP` |


### Changes Made

1. Set up Tailscale on Ubuntu-server VM
2. Configured Laptop to connect to tailscale service

### Notes

- Successful, remote connection is established

---
## Version 1.3.0 — Metasploit

**Date:** 2026-10-30 
**Version:** 1.3.0
**Change Type:** Installation 
**Status:** Completed 

### Components Affected

| Component | Hostname | IP Address |
|-----------|----------|------------|
| Proxmox Server | proxmox | `192.168.137.4` |
| Metasploit VM | msfadmin | `10.10.10.1` |
| Kail Linux VM | kali | `10.10.10.2` |

### Changes Made

1. Set up Metasploit VM on proxmox
2. Metaspoit is accessable through an isolated network (10.10.10.0/24)
3. Configured a second network interface on kali to allow connection to Metasploit

### Notes

- Simple installation and configuration, pentesting to come.

---
## Version 1.2.0 — GLPI Server

**Date:** 2026-10-29
**Version:** 1.2.0
**Change Type:** Installation
**Status:** Completed

### Components Affected

| Component | Hostname | IP Address |
|-----------|----------|------------|
| Proxmox Server | proxmox | `192.168.137.4` |
| GLPI VM | glpi | `192.168.137.6` |


### Changes Made

1. Set up Ubuntu Server to run GLPI
2. Created a few tickets for testing

### Notes

- ...

---
## Version 1.1.0 — Uptime Kuma, Kali Linux

**Date:** 2026-10-29 
**Version:** 1.1.0
**Change Type:** Installation
**Status:** Completed

### Components Affected

| Component | Hostname | IP Address |
|-----------|----------|------------|
| Raspberry Pi | raspberrypi | `192.168.137.120` |
| Proxmox Server | proxmox | `192.168.137.4` | 
| Kali Linux VM | kali | `192.168.137.5` |

### Changes Made

1. Installed Uptime Kuma on the Raspberry Pi
2. Set up Kali linux VM on Proxmox server

### Notes

- Still need to set up SSH on Kali machine

---

## Version X.X.X — (TEMPLATE)

**Date:**  
**Version:** 
**Change Type:** Installation / Configuration / Update / Maintenance / Security / Bug Fix  
**Status:** Completed / In Progress / Rolled Back

### Components Affected

| Component | Hostname | IP Address |
|-----------|----------|------------|


### Changes Made

1. 

### Notes

- 

---

<!-- Copy the version section above for each new update. -->
