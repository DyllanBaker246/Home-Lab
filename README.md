# Home Lab Infrastructure

> **Status:** Active development  
> **Focus:** Networking, cybersecurity, Linux administration, virtualization, and monitoring  
> **Lab subnet:** `192.168.137.0/24`

A personal home lab for learning enterprise networking, infrastructure administration, monitoring, and security testing. The lab is separated from the primary home network by a Cisco router performing NAT.

## Goals

- Configure and troubleshoot Cisco routing, switching, DHCP, NAT, VLANs, and port mirroring.
- Run Linux systems and virtual machines using Proxmox VE.
- Monitor availability with Uptime Kuma and analyze traffic with ntopng.
- Practice authorized penetration testing in isolated environments.
- Document changes, configurations, and recovery procedures.

## Network topology

```text
                    Internet
                       |
              Home router / LAN
                192.168.1.0/24
                       |
                 Cisco router
              NAT / routing / DHCP
                       |
                 Cisco switch
                192.168.137.0/24
                 /      |      \
                /       |       \
       Proxmox host  Raspberry Pi  Linux desktop
          vmbr0        monitoring
            |          /       \
         Kali VM   Uptime Kuma   ntopng
         Other VMs   Docker       systemd
```

> **Note:** The diagram is logical, not a verified physical port map. Proxmox guests use a bridge to join the lab LAN directly; no additional NAT is needed on Proxmox. The Cisco router still provides NAT upstream.

## Addressing plan

| Device / network | Address | Assignment | Notes |
|---|---|---|---|
| Home LAN | `192.168.1.0/24` | Existing | Outside lab |
| Lab LAN | `192.168.137.0/24` | Existing | Cisco-side subnet |
| Cisco router LAN | `192.168.137.1` | **Verify** | Assumed gateway |
| Proxmox host | `192.168.137.4` | **Verify** | Management UI typically on port 8006 |
| Raspberry Pi | `192.168.137.120` | DHCP (observed) | Reserve this address if desired |
| Kali Linux VM | `192.168.137.10` | Planned static | Confirm unused and outside DHCP pool |
| Cisco switch management | TBD | TBD | Only if management IP configured |
| Linux desktop | TBD | TBD | |
| Docker default bridge | `172.17.0.0/16` | Docker-managed | Container-internal networking |
| Isolated pentest segment | TBD | Planned | No route to home LAN |

**Addressing rules:** Verify the router's DHCP pool before choosing static IPs. Prefer DHCP reservations for infrastructure when possible. Record all assignments and changes here.

## Hardware inventory

| Equipment | Model | OS / firmware | Purpose |
|---|---|---|---|
| Cisco router | TBD | Cisco IOS / TBD | NAT, routing, DHCP |
| Cisco switch | TBD | Cisco IOS / TBD | Switching, VLANs, SPAN |
| Proxmox server | TBD | Proxmox VE / TBD | Virtualization |
| Raspberry Pi | TBD | Raspberry Pi OS / TBD | Monitoring |
| Linux desktop | TBD | Linux / TBD | Administration |

### Switch port map

| Port | Connected device | VLAN | Notes |
|---|---|---|---|
| TBD | Cisco router | TBD | Uplink |
| TBD | Proxmox host | TBD | VM bridge uplink |
| TBD | Raspberry Pi | TBD | Monitoring |
| TBD | Linux desktop | TBD | Workstation |
| TBD | SPAN destination | TBD | If configured for traffic capture |

## Infrastructure services

### Proxmox VE

- **Management URL:** `https://192.168.137.4:8006` (verify address)
- **Bridge:** `vmbr0` (verify physical NIC membership)
- **VM connectivity:** Bridged onto lab LAN; no Proxmox NAT
- **Backups:** TBD (configure scheduled VM backups; snapshots alone are not backups)

| VM | Intended address | Purpose | State |
|---|---|---|---|
| Kali Linux | `192.168.137.10` | Penetration testing | Installation / address to verify |
| Metasploitable | TBD | Vulnerable practice target | Planned |
| Ubuntu Server | TBD | General-purpose services | Planned |

