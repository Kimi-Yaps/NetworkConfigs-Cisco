# NetworkConfigs-Cisco

### Purpose
This repository serves as a centralized library of **standardized Cisco configuration templates**. It is designed to streamline network deployment by providing pre-verified, reusable snippets for repetitive tasks, reducing manual configuration errors and improving deployment speed.

---

## Core Configuration Modules

### Routing & Layer 3 Services
* **HSRP (Hot Standby Router Protocol):** High-availability gateway configurations.
* **Access Control Lists (ACL):** Templates for both Standard and Extended ACLs.
* **Remote Access:** Secure management setups for Telnet and SSH.
* **Hardening:** ACL-restricted remote access for enhanced VTY line security.

### Firewall & Security (ASA/Firepower)
* **Base Config:** Interface naming, security levels, and IP addressing.
* **DHCP Services:** Configuring the firewall as a DHCP server for local LANs.
* **Routing:** Static routing and OSPF integration for firewall-to-core connectivity.
* **Inspection:** Application-layer inspection policies (Modular Policy Framework).

---

## How to Use
1.  Navigate to the specific `.txt` or `.conf` file in the folders above.
2.  Identify variables in brackets, such as `<IP_ADDRESS>` or `<VLAN_ID>`.
3.  Modify the variables to match your environment.
4.  Apply to the device via Console or SSH.

> **Note:** Always test configurations in a lab environment (GNS3/Cisco Packet Tracer) before deploying to production.

---

## Author
**[Muhammad Iman Hakimi Bin Hashim]**
* **LinkedIn:** [www.linkedin.com/in/iman-hakimi-867a1b2a5]
* **Portfolio:** [https://github.com/Kimi-Yaps/rental-map.git]
