# 🌐 Lab 04: Enterprise Multi-Site WAN Network with OSPF

## 📌 Project Overview
This project implements a multi-site enterprise network connecting a **Headquarters (HQ)** office and a remote **Branch** office over a point-to-point WAN link. 

At Headquarters, internal departments (**HR - VLAN 10** and **Sales - VLAN 20**) are logically segmented on a Layer 2 switch and routed via **Router-on-a-Stick (802.1Q sub-interfaces)**. Dynamic routing using **OSPFv2 (Process 1, Area 0)** is deployed between HQ and Branch routers to provide automatic route exchange and full end-to-end connectivity to internal corporate web and DNS services (`www.diablo.local`).

---

## 📊 IP Addressing & VLAN Table

| Site / Location | Department / Segment | VLAN | Interface | IP Subnet | Default Gateway |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **HQ Office** | HR Department | VLAN 10 | `Gig0/0/0.10` | `192.168.10.0/24` | `192.168.10.1` |
| **HQ Office** | Sales Department | VLAN 20 | `Gig0/0/0.20` | `192.168.20.0/24` | `192.168.20.1` |
| **WAN Link** | HQ to Branch Point-to-Point | N/A | HQ `Gig0/0/1` <-> Branch `Gig0/0/0` | `10.0.0.0/30` | `.1` (HQ) / `.2` (Branch) |
| **Branch Office** | Branch LAN & Server | Native | `Gig0/0/1` | `172.16.10.0/24` | `172.16.10.1` |

---

## ⚙️ Key Technical Implementations

1. **VLAN Segmentation & 802.1Q Trunking:**
   * Created VLAN 10 (`HR`) and VLAN 20 (`Sales`) on the HQ Switch.
   * Configured an 802.1Q trunk link carrying tagged traffic to the HQ Router.
2. **Router-on-a-Stick (Inter-VLAN Routing):**
   * Configured sub-interfaces `Gig0/0/0.10` and `Gig0/0/0.20` with `encapsulation dot1Q` to route traffic between HR and Sales.
3. **Dynamic Routing with OSPFv2:**
   * Enabled `router ospf 1` (Area 0) across both HQ and Branch routers using exact wildcard masks.
   * Verified neighbor adjacency and dynamic route learning (`O` routes in `show ip route`).
4. **DHCP & Internal DNS Services:**
   * Configured IOS DHCP pools for automatic IP allocation and excluded gateway/server addresses.
   * Deployed internal DNS resolution mapping `www.diablo.local` to the corporate web server.

---

## 🔍 Troubleshooting & Lessons Learned

* **Issue 1: Physical WAN Port Conflict with Sub-interfaces**
  * *Symptom:* Attempting to configure the WAN IP on `Gig0/0/0` caused a conflict with existing 802.1Q sub-interfaces, and the WAN link remained down (red status).
  * *Resolution:* Reassigned the WAN link to dedicated physical interface `Gig0/0/1` on the HQ Router and issued `no shutdown` on both ends.
* **Issue 2: Cross-Site Unreachability (Missing OSPF Routes)**
  * *Symptom:* Pings from Branch PCs to HQ VLANs failed with `Destination host unreachable`.
  * *Resolution:* Inspected `show ip route` and discovered missing OSPF (`O`) entries. Added all directly connected LAN and WAN subnets to OSPF Process 1 Area 0 on both routers and verified the Branch host Default Gateway (`172.16.10.1`).

---

## ✅ Verification Results
* **Inter-VLAN Ping (HR <-> Sales):** 100% Success (`0% packet loss`)
* **Cross-Site WAN Ping (Branch <-> HQ):** 100% Success (`0% packet loss`)
* **Web & DNS Resolution:** Branch hosts successfully loaded `http://www.diablo.local` via Web Browser.

---

## 📂 Project Files
* **Simulation File:** `Lab4-MultiSite-OSPF.pkt`
* **HQ Router & Switch Config:** [`configs/HQ-Router-running-config.txt`](./configs/HQ-Router-running-config.txt)
* **Branch Router Config:** [`configs/Branch-Router-running-config.txt`](./configs/Branch-Router-running-config.txt)