**Safety:** Keep deliberately vulnerable guests on an isolated Proxmox bridge or VLAN, not on the ordinary lab LAN. Restrict routing to home and public networks as needed.

### Raspberry Pi: Uptime Kuma

- **Dashboard:** `http://192.168.137.120:3002`
- **Deployment:** Docker
- **Image:** `louislam/uptime-kuma:2`
- **Persistent volume:** `uptime-kuma`
- **Container port:** `3001`; **host port:** `3002`
- **Startup:** Docker `unless-stopped` restart policy

```bash
sudo docker ps
sudo docker logs --tail 50 uptime-kuma
sudo docker restart uptime-kuma
sudo docker inspect uptime-kuma --format '{{.HostConfig.RestartPolicy.Name}}'
```

To recreate the container using the existing named data volume:

```bash
sudo docker run -d \
  --restart=unless-stopped \
  -p 3002:3001 \
  -v uptime-kuma:/app/data \
  --name uptime-kuma \
  louislam/uptime-kuma:2
```

> Stop and remove the existing container before recreating it. **Do not delete the `uptime-kuma` volume** unless intentionally wiping the data. Back up the volume before upgrades.

Use ping monitors for reachable devices and HTTPS monitors for web services. Create manual maintenance windows for VMs intentionally powered off.

### Raspberry Pi: ntopng

- **Dashboard:** `https://192.168.137.120:3001`
- **Deployment:** Native systemd service
- **Transport:** HTTPS with a locally generated/self-signed certificate
- **Startup:** `ntopng.service` enabled
- **Purpose:** Traffic analysis and visibility

```bash
sudo systemctl status ntopng
sudo systemctl enable --now ntopng
sudo journalctl -u ntopng -n 50 --no-pager
sudo ss -lntp | grep ntopng
```

**Important:** ntopng listens on **HTTPS port 3001**, while Uptime Kuma uses **HTTP port 3002**. An earlier conflict occurred when both tried to bind host port 3001. Do not publish Kuma on host port 3001 while ntopng uses it.

### Service inventory

| Service | Host | Port | Protocol | Startup |
|---|---|---|---|---|
| Proxmox UI | Proxmox | 8006 | HTTPS | Host service |
| Uptime Kuma | Raspberry Pi | 3002 | HTTP | Docker restart policy |
| ntopng | Raspberry Pi | 3001 | HTTPS | systemd |
| SSH | Applicable hosts | 22 (verify) | SSH | systemd / host OS |

## Cisco configuration reference

### Router checks

```text
show ip interface brief
show ip route
show ip dhcp binding
show ip nat translations
show access-lists
show running-config
```

### Switch checks

```text
show interfaces status
show interfaces description
show vlan brief
show mac address-table
show interfaces trunk
show monitor session all
show running-config
```

Keep sanitized configuration backups under `configs/`. Never commit passwords, private keys, SNMP communities, or other secrets.

## Monitoring plan

| Target | Method | Endpoint | Maintenance policy |
|---|---|---|---|
| Cisco router | Ping | Gateway IP (verify) | Planned network work |
| Cisco switch | Ping | Management IP (if configured) | Planned network work |
| Proxmox | Ping / HTTPS | `192.168.137.4` (verify) | Planned host work |
| Raspberry Pi | Ping | `192.168.137.120` (current) | Planned reboot |
| ntopng | HTTPS | `https://192.168.137.120:3001` | Service upgrades |
| Kali VM | Ping | `192.168.137.10` (planned) | Manual maintenance while off |

For the self-signed ntopng certificate, configure the Kuma monitor to trust that certificate or explicitly ignore TLS verification **only for this trusted internal endpoint**. A login redirect (HTTP 302) may be an acceptable healthy response.

## Security practices

- Keep the home network and lab network separated; review Cisco NAT and ACL rules.
- Restrict Proxmox, SSH, and monitoring dashboards to trusted management devices.
- Use unique strong credentials; store them in a password manager, not this repository.
- Apply OS and package updates regularly.
- Back up Proxmox VMs, Raspberry Pi configurations, and Uptime Kuma's persistent data.
- Isolate intentionally vulnerable VMs from ordinary devices and the internet.
- Only test systems owned by you or explicitly authorized for testing.
- Document changes to firewall rules, VLANs, bridges, and IP allocations.

