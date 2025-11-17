# In this file is the technical information about the network.

# Network Topology – Security Onion Monitoring Lab

This document describes the technical network topology used in the Security Onion + Kali + Victim monitoring lab.  
It includes the virtual machines, their network interfaces, IP addressing, network segments, and the purpose of each network connection.

---

## 1. Virtual Machines Overview

### **1. Kali Linux (Attacker)**
- **OS:** Kali Linux Rolling  
- **Role:** Attacker machine used to generate malicious traffic for detection and monitoring  
- **Network Interfaces:**
  - **Adapter 1:** NAT  
    - Provides internet access for tool updates and package installations  
  - **Adapter 2:** Host-only (vboxnet0)  
    - **IP:** `192.168.56.102`  
    - Used to target the victim machine and communicate within the lab network

---

### **2. Victim VM (Target)**
- **OS:** Windows or Linux (depending on your lab setup)  
- **Role:** Target machine used for scans, enumeration, and exploit attempts  
- **Network Interfaces:**
  - **Adapter 1:** NAT  
    - Provides internet access for updates  
  - **Adapter 2:** Host-only (vboxnet0)  
    - **IP:** `192.168.56.110`  
    - Receives attack traffic from Kali  
    - Visible to Security Onion’s sniffing interface

---

### **3. Security Onion (IDS + SIEM)**
- **OS:** Security Onion 2  
- **Role:** Intrusion detection (Suricata), network monitoring (Zeek), and SIEM (Elastic/Kibana)  
- **Network Interfaces:**
  - **Adapter 1:** Bridged (Management interface)  
    - **IP:** `<YOUR_SO_MANAGEMENT_IP>`  
    - Used to access the Security Onion web interface from the host browser  
  - **Adapter 2:** Host-only (Sniffing interface)  
    - **No IP (0.0.0.0)**  
    - Promiscuous mode enabled  
    - Receives mirrored traffic from the host-only network (`vboxnet0`)  
    - Dedicated solely to packet capture and Suricata/Zeek processing  

---

## 2. Network Segments

### **Host-only Network (vboxnet0)**
- **Subnet:** `192.168.56.0/24`  
- **Purpose:** Internal lab network where attacker, victim, and Security Onion interact  
- **Traffic:**  
  - Attacks launched by Kali  
  - Victim’s responses  
  - Security Onion monitors all traffic passively  

Security Onion’s sniffing NIC has **no IP** but can see all packets.

---

### **NAT Network**
- **Used by:** Kali and Victim  
- **Purpose:** External internet access for updates, tools, and downloads  
- **Not monitored:** NAT traffic does **not** pass through Security Onion  

---

### **Bridged Network**
- **Used by:** Security Onion management NIC  
- **Purpose:**  
  - Allows your *host machine* to access  
    - Kibana / Elastic  
    - Security Onion Console  
  - Places SO on the same network as your real PC for easy management  

---

## 3. Machine Roles

### **Kali Linux**
- Generates malicious or suspicious traffic such as:
  - Nmap scans (SYN, FIN, Xmas, idle)  
  - Nikto  
  - SSH brute force  
  - HTTP probing  
- Simulates attacker behavior in a SOC environment

### **Victim Machine**
- Receives attacks from Kali  
- Produces detectable log and network traffic  
- Allows you to validate detection rules and alerting

### **Security Onion (SOC Platform)**
- Runs Suricata for network intrusion detection  
- Runs Zeek for network protocol analysis  
- Stores logs in Elasticsearch  
- Provides dashboards and alerting via Kibana / SO Console  
- Acts as the central security monitoring node

---

## 4. Purpose of Each NIC

| Machine        | NIC / Adapter | Purpose |
|----------------|---------------|---------|
| **Kali**       | Adapter 1 (NAT) | Internet access |
|                | Adapter 2 (Host-only) | Attack traffic to victim |
| **Victim**     | Adapter 1 (NAT) | Internet access |
|                | Adapter 2 (Host-only) | Receives attack traffic |
| **Security Onion** | Adapter 1 (Bridged) | Management UI access |
|                   | Adapter 2 (Host-only, no IP) | Sniffing traffic for Suricata/Zeek |

---

## 5. Special Configuration Notes

- **Suricata enabled** on Security Onion for packet-based intrusion detection  
- **ET Open Rules** activated to expand detection coverage  
- **Promiscuous mode** enabled on SO’s sniffing NIC  
- **Packetbeat / Zeek** optionally collecting protocol metadata  
- **Tests performed:**
  - Multiple Nmap scans against victim  
  - Basic port scanning  
  - Service enumeration  
  - Web scanning (optional)  
  - SSH brute-force attempts (optional)

These tests generate alerts visible in:
- Security Onion Console  
- Kibana / Elastic  
- Suricata fast.log  

---

## 6. Summary

This topology simulates a real small-scale SOC network where:
- an attacker (Kali)  
- targets a victim machine  
- and Security Onion passively monitors the network  
- to generate actionable alerts and logs  

This setup demonstrates skills in:
- blue team monitoring  
- IDS/IPS configuration  
- threat simulation  
- SIEM analysis  
- network design  
