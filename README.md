# Secure SD-WAN Banking Network

> **Design and Implementation of Secure SD-WAN Architecture for Bank Networks Using FortiGate and EVE-NG**

This repository contains the laboratory implementation, configurations, topology documentation, testing evidence, and results for a graduation thesis focused on designing and implementing a secure SD-WAN architecture for a banking network.

The project is implemented in **EVE-NG** using **FortiGate firewalls**, an emulated **Internet/ISP router**, Cisco switching, **IPsec VPN**, **SD-WAN**, **BGP**, **ADVPN**, and **Zabbix** monitoring.

---

## Architecture at a Glance

The implemented architecture is a **hub-and-spoke topology with one hub and two spokes**:

- **1 Hub:** HQ-FW
- **2 Spokes:** BRNCH-1 and BRNCH-2
- **2 WAN links at the hub:** HQ-FW WAN1 + WAN2
- **2 WAN links at Branch 1:** BRNCH-1 WAN1 + WAN2
- **2 WAN links at Branch 2:** BRNCH-2 WAN1 + WAN2
- **1 simulated Internet/ISP router** providing the WAN underlay
- **IPsec SD-WAN overlay** running across the WAN underlay
- **BGP AS 65001** for dynamic routing
- **ADVPN** for direct branch-to-branch shortcuts

### Actual Topology Diagram

![Secure SD-WAN Banking Topology](docs/topology/banking-topology.webp)

**Figure 1 — Implemented Secure SD-WAN Banking Topology (HQ Hub + Branch 1 Spoke + Branch 2 Spoke).**

The diagram above is the actual topology used for the thesis. **HQ-FW is the hub, BRNCH-1 is Spoke 1, and BRNCH-2 is Spoke 2. Each FortiGate site has two WAN connections to the simulated Internet/ISP underlay.**

---

## 1. Sites and Devices

| Site | Role | FortiGate | Internal Switch |
|---|---|---|---|
| HQ | **Hub** | HQ-FW | HQ-SW |
| Branch 1 | **Spoke 1** | BRNCH-1 | BCH1-SW |
| Branch 2 | **Spoke 2** | BRNCH-2 | BCH2-SW |

The ISP/Internet device is used to simulate the WAN underlay between the three FortiGate sites.

---

## 2. Headquarters — Hub

HQ is the **hub site** of the hub-and-spoke SD-WAN architecture.

### HQ LAN VLANs

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

HQ-FW has **two WAN links** toward the simulated Internet/ISP router:

- **WAN1:** `172.16.10.0/30`
- **WAN2:** `172.16.20.0/30`

These are two separate underlay paths used by the SD-WAN implementation.

---

## 3. Branch 1 — Spoke 1

Branch 1 is the **first spoke** connected to the HQ hub through the simulated WAN underlay.

### Branch 1 LAN VLANs

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

BRNCH-1 has **two WAN links** toward the simulated Internet/ISP router:

- **WAN1:** `172.16.10.4/30`
- **WAN2:** `172.16.20.4/30`

---

## 4. Branch 2 — Spoke 2

Branch 2 is the **second spoke** connected to the HQ hub through the simulated WAN underlay.

### Branch 2 LAN VLANs

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

BRNCH-2 has **two WAN links** toward the simulated Internet/ISP router:

- **WAN1:** `172.16.20.8/30`
- **WAN2:** `172.16.10.8/30`

---

## 5. WAN Underlay Summary

The WAN underlay contains **two logical paths**, and every FortiGate site connects to both paths through the simulated Internet/ISP router.

| Site | WAN Link 1 | WAN Link 2 |
|---|---|---|
| HQ / Hub | `172.16.10.0/30` | `172.16.20.0/30` |
| Branch 1 / Spoke 1 | `172.16.10.4/30` | `172.16.20.4/30` |
| Branch 2 / Spoke 2 | `172.16.20.8/30` | `172.16.10.8/30` |

The **underlay** provides transport. The banking LAN traffic is carried through the secure overlay.

---

## 6. IPsec SD-WAN Overlay

The FortiGate devices establish the secure WAN overlay across the two available underlay paths.

The topology identifies the following overlay networks:

| Overlay | Network |
|---|---|
| VPN-1 | `10.100.100.0/24` |
| VPN-2 | `10.200.200.0/24` |

The overlay is used to securely carry traffic between HQ and the two branch sites.

### BGP

- **BGP Autonomous System:** `65001`
- BGP provides dynamic route exchange for the site networks over the overlay.

### ADVPN

