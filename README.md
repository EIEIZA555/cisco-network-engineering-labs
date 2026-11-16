# 🌐 Cisco Network Engineering Hands-On Portfolio

Welcome to my Network Engineering portfolio repository. This repository contains enterprise-grade network architectures, configurations, and troubleshooting case studies built and verified in **Cisco Packet Tracer**.

Each project is designed to simulate real-world corporate infrastructure challenges, including **VLAN Segmentation, Inter-VLAN Routing (Router-on-a-Stick), Dynamic Routing (OSPFv2), Perimeter Firewall Security (Cisco ASA 5506-X), DMZ Architecture, Dynamic PAT (NAT Overload), and Stateful Packet Inspection**.

---

## 📂 Featured Network Projects

| Lab | Project Title | Core Technologies | Key Achievement |
| :--- | :--- | :--- | :--- |
| **[Lab 04](./Lab04-Enterprise-OSPF-WAN)** | **Enterprise Multi-Site WAN with OSPF** | OSPFv2 (Area 0), 802.1Q Trunking, Router-on-a-Stick, DHCP, DNS | Connected HQ (VLANs 10/20) and Branch networks across a WAN link with 100% dynamic route convergence and internal domain resolution (`www.diablo.local`). |
| **[Lab 05](./Lab05-Enterprise-ASA-Firewall)** | **Enterprise Perimeter Security & DMZ (Cisco ASA)** | Cisco ASA 5506-X, Security Zones, Dynamic PAT, Extended ACLs, MPF Inspection | Built a 3-zone perimeter firewall (Inside, DMZ, Outside) isolating public web servers, enabling Internet PAT, and enforcing granular ICMP/HTTP security policies. |

---

## 🛠️ Technical Skills Demonstrated

* **Routing & Switching:** Static & Default Routing, OSPFv2 (Single-Area), VLANs, 802.1Q Trunking, Inter-VLAN Routing (Sub-interfaces & Routed Ports)
* **Network Security:** Cisco ASA Firewall Configuration, Security Levels (0–100), Extended Access Control Lists (ACLs), Modular Policy Framework (MPF) Stateful Inspection (`inspect icmp`, `inspect http`), DMZ Segmentation
* **IP Services:** Dynamic PAT (NAT Overload), Object NAT, IOS & ASA DHCP Server (`dhcpd`), DNS Resolution
* **Troubleshooting:** OSI Layer 1–7 Diagnostics, Routing Table Analysis (`show ip route`, `show route`), NAT Translation Verification (`show nat`, `show xlate`), Packet Path Tracing

---

## 🚀 How to Use This Repository

1. Navigate into each project folder (`Lab04-Enterprise-OSPF-WAN` or `Lab05-Enterprise-ASA-Firewall`).
2. Read the project-specific `README.md` for topology details, IP addressing tables, and troubleshooting logs.
3. Review the complete CLI configurations inside the `configs/` directory.
4. Download the `.pkt` simulation file to test the live network in **Cisco Packet Tracer**.
