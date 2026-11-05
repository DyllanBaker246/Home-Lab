
# Virtual Machine: dns-server

## 1. Overview

**Purpose:** The DNS-Server vm serves as my home lab's local DNS-Server. It resolves the domain names of locally run web applications to IP addresses.

**Creation Date:** 2026-11-4

**Last Updated:** 2026-11-4

## 2. Virtual Machine Information

| Property | Value |
|---|---|
| VM Name | dns-server |
| VM ID | |
| Hypervisor | Proxmox VE |
| Node |  |
| Operating System | Ubuntu Server |
| OS Version |  |
| VM / LXC | |
| CPU Cores | |
| RAM | |
| Disk Size | |
| Storage Location | |
| Boot on Startup | Yes / No |

## 3. Network Configuration

| Property | Value |
|---|---|
| Hostname | |
| IP Address | |
| Subnet Mask | |
| Gateway | |
| DNS Server | |
| MAC Address | |
| Network Bridge | |
| VLAN Tag | |
| DHCP / Static | |

### Network Interfaces

| Interface | IP Address | Purpose |
|---|---|---|
| | | |

## 4. Installed Software

| Software | Version | Purpose |
|---|---|---|
| | | |

## 5. Running Services

| Service | Port | Protocol | Auto Start |
|---|---|---|---|
| | | | |

## 6. Initial Setup

### Operating System Installation

Document the installation process.

### System Updates

```bash
sudo apt update && sudo apt upgrade -y
```

### User Configuration

- Primary User:
- Sudo Access:
- SSH Enabled:
- SSH Authentication:

## 7. Storage

| Mount Point | Size | Purpose |
|---|---|---|
| / | | |
| | | |

## 8. Security Configuration

- Firewall:
- SSH Configuration:
- Wazuh Agent:
- Automatic Updates:
- Network Restrictions:

## 9. Monitoring

- Monitoring Software:
- Monitoring IP:
- Health Checks:
- Log Locations:

## 10. Backup and Recovery

- Backup Method:
- Backup Schedule:
- Retention Policy:
- Last Successful Backup:
- Recovery Procedure:

## 11. Useful Commands

### Check IP Address

```bash
ip addr
```

### Check Routing

```bash
ip route
```

### Check Running Services

```bash
systemctl --type=service --state=running
```

### Check Disk Usage

```bash
df -h
```

### Check Memory

```bash
free -h
```

## 12. Troubleshooting

### Issue: [Issue Name]

**Symptoms:**

**Cause:**

**Resolution:**

## 13. Planned Improvements

- [ ] 
- [ ] 

## 14. Change History

| Date | Change | Reason |
|---|---|---|
| | | |
