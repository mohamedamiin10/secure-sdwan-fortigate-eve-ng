# Secure SD-WAN Banking Network

> **Design and Implementation of Secure SD-WAN Architecture for Bank Networks Using FortiGate and EVE-NG**

This repository contains the laboratory implementation, configurations, topology documentation, testing evidence, and results for a graduation thesis that designs and evaluates a secure SD-WAN architecture for a banking network.

The implementation is built in **EVE-NG** using **FortiGate firewalls**, an emulated **Internet/ISP router**, Cisco switching, **IPsec VPN**, **SD-WAN**, **BGP**, **ADVPN**, and **Zabbix** monitoring.

---

## 1. Project Overview

The project models a banking network consisting of:

- **Headquarters (HQ)**
- **Branch 1**
- **Branch 2**
- An emulated **Internet/ISP underlay** providing two WAN paths
- An encrypted **IPsec SD-WAN overlay** between the FortiGate sites
- **BGP AS 65001** for dynamic routing
- **ADVPN** for dynamic branch-to-branch shortcuts
- **Zabbix** for network monitoring

The design separates banking users into VLANs and provides secure connectivity between the sites over the simulated Internet.

---

## 2. Implemented Topology

The topology used in this project is **not a generic three-site SD-WAN example**. It follows the actual laboratory topology implemented for the thesis.

```text
                              MANAGEMENT
                                  │
                              Zabbix Server
                              10.10.140.10
                                  │
                              HQ-FW / HQ-SW
                                  │
        ┌─────────────────────────┼─────────────────────────┐
        │                         │                         │
     HQ Users                 HQ VLANs                  HQ-FW
  10.10.x.0/24          VLAN 110 / 120 / 130          FortiGate
                                                          │
                                              ┌───────────┴───────────┐
                                              │                       │
                                           WAN Path 1             WAN Path 2
                                              │                       │
                                              └───────────┬───────────┘
                                                          │
                                                   ISP / Internet
                                                          │
                                  ┌───────────────────────┼───────────────────────┐
                                  │                       │                       │
                              BRNCH-1                 BRNCH-2                  WAN
                              FortiGate                FortiGate              Underlay
                                  │                       │
                               BCH1-SW                 BCH2-SW
                                  │                       │
                             Branch 1 Users          Branch 2 Users

                    ─────── IPsec / SD-WAN Overlay ───────
                    VPN-1: 10.100.100.0/24
                    VPN-2: 10.200.200.0/24
                    BGP: AS 65001
                    ADVPN: direct branch-to-branch shortcuts
```

The detailed topology diagram will be stored under `docs/topology/` when uploaded.

---

## 3. Headquarters Network

### HQ VLANs

| VLAN | Role | Network |
|---|---|---|
| 110 | Tellers | `10.10.110.0/24` |
| 120 | Supervisors | `10.10.120.0/24` |
| 130 | Admin / General Manager | `10.10.130.0/24` |
| 140 | Monitoring / Zabbix | `10.10.140.0/24` |

### HQ Endpoints

| Device / Role | IP Address |
|---|---|
| General Manager | `10.10.130.10/24` |
| Teller 1 | `10.10.110.11/24` |
| Teller 2 | `10.10.110.12/24` |
| Teller 3 | `10.10.110.13/24` |
| Supervisor | `10.10.120.10/24` |
| Zabbix Monitoring Server | `10.10.140.10/24` |

The HQ site uses **HQ-FW** as the FortiGate security gateway and **HQ-SW** for the internal switching/VLAN environment.

---

## 4. Branch 1 Network

### Branch 1 VLANs

| VLAN | Role | Network |
|---|---|---|
| 110 | Tellers | `10.11.110.0/24` |
| 120 | Supervisors | `10.11.120.0/24` |

### Branch 1 Endpoints

| Device / Role | IP Address |
|---|---|
| Teller 4 | `10.11.110.11/24` |
| Teller 5 | `10.11.110.12/24` |
| Supervisor | `10.11.120.11/24` |

Branch 1 uses **BRNCH-1** as its FortiGate and **BCH1-SW** as its internal switch.

---

## 5. Branch 2 Network

### Branch 2 VLANs

| VLAN | Role | Network |
|---|---|---|
| 110 | Tellers | `10.12.110.0/24` |
| 120 | Supervisors | `10.12.120.0/24` |

### Branch 2 Endpoints

| Device / Role | IP Address |
|---|---|
| Teller 6 | `10.12.110.11/24` |
| Teller 7 | `10.12.110.12/24` |
| Supervisor | `10.12.120.11/24` |

Branch 2 uses **BRNCH-2** as its FortiGate and **BCH2-SW** as its internal switch.

---

## 6. WAN / Internet Underlay

The ISP/Internet router provides the simulated WAN underlay. Each FortiGate has two WAN connections, allowing the SD-WAN implementation to evaluate multiple paths and perform failover.

### HQ WAN Links

- WAN path 1: `172.16.10.0/30`
- WAN path 2: `172.16.20.0/30`

### Branch 1 WAN Links

- WAN path 1: `172.16.10.4/30`
- WAN path 2: `172.16.20.4/30`

### Branch 2 WAN Links

