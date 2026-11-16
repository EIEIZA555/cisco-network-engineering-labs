# 🔒 Lab 05: Enterprise Perimeter Security & DMZ Architecture (Cisco ASA 5506-X)

## 📌 Project Overview
This project designs and implements a multi-zone enterprise perimeter security architecture using a **Cisco ASA 5506-X Next-Generation Firewall**. 

The network is segmented into distinct security zones based on Cisco ASA Security Levels:
* **Inside Zones (`inside_hr` & `inside_sales` - Level 100):** Trusted internal corporate departments receiving dynamic IP addressing via ASA `dhcpd`.
* **DMZ Zone (`DMZ` - Level 50):** Semi-trusted demilitarized zone hosting the internal corporate web server (`192.168.100.100`).
* **Outside Zone (`Outside` - Level 0):** Untrusted public WAN uplink connected to the ISP router (`203.0.113.1`) and external public web server (`198.51.100.10`).

---

## 📊 Security Zones & Interface Addressing

| Security Zone (`nameif`) | ASA Physical Port | Security Level | IP Subnet | ASA Interface IP | Connected Segment |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`inside_hr`** | `GigabitEthernet 1/7` | **100** | `10.10.10.0/24` | `10.10.10.1` | Corporate Switch (VLAN 10 - HR) |
| **`inside_sales`** | `GigabitEthernet 1/8` | **100** | `10.10.20.0/24` | `10.10.20.1` | Corporate Switch (VLAN 20 - Sales) |
| **`DMZ`** | `GigabitEthernet 1/3` | **50** | `192.168.100.0/24` | `192.168.100.1` | DMZ Web Server (`192.168.100.100`) |
| **`Outside`** | `GigabitEthernet 1/1` | **0** | `203.0.113.0/30` | `203.0.113.2` | ISP Router (`203.0.113.1`) -> Public Web (`198.51.100.10`) |

---

## ⚙️ Key Security & Networking Implementations

1. **Dedicated Physical Routed Interfaces (ASA-to-Switch):**
   * Allocated separate physical ASA interfaces (`Gig1/7` and `Gig1/8`) mapped to dedicated access ports (VLAN 10 and VLAN 20) on the internal Layer 2 switch.
2. **Dynamic PAT (NAT Overload) via Network Objects:**
   * Configured Auto NAT (`OBJ_HR` and `OBJ_Sales`) translating private internal subnets (`10.10.10.0/24` and `10.10.20.0/24`) to the single public IP on the `Outside` interface (`203.0.113.2`).
3. **Modular Policy Framework (MPF) Stateful Inspection:**
   * Customized `global_policy` under `inspection_default` to statefully inspect **ICMP** (`inspect icmp`) and **HTTP** (`inspect http`) traffic, dynamically permitting return echo replies and web traffic across security zones.
4. **Granular Extended ACL Policy (`SALES_POLICY`):**
   * Enforced least-privilege access control blocking the Sales department (`10.10.20.0/24`) from sending ICMP pings to the DMZ Server (`192.168.100.100`), while explicitly permitting HTTP web access and Internet egress.

---

## 🔍 Troubleshooting & Engineering Case Studies

* **Case 1: Overcoming Packet Tracer 802.1Q Sub-interface Limitations on ASA**
  * *Challenge:* Cisco Packet Tracer's ASA 5506-X image does not support `vlan` encapsulation on sub-interfaces (`Gig1/2.10`), nor does it support the `clear configure` CLI syntax.
  * *Solution:* Re-architected the Inside distribution design from Router-on-a-Stick to **Dedicated Physical Routed Links** (`Gig1/7` for HR, `Gig1/8` for Sales) connected to untagged VLAN access ports on the switch.
* **Case 2: Resolving Implicit Deny Drops from Reversed Inbound ACLs**
  * *Challenge:* Initial deployment of an inbound ACL on `inside_hr` caused 100% packet loss (`Request timed out`) to both DMZ and Outside destinations.
  * *Solution:* Diagnosed reversed source/destination parameters triggering the ASA's implicit deny rule. Unbound the redundant ACL (`no access-group HR_IN in interface inside_hr`) to leverage the native Security Level 100 -> 50/0 outbound forwarding behavior.
* **Case 3: End-to-End NAT & Stateful Return Verification**
  * *Challenge:* Verifying whether outbound web timeouts stemmed from NAT failure, ISP routing, or application inspection.
  * *Solution:* Used `show nat` and `show xlate` on the ASA to confirm active PAT translations (`translate_hits = 201, untranslate_hits = 142`), enabled `inspect http` in the global policy map, and resolved Packet Tracer URL parsing quirks.

---

## ✅ Verification Summary

| Source Host | Destination | Protocol / Test | Expected Result | Verified Status |
| :--- | :--- | :--- | :--- | :--- |
| **PC0 (HR)** | `10.10.10.1` (ASA Gateway) | ICMP Ping | Permit | ✅ `Reply (0% loss)` |
| **PC0 (HR)** | `192.168.100.100` (DMZ Server) | ICMP Ping & HTTP | Permit | ✅ `Reply (0% loss)` & Web Loaded |
| **PC0 (HR)** | `198.51.100.10` (Public Web) | ICMP Ping & HTTP (via PAT) | Permit | ✅ `translate_hits` & Web Loaded |
| **PC1 (Sales)** | `192.168.100.100` (DMZ Server) | ICMP Ping | **Deny (Blocked by ACL)** | ✅ `Request timed out` |
| **PC1 (Sales)** | `192.168.100.100` & `198.51.100.10` | HTTP Web Browser | Permit | ✅ Web Loaded Successfully |

---

## 📂 Project Files
* **Simulation File:** `Lab5-ASA-DMZ-Security.pkt`
* **Cisco ASA 5506-X Full Config:** [`configs/ASA5506-running-config.txt`](./configs/ASA5506-running-config.txt)
* **ISP Router & Switch Config:** [`configs/ISP-and-Switch-config.txt`](./configs/ISP-and-Switch-config.txt)
