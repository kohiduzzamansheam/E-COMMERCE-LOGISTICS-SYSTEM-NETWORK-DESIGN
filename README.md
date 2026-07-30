# E-COMMERCE-LOGISTICS-SYSTEM-NETWORK-DESIGN

A complete enterprise network design built in **Cisco Packet Tracer**, connecting six sites — **MOH, WHC, CSC, DDU, DPU, and RPP** — over a hierarchically subnetted `41.41.0.0/16` address space using **Variable Length Subnet Masking (VLSM)**, **RIPv2**, static/floating routes, and a redundant backup WAN link.

This project was completed as a networking course project (CSE421).

---

## 📌 Project Highlights

- Designed a **hierarchical VLSM addressing scheme** for a 16-bit block (`41.41.0.0/16`) serving 6 sites, 2 nested sub-sites, and 6 point-to-point WAN links — with zero wasted address space beyond what each segment actually needed.
- Configured **6 Cisco routers** (MOH, WHC, CSC, DDU, DPU, RPP) with LAN and serial WAN interfaces.
- Deployed **RIPv2** (classless, no auto-summary) as the dynamic interior routing protocol across the WAN backbone.
- Added a **static route + redistribution** to reach a site that RIP alone couldn't propagate to correctly.
- Built in **link redundancy**: a backup serial link between WHC and CSC so the network survives a primary WAN failure.
- Deployed end devices and servers (DNS, Email, DHCP, Web) with correct default gateways per subnet.
- Verified end-to-end reachability across all 6 sites via ICMP (ping) and traceroute testing in Packet Tracer simulation mode.


**Sites:**

| Site | Role | LAN Subnet |
|------|------|------------|
| MOH  | Headquarters / core router, hub for WAN backbone | `41.41.0.0/24` |
| WHC  | Branch site, secondary WAN hub (redundant link to CSC) | `41.41.1.0/24` |
| CSC  | Branch site, connects to DPU | `41.41.2.0/24` |
| DDU  | Branch site, connects to RPP | `41.41.3.0/24` |
| DPU  | Sub-site off CSC, split further into DPU + RPP | `41.41.4.0/25` |
| RPP  | Sub-site off DPU/DDU | `41.41.4.128/26` |

**WAN backbone** (`41.41.5.0/24`, subnetted into `/30`s for point-to-point links):

| Link | Subnet | Purpose |
|------|--------|---------|
| MOH ↔ WHC | `41.41.5.0/30` | Primary WAN link |
| WHC ↔ DDU | `41.41.5.4/30` | WAN link |
| DDU ↔ RPP | `41.41.5.8/30` | WAN link |
| CSC ↔ DPU | `41.41.5.12/30` | WAN link |
| MOH ↔ CSC | `41.41.5.16/30` | Shared backbone segment |
| WHC ↔ CSC | `41.41.5.20/30` | **Backup / redundant link** |

Full breakdown and VLSM math: see [`docs/vlsm-subnetting.md`](docs/vlsm-subnetting.md).

---

## 🔢 IP Addressing Plan

Every router interface, server, and PC in the topology, with subnet mask and default gateway:
👉 [`docs/ip-addressing-table.md`](docs/ip-addressing-table.md)

---

## ⚙️ Router Configuration

CLI configuration excerpts for interface addressing, RIPv2, static routing, and redistribution:
👉 [`docs/router-configurations.md`](docs/router-configurations.md)

Key routing decisions:
- **RIPv2** (`version 2`, `no auto-summary`) runs on `network 41.0.0.0` across all routers for classless VLSM-aware routing.
- A **static route** (`ip route 41.41.3.0 255.255.255.0 41.41.5.2`) was added on MOH toward the DDU network via WHC, then **redistributed into RIP** (`redistribute static`) so the rest of the network learns the path.
- The **WHC–CSC link** (`41.41.5.20/30`) acts as a backup path, providing redundancy if the primary MOH–CSC backbone link fails.

---

## 🧪 Testing & Verification

Validated in Packet Tracer using:
- `ping` between end devices on different LANs to confirm inter-site reachability
- `show ip route` on each router to confirm RIP-learned and static routes
- `show ip interface brief` to confirm all interfaces were correctly addressed and up
- Simulated link failure on the primary backbone to confirm traffic can reroute over the WHC–CSC backup link


## 🗂️ Repository Structure

```
.
├── README.md                       # This file
├── project01_01.pkt                # Cisco Packet Tracer topology file (open with Packet Tracer)
├── vlsm-diagram.png                # VLSM subnetting tree diagram
│           
├── docs/
│   ├── vlsm-subnetting.md          # Full VLSM breakdown & subnetting math
│   ├── ip-addressing-table.md      # Every interface/device IP, mask, gateway
│   └── router-configurations.md    # IOS CLI configuration commands
└── Project Instructions
```

---

## 🛠️ Tools & Skills Demonstrated

- **VLSM (Variable Length Subnet Masking)** subnetting design
- **Cisco IOS CLI** configuration (interfaces, RIPv2, static routes, redistribution)
- **Hierarchical network design** for a multi-site enterprise WAN
- **Network redundancy** planning (backup links)
- **Cisco Packet Tracer** simulation and testing

