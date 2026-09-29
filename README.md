# ClassTP-RapidPVST-PORTFAST-BPDU

# Cisco Rapid PVST+, PortFast & BPDU Guard

![Cisco](https://img.shields.io/badge/Cisco-Networking-049FD9?style=for-the-badge&logo=cisco&logoColor=white)
![Packet Tracer](https://img.shields.io/badge/Cisco-Packet_Tracer-049FD9?style=for-the-badge&logo=cisco&logoColor=white)
![Switching](https://img.shields.io/badge/Technology-Layer_2_Switching-1F6FEB?style=for-the-badge)
![STP](https://img.shields.io/badge/Protocol-Rapid_PVST%2B-6F42C1?style=for-the-badge)

A Cisco switching lab focused on Layer 2 redundancy, Spanning Tree Protocol (STP), Rapid PVST+, PortFast, and BPDU Guard.

This project demonstrates how to configure a redundant three-switch topology, elect a root bridge, prevent Layer 2 loops, and enable fast convergence using Rapid PVST+.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Objectives](#objectives)
- [Network Topology](#network-topology)
- [IP Addressing Table](#ip-addressing-table)
- [VLAN Configuration](#vlan-configuration)
- [Technologies Used](#technologies-used)
- [Configuration](#configuration)
- [Verification Commands](#verification-commands)
- [Expected Results](#expected-results)
- [Key Concepts](#key-concepts)
- [Repository Structure](#repository-structure)
- [Author](#author)

---

## Project Overview

This lab implements a redundant switched network using three Cisco switches:

- S1 — Secondary Root Bridge
- S2 — Primary Root Bridge
- S3 — Access Switch

The network uses VLAN 10 for user traffic and VLAN 99 for management.

The switches are connected using trunk links with VLAN 99 configured as the native VLAN.

Rapid PVST+ is enabled on all three switches. PortFast and BPDU Guard are configured on access ports to improve endpoint connectivity and protect against potential Layer 2 loops.

## Objectives

1. Configure the basic settings of three Cisco switches.
2. Create VLAN 10 and VLAN 99.
3. Configure access ports and trunk links.
4. Assign management IP addresses to the switches.
5. Configure S2 as the primary root bridge.
6. Configure S1 as the secondary root bridge.
7. Enable Rapid PVST+ on all switches.
8. Configure PortFast and BPDU Guard on edge ports.
9. Verify STP convergence and redundant link behavior.

---

## Network Topology

The lab uses a redundant triangle topology between S1, S2, and S3.

```text
                    S2
                 Root Bridge
                /           \
          Fa0/1               Fa0/3
             /                 \
          Fa0/1                 Fa0/1
           S1 ------------------ S3
      Secondary Root          Access Switch
             Fa0/3              Fa0/3
             |                    |
           Fa0/6                Fa0/18
             |                    |
           PC-A                  PC-C
```

The diagram is illustrative. Confirm the exact cable-to-port mapping in your Packet Tracer topology before applying the configurations.

### Switch Roles

| Switch | Role | Management IP |
|---|---|---|
| S1 | Secondary Root Bridge | 192.168.1.11 |
| S2 | Primary Root Bridge | 192.168.1.12 |
| S3 | Access Switch | 192.168.1.13 |

---

## IP Addressing Table

| Device | Interface | IP Address | Subnet Mask |
|---|---|---|---|
| S1 | VLAN 99 | 192.168.1.11 | 255.255.255.0 |
| S2 | VLAN 99 | 192.168.1.12 | 255.255.255.0 |
| S3 | VLAN 99 | 192.168.1.13 | 255.255.255.0 |
| PC-A | NIC | 192.168.0.2 | 255.255.255.0 |
| PC-C | NIC | 192.168.0.3 | 255.255.255.0 |

No default gateway is required for communication within the same subnet.

---

## VLAN Configuration

| VLAN ID | VLAN Name | Purpose |
|---|---|---|
| 10 | User | End-user traffic |
| 99 | Management | Switch management |

### Port Assignments

| Switch | Interface | Mode | VLAN |
|---|---|---|---|
| S1 | Fa0/6 | Access | 10 |
| S3 | Fa0/18 | Access | 10 |
| All switches | Fa0/1, Fa0/3 | Trunk | Native VLAN 99 |

---

## Technologies Used

- Cisco IOS
- Cisco Packet Tracer
- IEEE 802.1Q trunking
- Per-VLAN Spanning Tree Plus (PVST+)
- Rapid PVST+ (IEEE 802.1w)
- PortFast
- BPDU Guard
- VLAN segmentation
- Layer 2 redundancy

---

## Configuration

Full Cisco IOS configurations for all three switches are provided below.

### S1 — Secondary Root Bridge

S1 is configured as the secondary root bridge for VLANs 1, 10, and 99.

### S2 — Primary Root Bridge

S2 is configured as the primary root bridge for VLANs 1, 10, and 99.

### S3 — Access Switch

S3 provides endpoint connectivity and uses global PortFast and BPDU Guard defaults for eligible access ports.

---

## Verification Commands

Run these commands on the switches to verify the configuration.

### VLAN Verification

```cisco
show vlan brief
```

### Trunk Verification

```cisco
show interfaces trunk
```

### Spanning Tree Verification

```cisco
show spanning-tree
show spanning-tree vlan 10
show spanning-tree vlan 99
```

### Root Bridge Verification

```cisco
show spanning-tree root
```

### Rapid PVST+ Verification

```cisco
show running-config | include spanning-tree mode
```

Expected:

```cisco
spanning-tree mode rapid-pvst
```

### PortFast and BPDU Guard Verification

```cisco
show spanning-tree interface fa0/6 detail
show spanning-tree interface fa0/18 detail
show running-config | include spanning-tree
```

### Interface Status

```cisco
show ip interface brief
show interfaces status
```

### Connectivity Tests

From PC-A:

```text
ping 192.168.0.3
```

From S1:

```cisco
ping 192.168.1.12
ping 192.168.1.13
```

From S2:

```cisco
ping 192.168.1.11
ping 192.168.1.13
```

From S3:

```cisco
ping 192.168.1.11
ping 192.168.1.12
```

---

## Expected Results

| Test | Expected Result |
|---|---|
| VLAN 10 | Created and active |
| VLAN 99 | Created and active |
| Trunk links | Operational with native VLAN 99 |
| Root bridge | S2 |
| Secondary root bridge | S1 |
| Rapid PVST+ | Enabled on all switches |
| Redundant path | One path blocked where required to prevent loops |
| PortFast | Enabled on configured edge ports |
| BPDU Guard | Enabled on configured edge ports |
| PC-A to PC-C | Successful ping when VLAN and cabling are correct |

---

## Key Concepts

### Rapid PVST+

Rapid PVST+ is Cisco's per-VLAN implementation of Rapid Spanning Tree Protocol. It improves convergence after Layer 2 topology changes.

### Root Bridge

The root bridge is the reference switch used by STP to calculate the loop-free topology.

In this lab:

- S2 is the primary root bridge.
- S1 is the secondary root bridge.

### PortFast

PortFast allows an eligible edge port to transition rapidly to the forwarding state when a host connects.

It should be used on end-device access ports, not ordinary switch-to-switch trunk links.

### BPDU Guard

BPDU Guard protects PortFast-enabled edge ports by placing a port into an error-disabled state if it receives a BPDU.

This helps prevent unauthorized switches from being connected to protected edge ports.

---

## Repository Structure

```text
rapid-pvst-portfast-bpduguard/
|
|-- README.md
|-- configs/
|   |-- S1.cfg
|   |-- S2.cfg
|   |-- S3.cfg
|
|-- topology/
|   |-- topology.png
|
|-- screenshots/
|   |-- vlan-verification.png
|   |-- spanning-tree-verification.png
|   |-- connectivity-test.png
|
|-- packet-tracer/
    |-- rapid-pvst-lab.pkt
```

---

## Author

**Aya Hathout**

Digital Infrastructure | Networking & Cybersecurity

GitHub: [A-straya](https://github.com/A-straya)

---

## References

Cisco Networking Academy — Rapid PVST+, PortFast, and BPDU Guard lab.

Cisco documentation: [Spanning Tree Protocol](https://www.cisco.com/)


