# In this file is the technical information about the network.

## Network Topology – Security Onion Monitoring Lab

This document describes the network topology used in the Security Onion + Kali + Target monitoring lab.  
It includes the virtual machines, their network interfaces, IP addressing and the purpose of each network connection.

---

### Virtual Machines

#### **1. Security Onion (IDS + SIEM)**
- **OS:** Security Onion 2.
- **Role:** Intrusion detection, network monitoring, and SIEM.
- **Network Interfaces:**
  - **Adapter 1:** Bridged (Management interface)  
    - **IP:** `192.168.178.xyz`. (omitting by choice for best practice)
    - Used to access the Security Onion web interface from the host browser.  
  - **Adapter 2:** Host-only (Sniffing interface)  
    - **IP:** `192.168.56.101`.
    - Promiscuous mode enabled.

---

#### **2. Kali Linux (Attacker)**
- **OS:** Kali Linux.
- **Role:** Generate attacks for the purpose of detection and monitoring.
- **Network Interfaces:**
  - **Adapter 1:** NAT  
    - Provides internet access for tool updates and package installations.  
  - **Adapter 2:** Host-only (vboxnet0)  
    - **IP:** `192.168.56.102`.

---

#### **3. Victim VM (Target)**
- **OS:** Linux.
- **Role:** Target machine used for exploit attempts.  
- **Network Interfaces:**
  - **Adapter 1:** NAT  
    - Provides internet access for updates.  
  - **Adapter 2:** Host-only (vboxnet0)  
    - **IP:** `192.168.56.103`.
    - Receives attack traffic from Kali.
    - "Sniffable" to Security Onion’s sniffing interface.

---

#### **NAT Network**
- **Used by:** Kali and Victim.
- **Purpose:** External internet access for updates, tools, and downloads.  
- **Not monitored:** NAT traffic does not pass through Security Onion.

---

#### **Bridged Network**
- **Used by:** Security Onion management NIC  
- **Purpose:**  
  - Allows host machine to access.  
    - Kibana / Elastic.
    - Security Onion Console.
  - Places SO on the same network as a real PC for easy management.

---

### Notes

- **Suricata enabled** on Security Onion for packet-based intrusion detection.
- **ET Open Rules** activated to expand detection coverage.
- **Promiscuous mode** enabled on SO’s sniffing NIC.
- **Tests performed:**
  - Multiple Nmap scans against victim.
  - Basic port scanning.
- These tests generate alerts visible in Security Onion Console.

---

### Summary

This topology simulates a small-scale SOC network where an attacker (Kali) targets a victim machine  
and Security Onion passively monitors the network to generate actionable alerts and logs  

This setup demonstrates skills in:
- blue team monitoring  
- IDS/IPS configuration  
- threat simulation  
- SIEM analysis  
- network design  
