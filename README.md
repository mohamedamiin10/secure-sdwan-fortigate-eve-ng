# Secure SD-WAN Banking Network

> **Design and Implementation of Secure SD-WAN Architecture for Bank Networks Using FortiGate and EVE-NG**

This repository contains the laboratory implementation, configurations, topology documentation, screenshots, testing evidence, and results for a graduation thesis focused on designing and implementing a secure SD-WAN architecture for a banking network.

The project is implemented in **EVE-NG** using **FortiGate firewalls**, an emulated **Internet/ISP router**, Cisco switching, **IPsec VPN**, **SD-WAN**, **BGP**, **ADVPN**, and **Zabbix** monitoring.

---

## Architecture at a Glance

The implemented architecture is a **hub-and-spoke topology with one hub and two spokes**:

- **1 Hub:** HQ-FW
- **2 Spokes:** BRNCH-1 and BRNCH-2
- **2 WAN links at HQ:** HQ-FW WAN1 + WAN2
- **2 WAN links at Branch 1:** BRNCH-1 WAN1 + WAN2
- **2 WAN links at Branch 2:** BRNCH-2 WAN1 + WAN2
- **Simulated Internet/ISP underlay**
- **IPsec SD-WAN overlay**
- **BGP AS 65001**
- **ADVPN** for dynamic branch-to-branch shortcuts

## Actual Topology Diagram

![Secure SD-WAN Banking Topology](topology/banking-topology.webp)

> **Figure 1 — Implemented Secure SD-WAN Banking Topology.**
>
> HQ-FW is the hub, BRNCH-1 is Spoke 1, and BRNCH-2 is Spoke 2. Each FortiGate site has two WAN connections to the simulated Internet/ISP underlay.

---

## Sites and Devices

| Site | Role | FortiGate | Internal Switch |
|---|---|---|---|
| HQ | **Hub** | HQ-FW | HQ-SW |
| Branch 1 | **Spoke 1** | BRNCH-1 | B1-SW |
| Branch 2 | **Spoke 2** | BRNCH-2 | B2-SW |

---

## Headquarters — Hub

### HQ VLANs

| VLAN | Role | Network |
|---|---|---|
| 110 | Tellers | `10.10.110.0/24` |
| 120 | Supervisors | `10.10.120.0/24` |
| 130 | Admin / General Manager | `10.10.130.0/24` |
| 140 | Monitoring / Zabbix | `10.10.140.0/24` |

### HQ Endpoints

| Role | IP Address |
|---|---|
| General Manager | `10.10.130.10/24` |
| Teller 1 | `10.10.110.11/24` |
| Teller 2 | `10.10.110.12/24` |
| Teller 3 | `10.10.110.13/24` |
| HQ Supervisor | `10.10.120.11/24` |
| Zabbix Monitoring Server | `10.10.140.10/24` |

### HQ WAN Underlay

- **WAN1:** `172.16.10.0/30`
- **WAN2:** `172.16.20.0/30`

---

## Branch 1 — Spoke 1

### Branch 1 VLANs

| VLAN | Role | Network |
|---|---|---|
| 110 | Tellers | `10.11.110.0/24` |
| 120 | Supervisors | `10.11.120.0/24` |

### Branch 1 Endpoints

| Role | IP Address |
|---|---|
| Teller 4 | `10.11.110.11/24` |
| Teller 5 | `10.11.110.12/24` |
| Branch 1 Supervisor | `10.11.120.11/24` |

### Branch 1 WAN Underlay

- **WAN1:** `172.16.10.4/30`
- **WAN2:** `172.16.20.4/30`

---

## Branch 2 — Spoke 2

### Branch 2 VLANs

| VLAN | Role | Network |
|---|---|---|
| 110 | Tellers | `10.12.110.0/24` |
| 120 | Supervisors | `10.12.120.0/24` |

### Branch 2 Endpoints

| Role | IP Address |
|---|---|
| Teller 6 | `10.12.110.11/24` |
| Teller 7 | `10.12.110.12/24` |
| Branch 2 Supervisor | `10.12.120.11/24` |

### Branch 2 WAN Underlay

- **WAN1:** `172.16.20.8/30`
- **WAN2:** `172.16.10.8/30`

---

## WAN Underlay Summary

Every FortiGate site has **two WAN links** connected to the simulated Internet/ISP underlay.

| Site | WAN Link 1 | WAN Link 2 |
|---|---|---|
| HQ / Hub | `172.16.10.0/30` | `172.16.20.0/30` |
| Branch 1 / Spoke 1 | `172.16.10.4/30` | `172.16.20.4/30` |
| Branch 2 / Spoke 2 | `172.16.20.8/30` | `172.16.10.8/30` |

The WAN links provide the **underlay transport**. Banking LAN traffic is carried through the secure overlay.