ADVPN is used for **dynamic branch-to-branch shortcuts**. When a shortcut is established, traffic between Branch 1 and Branch 2 can use a direct spoke-to-spoke path instead of permanently traversing the HQ hub.

The architecture therefore remains **hub-and-spoke**, while ADVPN can dynamically create a direct path between the two spokes when required.

---

## 7. SD-WAN Function

SD-WAN operates across the **two WAN links available at each FortiGate site**.

The implementation evaluates WAN path health and availability and can react to WAN degradation or failure.

**Each of the three FortiGates has two WAN links.**

---

## 8. Monitoring

The HQ monitoring network uses **VLAN 140**:

- Network: `10.10.140.0/24`
- Zabbix Server: `10.10.140.10/24`

Zabbix is used to monitor and evaluate network behavior, including:

- Latency
- Packet loss
- Jitter
- Bandwidth utilization
- WAN availability
- Routing availability
- SD-WAN SLA status

---

## 9. Testing and Evaluation

The thesis evaluates the implemented architecture through controlled laboratory scenarios:

1. **Normal operation** — verify baseline connectivity and performance.
2. **WAN degradation** — observe SD-WAN path-health behavior.
3. **WAN failure** — verify automatic failover to the available path.
4. **WAN recovery** — verify restoration of the recovered path.
5. **HQ ↔ Branch communication** — verify secure hub-to-spoke connectivity.
6. **Branch 1 ↔ Branch 2 communication** — verify ADVPN shortcut behavior.
7. **IPsec verification** — verify encrypted WAN traffic.
8. **BGP verification** — verify dynamic route exchange.
9. **Monitoring verification** — observe WAN and overlay performance using Zabbix.

---

## 10. Repository Structure

```text
secure-sdwan-fortigate-eve-ng/
├── README.md
├── docs/
│   ├── topology/
│   │   └── banking-topology.webp
│   ├── architecture/
│   ├── methodology/
│   └── results/
├── configurations/
│   ├── fortigate/
│   │   ├── hq/
│   │   ├── branch1/
│   │   └── branch2/
│   ├── routing/
│   ├── switching/
│   └── monitoring/
├── eve-ng/
│   ├── topology/
│   └── notes/
├── screenshots/
│   ├── topology/
│   ├── fortigate/
│   ├── sdwan/
│   ├── ipsec/
│   ├── bgp/
│   ├── advpn/
│   ├── monitoring/
│   └── testing/
├── testing/
│   ├── connectivity/
│   ├── failover/
│   ├── performance/
│   └── security/
└── references/
```

---

## 11. Technologies Used

| Technology | Purpose |
|---|---|
| **FortiGate** | Firewall, IPsec VPN, SD-WAN and ADVPN |
| **EVE-NG** | Network emulation platform |
| **IPsec / ESP** | Secure encrypted WAN overlay |
| **SD-WAN** | Multi-WAN path management and resilience |
| **BGP** | Dynamic routing, AS 65001 |
| **ADVPN** | Dynamic spoke-to-spoke shortcuts |
| **Cisco IOSv / IOSvL2** | Internal routing, switching and VLAN infrastructure |
| **Zabbix** | Network monitoring and performance analysis |

---

## 12. Security Validation

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

## 13. Results

Measured results from the actual laboratory tests will be documented in `docs/results/`, `testing/`, and `screenshots/`.

The repository will include supporting evidence for:

- Latency
- Packet loss
- Jitter
- WAN failover and recovery
- BGP routing
- IPsec encryption
- ADVPN shortcuts
- SD-WAN SLA behavior
- Zabbix monitoring

No performance value is presented here unless it is supported by the corresponding laboratory evidence.

---

## 14. Academic Context

**Thesis Title:** Design and Implementation of Secure SD-WAN Architecture for Bank Networks Using FortiGate and EVE-NG  
**Project Type:** Graduation / Thesis Project  
**Environment:** EVE-NG network laboratory  
**Architecture:** 1 Hub + 2 Spokes, with 2 WAN links at every FortiGate site  
**Primary Technologies:** FortiGate, SD-WAN, IPsec, ADVPN, BGP, EVE-NG and Zabbix

---

## 15. Repository Status

This repository is being organized as the public technical documentation and evidence repository for the thesis. Configuration files, topology diagrams, EVE-NG documentation, screenshots, test results, and supporting materials will be added progressively.

---

**Secure SD-WAN • FortiGate • Dual-WAN • IPsec • ADVPN • BGP • EVE-NG • Zabbix**