## Troubleshooting quick reference

```bash
# Raspberry Pi interfaces and routes
ip -br addr
ip route

# Service availability
sudo systemctl status docker ntopng --no-pager
sudo docker ps -a

# Check web ports
sudo ss -lntp | grep -E ':3001|:3002|:8006'

# Docker networking
sudo docker network ls
sudo docker inspect uptime-kuma --format '{{json .NetworkSettings.Networks}}'

# Docker logs
sudo journalctl -u docker -b -l --no-pager

# Test ntopng HTTPS locally
curl -kI https://127.0.0.1:3001/

# Test Kuma HTTP locally
curl -I http://127.0.0.1:3002/
```

## Backup and recovery

| Asset | Backup method | Frequency | Restore tested? |
|---|---|---|---|
| Cisco configurations | Export sanitized running/startup config | After changes | TBD |
| Proxmox VMs | Proxmox Backup / vzdump | TBD | TBD |
| Uptime Kuma | Back up Docker volume with container stopped or consistent backup method | TBD | TBD |
| ntopng configuration | Back up `/etc/ntopng/` and relevant settings | After changes | TBD |
| Documentation | Git repository | Every change | N/A |

Store at least one backup outside the Proxmox host and Raspberry Pi. Record actual recovery steps once tested.

## Change management

For significant changes, create an entry in `docs/change-log.md` with:

| Field | Description |
|---|---|
| Date | When the change occurred |
| Device / service | Affected system |
| Reason | Why the change was needed |
| Before | Prior configuration or behavior |
| Change | Commands and settings modified |
| Verification | Tests performed and results |
| Rollback | How to reverse the change |
| Outcome | Success, partial success, or follow-up needed |

### Change log (summary)

| Date | Component | Change | Result |
|---|---|---|---|
| 2026-10-28 | Raspberry Pi | Deployed Uptime Kuma with Docker | Working on host port 3002 |
| 2026-10-28 | ntopng / Docker | Identified host-port conflict on 3001 | Separated service ports |
| 2026-10-28 | ntopng | Confirmed HTTPS listener on port 3001 | Responds with login redirect |

## Roadmap

- [ ] Verify and document all physical port connections.
- [ ] Confirm DHCP scope, default gateway, and static IP reservations.
- [ ] Finalize Kali VM deployment and static IP configuration.
- [ ] Build an isolated network for vulnerable testing VMs.
- [ ] Set up regular Proxmox and Raspberry Pi backups.
- [ ] Configure Uptime Kuma notifications.
- [ ] Create network and service diagrams.
- [ ] Document VLANs, ACLs, and switch SPAN configuration.
- [ ] Test reboot recovery for ntopng and Uptime Kuma.
- [ ] Add DNS names for internal services.

## Suggested repository structure

```text
home-lab/
├── README.md
├── diagrams/
│   └── network-topology.png
├── configs/
│   ├── cisco-router.example.txt
│   └── cisco-switch.example.txt
├── docs/
│   ├── addressing.md
│   ├── hardware-inventory.md
│   ├── change-log.md
│   ├── backup-recovery.md
│   └── troubleshooting.md
├── services/
│   ├── uptime-kuma.md
│   └── ntopng.md
└── vms/
    ├── kali-linux.md
    └── metasploitable.md
```

## References

- [Proxmox VE documentation](https://pve.proxmox.com/pve-docs/)
- [Kali Linux documentation](https://www.kali.org/docs/)
- [Uptime Kuma repository](https://github.com/louislam/uptime-kuma)
- [ntopng documentation](https://www.ntop.org/guides/ntopng/)
- [Docker documentation](https://docs.docker.com/)

---

**Documentation convention:** Replace `TBD`, `planned`, and `verify` entries as configurations are confirmed. Keep credentials and sensitive inventory details out of public repositories.
