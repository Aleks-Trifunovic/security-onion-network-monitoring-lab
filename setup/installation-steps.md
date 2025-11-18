
## This document covers how I installed and configured the lab environment, as well as the issues I ran into and how I fixed them. The goal is to reflect the real process I went through rather than just giving a step-by-step guide.


### Kali Linux Setup

1. Downloaded the Kali ISO.
2. Created a VM in VirtualBox and assigned:
   - 2 CPU cores.  
   - 8 GB RAM.
   - 60 GB disk.
3. Added two network adapters:
   - Adapter 1 → NAT.
   - Adapter 2 → Host-only (vboxnet0).
4. Installed Kali using the graphical installer.
5. Updated the system: sudo apt update && sudo apt full-upgrade -y.
6. Confirmed tools like Nmap were installed (Kali usually has them by default).

No issues here — Kali worked as expected.

### Target Machine Setup (Linux)

1. Setup a small linux VM.
2. Created a VM in VirtualBox and assigned:
   - 2 CPU cores.  
   - 4 GB RAM.
   - 10 GB disk.
3. Added two network adapters:
   - Adapter 1 → NAT.
   - Adapter 2 → Host-only (vboxnet0).
4. Downloaded and installed Ubuntu.

This machine is used as the “victim” for Nmap scans and other test traffic.

### Security Onion

1. Downloaded the Security Onion ISO.
2. Created a VM in VirtualBox and assigned:
   - 4 CPU cores.  
   - 16 GB RAM.
   - 203 GB disk.
3. Added two network adapters:
   - Adapter 1 → Adapter 1 → Bridged (management).
   - Adapter 2 → Host-only (sniffing).
4. Installed Security Onion in Evaluation/Sandbox mode.
5. Picked:
   - Bridged NIC as the management interface.
   - Host-only NIC as the monitoring interface.
  
6. Finished the setup wizard and rebooted.
7. Accessed the Security Onion UI from the host browser using the management IP.

During the setup I discovered that the sniffing NIC ended up with an IP 192.168.56.101. At first this seemed wrong because sniffing interfaces often have no IP.
  - But it turned out to be completely fine:
  - It has no gateway, so it can’t route traffic anywhere.
  - It only lives inside the isolated host-only network.
  - Suricata ignores the interface’s IP anyway.
  - It makes troubleshooting easier because I can confirm the adapter is up.

