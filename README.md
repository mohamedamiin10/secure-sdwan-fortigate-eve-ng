# Secure SD-WAN Architecture for Bank Networks

> **Design and Implementation of Secure SD-WAN Architecture for Bank Networks Using FortiGate and EVE-NG**

## Overview

This repository contains the implementation, configuration references, topology documentation, testing evidence, and results for a graduation thesis focused on designing and implementing a secure SD-WAN architecture for bank networks.

The project investigates **FortiGate**, **IPsec VPN**, **SD-WAN**, **ADVPN**, **BGP**, and **EVE-NG** to build a secure and resilient WAN architecture using an encrypted Internet-based overlay.

## Objectives

- Design a secure hub-and-spoke SD-WAN architecture for a bank network.
- Establish encrypted IPsec connectivity between headquarters and branches.
- Implement BGP for dynamic route exchange.
- Use SD-WAN performance monitoring and SLA-based path selection.
- Implement ADVPN for efficient inter-branch communication.
- Test automatic WAN failover and network resilience.
- Measure latency, jitter, packet loss, bandwidth, and routing availability.
- Verify that WAN traffic is protected by IPsec encryption.

## Lab Architecture

The EVE-NG laboratory includes an HQ FortiGate hub, Branch 1 and Branch 2 FortiGate spokes, an ISP router representing the Internet underlay, Cisco IOSv/IOSvL2 devices, and Zabbix monitoring.

```text
                         ┌─────────────────┐
                         │   ISP / Internet│
                         │     Underlay    │
                         └────────┬────────┘
                                  │
                           ┌──────┴──────┐
                           │ HQ FortiGate│
                           │     HUB     │
                           └──────┬──────┘
                                  │
                         IPsec / SD-WAN Overlay
                              ┌───┴───┐
                              │       │
                        ┌─────┴──┐ ┌──┴──────┐
                        │Branch 1│ │ Branch 2│
                        │FortiGate│ │FortiGate│
                        └────────┘ └─────────┘

                 ADVPN enables direct branch-to-branch paths
```

## Repository Structure

```text
secure-sdwan-fortigate-eve-ng/
├── README.md
├── docs/
│   ├── architecture/
│   ├── methodology/
│   ├── topology/
│   └── results/
├── configurations/
│   ├── fortigate/
│   │   ├── hq/
│   │   ├── branch1/
│   │   └── branch2/
│   ├── routing/
│   ├── switching/
│   └── monitoring/
├── screenshots/
│   ├── topology/
│   ├── fortigate/
│   ├── sdwan/
│   ├── vpn/
│   ├── routing/
│   ├── monitoring/
│   └── testing/
├── eve-ng/
│   ├── topology/
│   └── notes/
├── testing/
│   ├── connectivity/
│   ├── failover/
│   ├── performance/
│   └── security/
└── references/
```

## Technologies

| Technology | Purpose |
|---|---|
| FortiGate | Firewall, IPsec VPN, SD-WAN and ADVPN |
| EVE-NG | Network emulation and laboratory environment |
| IPsec / ESP | Encrypted WAN overlay |
| BGP | Dynamic routing |
| ADVPN | Dynamic branch-to-branch shortcuts |
| SD-WAN SLA | Link health and path selection |
| Cisco IOSv / IOSvL2 | Routing and switching simulation |
| Zabbix | Network monitoring |

## Testing

The implementation is evaluated under normal WAN operation, WAN degradation, automatic failover, branch-to-branch communication, IPsec/ESP verification, latency and packet-loss measurement, BGP availability, and monitoring/SLA scenarios.

## Results

The completed laboratory evaluation demonstrated low-latency encrypted connectivity, zero packet loss in the normal test scenarios, automatic WAN recovery without manual intervention, protected IPsec traffic across the simulated Internet, successful ADVPN shortcuts, and continuous performance monitoring.

Detailed measurements and evidence will be added under `docs/results/`, `testing/`, and `screenshots/`.

## Security Notice

This repository is for academic and laboratory use. **Never commit real passwords, pre-shared keys, private keys, certificates, API tokens, monitoring credentials, or confidential institutional information.** Replace sensitive values with placeholders such as `<PASSWORD>`, `<PSK>`, and `<PRIVATE_KEY>`.

## Academic Context

**Project:** Design and Implementation of Secure SD-WAN Architecture for Bank Networks Using FortiGate and EVE-NG  
**Focus:** Secure WAN architecture, SD-WAN, IPsec, ADVPN, BGP, resilience, and network monitoring  
**Environment:** EVE-NG virtual network laboratory

## Repository Status

This repository is being prepared as the public documentation and evidence repository for the thesis project. Configuration files, screenshots, topology diagrams, and supporting documentation will be added progressively.

## License

Provided for academic and educational purposes.

---

**Secure SD-WAN • FortiGate • IPsec • ADVPN • BGP • EVE-NG**