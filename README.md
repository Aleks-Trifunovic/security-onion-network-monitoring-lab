# Security Onion Network Monitoring Lab

### This lab simulates a small SOC environment with Security Onion as IDS/SIEM, a Kali attacker and a Linux Machine as a target. The goal of the lab is to generate realistic attacker activity, capture and analyze the traffic, and validate network detection capabilities.

-----
### Lab Objective

To build and document a functional IDS/SIEM environment that demonstrates:

- Network-based threat detection
- Suricata alerting
- Security Onion event analysis
- Basic attacker behavior simulation
- Understanding of monitored vs. unmonitored network segments

-----

Tools & Technologies:

Security Onion
- Suricata (IDS)
- Zeek (protocol analysis)
- Elastic / Kibana (SIEM & dashboards)

Kali Linux
- Used to generate intentional malicious/suspicious traffic (e.g., Nmap scans)

Linux Target Machine
- Acts as the victim host for attack simulation

VirtualBox
- Used to create and manage an isolated virtual lab environment

-----

What This Lab Demonstrates:

- Ability to design and document a monitoring-focused network
- Understanding of IDS principles and network analysis
- Practical experience with Security Onion’s detection pipeline
- Ability to simulate attacks and interpret SOC-style alerts
- Skills in troubleshooting, documentation, and analysis