- WAN path 1: `172.16.20.8/30`
- WAN path 2: `172.16.10.8/30`

These networks represent the **underlay**. The banking LAN networks are carried through the encrypted overlay rather than being exposed directly across the simulated Internet.

---

## 7. IPsec SD-WAN Overlay

The secure overlay is implemented using IPsec VPN connections between the FortiGate sites.

| Overlay | Network |
|---|---|
| VPN-1 | `10.100.100.0/24` |
| VPN-2 | `10.200.200.0/24` |

The overlay provides encrypted site-to-site connectivity over the Internet/ISP underlay.

### BGP

- **Autonomous System:** `65001`
- BGP is used for dynamic exchange of the site LAN routes across the overlay.

### ADVPN

ADVPN is implemented to allow eligible branch-to-branch traffic to establish direct shortcuts rather than permanently traversing the HQ FortiGate. This improves the path used for inter-branch communication when the shortcut is established.

---

## 8. SD-WAN and Resilience

The FortiGate SD-WAN implementation monitors the available WAN paths and uses performance information to make path-selection decisions.

The thesis evaluates the network under conditions including:

1. Normal operation with both WAN paths available.
2. WAN degradation.
3. WAN path failure.
4. Automatic failover and recovery.
5. Branch-to-branch communication.
6. IPsec tunnel availability.
7. Routing/BGP availability.
8. Network performance monitoring.

---

## 9. Monitoring

A **Zabbix monitoring server** is connected to the HQ monitoring network:

- Monitoring VLAN: `10.10.140.0/24`
- Zabbix Server: `10.10.140.10`

Monitoring is used to observe network health and evaluate measurements such as:

- Latency
- Packet loss
- Jitter
- Bandwidth utilization
- WAN availability
- Routing availability
- SD-WAN SLA status

---

## 10. Security Validation

Security testing focuses on confirming that traffic crossing the WAN underlay is protected by the IPsec overlay.

The project includes verification of:

- IPsec tunnel establishment
- ESP traffic on the WAN
- Protection of internal banking networks across the underlay
- Secure site-to-site communication
- Failover while maintaining encrypted connectivity

Sensitive credentials and secrets must never be committed to this repository.

---

## 11. Test Scenarios

| Scenario | Purpose |
|---|---|
| Normal operation | Establish baseline connectivity and performance |
| WAN degradation | Evaluate SD-WAN path selection |
| WAN failure | Verify automatic failover |
| WAN recovery | Verify restoration of connectivity |
| HQ ↔ Branch | Validate inter-site connectivity |
| Branch ↔ Branch | Validate ADVPN/direct shortcut behavior |
| IPsec inspection | Verify encrypted WAN traffic |
| BGP verification | Confirm dynamic route exchange |
| Zabbix monitoring | Observe network performance and availability |

---

## 12. Repository Structure

```text
secure-sdwan-fortigate-eve-ng/
├── README.md
├── docs/
│   ├── topology/
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
│   ├── vpn/
│   ├── bgp/
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

## 13. Technologies Used

| Technology | Role in the Project |
|---|---|
| **FortiGate** | Firewall, IPsec VPN, SD-WAN and ADVPN |
| **EVE-NG** | Network emulation platform |
| **IPsec / ESP** | Encrypted WAN overlay |
| **BGP** | Dynamic routing, AS 65001 |
| **ADVPN** | Dynamic branch-to-branch shortcuts |
| **SD-WAN** | Multi-WAN path selection and resilience |
| **Cisco IOSv / IOSvL2** | Routing/switching and VLAN infrastructure |
| **Zabbix** | Network monitoring and performance analysis |

---

## 14. Thesis Results

The final repository will contain the measured results and evidence from the implemented lab, including latency, packet loss, failover, routing, IPsec security validation, and inter-branch performance.

Measured results should be documented from the actual laboratory tests rather than assumed values. Supporting screenshots, command outputs, and test tables will be placed in `testing/`, `docs/results/`, and `screenshots/`.

---

## 15. Academic Context

**Thesis Title:** Design and Implementation of Secure SD-WAN Architecture for Bank Networks Using FortiGate and EVE-NG

**Project Type:** Graduation / Thesis Project  
**Environment:** EVE-NG network laboratory  
**Architecture:** HQ + Branch 1 + Branch 2  
**Primary Technologies:** FortiGate, SD-WAN, IPsec, ADVPN, BGP, EVE-NG and Zabbix

---

## 16. Security Notice

This repository is intended for academic and laboratory use.

**Never commit:**

- FortiGate passwords
- IPsec pre-shared keys
- Private keys
- Certificates containing sensitive material
- Zabbix credentials
- API tokens
- Real production credentials
- Confidential institutional information

Use placeholders such as `<PASSWORD>`, `<PSK>`, and `<PRIVATE_KEY>` when documenting configurations.

---

## 17. Repository Status

The repository is being organized as the public technical documentation and evidence repository for the thesis. Configuration files, EVE-NG documentation, topology diagrams, screenshots, test results, and supporting materials will be added progressively.

---

**Secure SD-WAN • FortiGate • IPsec • ADVPN • BGP • EVE-NG • Zabbix**