---

## IPsec SD-WAN Overlay

The FortiGate devices establish the secure WAN overlay across the available underlay paths.

| Overlay | Network |
|---|---|
| VPN-1 | `10.100.100.0/24` |
| VPN-2 | `10.200.200.0/24` |

### BGP

- **BGP Autonomous System:** `65001`
- BGP provides dynamic route exchange across the overlay.

### ADVPN

ADVPN provides **dynamic spoke-to-spoke shortcuts**. When a shortcut is established, Branch 1 and Branch 2 can communicate directly instead of permanently traversing the HQ hub.

The overall architecture remains **hub-and-spoke**, while ADVPN dynamically creates a direct path between the two spokes when required.

---

## SD-WAN

SD-WAN operates across the **two WAN links available at each FortiGate site** and evaluates WAN path health and availability for path selection and failover.

**HQ-FW, BRNCH-1, and BRNCH-2 each have two WAN links.**

---

## Monitoring

The HQ monitoring network uses **VLAN 140**:

- Network: `10.10.140.0/24`
- Zabbix Server: `10.10.140.10/24`

Monitoring covers network behavior such as:

- Latency
- Packet loss
- Jitter
- Bandwidth utilization
- WAN availability
- Routing availability
- SD-WAN SLA status

---

## Testing and Evaluation

The thesis evaluates the implementation through controlled laboratory scenarios:

1. Normal operation
2. WAN degradation
3. WAN failure and automatic failover
4. WAN recovery
5. HQ-to-branch connectivity
6. Branch-to-branch connectivity and ADVPN shortcuts
7. IPsec verification
8. BGP verification
9. Monitoring verification

---

## Repository Structure

The repository intentionally contains **only three main project folders**:

```text
secure-sdwan-fortigate-eve-ng/
├── README.md
├── topology/
│   └── banking-topology.webp
├── configurations/
└── screenshots/
```

### `topology/`

Contains the implemented network topology diagrams and related topology images.

### `configurations/`

Contains configuration files and command outputs for the FortiGate devices, switching, routing, SD-WAN, IPsec, BGP, ADVPN, and monitoring components.

### `screenshots/`

Contains screenshots documenting the implementation, configuration, verification, monitoring, and testing results.

No additional project folders are required at this stage. Content can be organized naturally by filename within these three folders.

---

## Technologies Used

| Technology | Purpose |
|---|---|
| **FortiGate** | Firewall, IPsec VPN, SD-WAN and ADVPN |
| **EVE-NG** | Network emulation platform |
| **IPsec / ESP** | Secure encrypted WAN overlay |
| **SD-WAN** | Multi-WAN path management and resilience |
| **BGP** | Dynamic routing, AS 65001 |
| **ADVPN** | Dynamic spoke-to-spoke shortcuts |
| **Cisco IOSv / IOSvL2** | Internal switching and VLAN infrastructure |
| **Zabbix** | Network monitoring and performance analysis |

---

## Security Validation

The project validates that banking traffic transported across the Internet/ISP underlay is protected by the IPsec overlay.

Security validation includes:

- IPsec tunnel establishment
- ESP traffic verification
- Protection of internal banking addressing across the WAN underlay
- Secure HQ-to-branch communication
- Secure branch-to-branch communication
- Maintaining secure connectivity during WAN failover

**Never upload real passwords, IPsec pre-shared keys, private keys, certificates, Zabbix credentials, API tokens, or other confidential information.**

Use placeholders such as `<PASSWORD>`, `<PSK>`, and `<PRIVATE_KEY>` in configuration examples.

---

## Results

Measured results from the actual laboratory tests will be added to the `screenshots/` and `configurations/` folders as supporting evidence.

Results will cover:

- Latency
- Packet loss
- Jitter
- WAN failover and recovery
- BGP routing
- IPsec encryption
- ADVPN shortcuts
- SD-WAN SLA behavior
- Zabbix monitoring

Performance values will only be documented when supported by actual laboratory evidence.

---

## Academic Context

**Thesis Title:** Design and Implementation of Secure SD-WAN Architecture for Bank Networks Using FortiGate and EVE-NG  
**Project Type:** Graduation / Thesis Project  
**Environment:** EVE-NG network laboratory  
**Architecture:** 1 Hub + 2 Spokes, with 2 WAN links at every FortiGate site  
**Primary Technologies:** FortiGate, SD-WAN, IPsec, ADVPN, BGP, EVE-NG and Zabbix

---

## Repository Status

The repository is prepared for the progressive upload of the actual thesis topology, configuration files, screenshots, verification evidence, and final results.

---

**Secure SD-WAN • FortiGate • Dual-WAN • IPsec • ADVPN • BGP • EVE-NG • Zabbix**