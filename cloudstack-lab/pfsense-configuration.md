# pfSense Firewall Configuration — CloudStack Lab

## Overview

pfSense was deployed as a virtual machine on one of the VMware ESXi hosts to serve as the firewall and router for the CloudStack lab pod. It handled WAN/LAN separation, DHCP for lab hosts, NAT for outbound internet access, and firewall rules controlling traffic between the CloudStack management network and the rest of the lab.

## Environment Context

| Component | Value |
|---|---|
| pfSense version | 2.7.x (CE) |
| Deployment | VM on VMware ESXi 7.0.3 |
| WAN uplink | Campus network / UDM Pro upstream |
| LAN segment | CloudStack management and pod network |
| Management server IP | 192.168.1.50 |
| ESXi host IPs | 192.168.1.103, 192.168.1.219 |

---

## 1. VM Setup on ESXi

pfSense was installed as a VM before CloudStack was deployed, so the firewall was in place before any cloud infrastructure was brought up.

**VM configuration:**

- Guest OS: FreeBSD 64-bit
- vCPUs: 2
- RAM: 2 GB
- Disk: 20 GB thin provisioned
- NICs: 2 (one mapped to the WAN port group, one mapped to the LAN port group)

Two vSphere port groups were created on the ESXi host:

| Port Group | Purpose |
|---|---|
| `PG-WAN` | Uplink to campus network / UDM Pro |
| `PG-LAN` | Internal CloudStack lab network |

---

## 2. Initial Setup and Interface Assignment

On first boot, pfSense prompts for interface assignment via the console.

```
WAN  -> em0   (mapped to PG-WAN)
LAN  -> em1   (mapped to PG-LAN)
```

VLANs were not configured at this stage — flat WAN and LAN interfaces were sufficient for the lab scope.

---

## 3. Interface Configuration

### WAN Interface

The WAN interface received its address from the upstream DHCP server (campus network / UDM Pro).

| Setting | Value |
|---|---|
| Type | DHCP |
| Upstream | UDM Pro / campus gateway |

### LAN Interface

The LAN interface was assigned a static address and acted as the default gateway for all lab hosts.

| Setting | Value |
|---|---|
| IPv4 address | 192.168.1.1 |
| Subnet mask | 255.255.255.0 (/24) |
| DHCP server | Enabled on this interface |

---

## 4. DHCP Server

DHCP was enabled on the LAN interface to hand out addresses to ESXi hosts and the CloudStack management server.

| Setting | Value |
|---|---|
| Pool range | 192.168.1.100 – 192.168.1.200 |
| Gateway | 192.168.1.1 |
| DNS | 8.8.8.8, 8.8.4.4 |

Static mappings were set for known hosts so their addresses stayed consistent across reboots:

| Host | MAC | IP |
|---|---|---|
| CloudStack management | (recorded on server) | 192.168.1.50 |
| ESXi host 1 | (recorded on server) | 192.168.1.103 |
| ESXi host 2 | (recorded on server) | 192.168.1.219 |

---

## 5. NAT — Outbound

Outbound NAT was left on **Automatic** mode. pfSense automatically masqueraded all LAN traffic behind the WAN IP when leaving the lab network, giving every lab host internet access without manual rules.

---

## 6. Firewall Rules

### WAN Rules

The default pfSense WAN ruleset blocks all unsolicited inbound traffic. No inbound WAN rules were added — the lab did not require external access into the pod.

### LAN Rules

The default LAN rule allows all traffic from LAN hosts to any destination. This was kept in place for the lab to avoid blocking CloudStack agent traffic, MySQL, and management port communication between hosts.

| Rule | Source | Destination | Action |
|---|---|---|---|
| Default LAN allow | LAN net | any | Allow |

One explicit rule was added to ensure the CloudStack UI port was reachable from lab workstations:

| Rule | Source | Destination | Port | Action |
|---|---|---|---|---|
| CloudStack UI | LAN net | 192.168.1.50 | 8080 | Allow |

---

## 7. DNS

pfSense's built-in DNS Resolver (Unbound) was left enabled. Lab hosts used 192.168.1.1 as their DNS server, which forwarded upstream to 8.8.8.8.

---

## 8. Web UI Access

The pfSense web UI was accessed from any lab workstation on the LAN segment:

```
https://192.168.1.1
```

Default credentials were changed immediately after first login.

---

## Problems Encountered

- ESXi initially had no route to the internet until the pfSense VM was fully booted and the LAN port group was attached — host order of operations mattered.
- The CloudStack management server occasionally lost its DHCP lease during long lab sessions; this was resolved by adding a static DHCP mapping for its MAC address.

---

## Skills Practiced

- pfSense VM deployment on ESXi
- WAN/LAN interface assignment and addressing
- DHCP server configuration with static mappings
- Outbound NAT for lab internet access
- Firewall rule management
- DNS forwarding via Unbound
