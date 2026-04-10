
# Findings & Learnings:

This lab was my attempt on building a small monitoring environment with Security Onion, Kali, and a Linux target machine. The goal was to generate some real traffic, watch how Security Onion reacts, and get a feel for how network-based detection actually works in practice. Here are the main things I learned along the way.

---

## Network Setup:

Well the network layout was the thing that surprised me most. I assumed getting Security Onion installed was the hard part. The hard part was understanding that it will only alert on traffic that actually reaches its sniffing interface. If the traffic goes through NAT or the wrong adapter, you get nothing.

The other thing that clicked for me was why you'd want to keep management and monitoring on separate interfaces. The bridged adapter is convenient for reaching the web UI, but it has no business being your monitoring interface. I ended up putting the attacker and victim machines on a host-only network, which turned out to be exactly right. Everything stays contained, but Security Onion can still see it.

One thing that tripped me up early: my sniffing interface has an IP (192.168.56.101) but no gateway, so it's basically isolated. I wasn't sure if that would cause problems with Suricata, but it doesn't care whether the interface has an IP or not. It just sniffs packets directly. The IP actually made troubleshooting easier, since I could at least confirm the interface was up and reachable.

---

## The Frustrating Parts:

At first I couldn't get any alerts at all, which was genuinely annoying. I kept thinking something was broken with Security Onion itself. Turned out the issue was just which NIC was doing the sniffing and that I had the wrong adapter set up, and promiscuous mode wasn't configured properly either.
Also: NAT traffic will never be monitored. That was a key thing I had to learn the hard way. Once I moved everything to the host-only network and got promiscuous mode right, things started behaving ok.
Getting the ET Open ruleset enabled also made a big difference. Before that, even with everything else correct, alerts were inconsistent. After enabling it, they fired reliably.

---

## What Worked:

Once the networking was sorted out, the alerts came in exactly like they should. I ran a basic Nmap scan from Kali and multiple Suricata signatures fired. Watching those alerts show up in the dashboards made the whole detection pipeline feel pretty real.
The lab stayed stable and it was smooth sailing from there on.

---


## The Alert Dashboard:

Here's what the alert dashboard looks like when the ET Open rules trigger from an Nmap scan:

<img width="1478" height="713" alt="so-alerts" src="https://github.com/user-attachments/assets/84b3aa6c-bc94-41e5-a755-0ac6a07f29d6" />
<img width="1914" height="1022" alt="so-alert-details" src="https://github.com/user-attachments/assets/5b73c535-4556-4220-8465-e4ac6d7f9129" />
<img width="1907" height="1022" alt="so-alert-details-2" src="https://github.com/user-attachments/assets/dff7518d-9f44-4bfe-aead-ca889cd06818" />

Right away you can see the source and destination IPs, the destination port, the rule that fired, the timestamp, severity, and a short description of what Suricata thinks is going on. For something as simple as a port scan, it's a good example of how Security Onion takes raw traffic and turns it into something you can actually act on.

When you open an alert and look at the detailed view, there's also a section called "guided analysis." It's described as experimental, and it seems to use some kind of AI-assisted logic to help explain what an alert might mean. I found it useful as a gut check, especially when I wasn't totally sure how to interpret a rule name.
This is how it looks like:

<img width="1910" height="1018" alt="so-alert-guided" src="https://github.com/user-attachments/assets/a6fd445c-b28b-4c9c-96ad-f911b72aefbc" />
<img width="1170" height="564" alt="so-alert-guided-2" src="https://github.com/user-attachments/assets/0c653c17-c584-4420-80a2-4c1a419cb42a" />
<img width="1170" height="468" alt="so-alert-guided-3" src="https://github.com/user-attachments/assets/3c0f5170-5426-4a74-900b-6a24f4d9d0cd" />
<img width="1169" height="603" alt="so-alert-guided-4" src="https://github.com/user-attachments/assets/a84ecb1f-fbad-4155-ac61-a87ba5c84442" />
<img width="1337" height="424" alt="so-alert-guided-5" src="https://github.com/user-attachments/assets/4ebec831-8eef-495d-918d-af241beb2119" />

The alert dashboard and guided analysis feel like they complement each other well. One gives you the raw data, the other helps you think through what it might mean.

---

## What I Actually Learned:

- How Security Onion, Suricata, and the Elastic interface fit together as a stack. 
- What it looks like when network traffic actually becomes an alert.
- GVirtualBox networking. Specifically how different adapter types behave.
- How to troubleshoot when alerts just aren't firing (and not to question my life choices when i have to reinstall everything).
- Reading Suricata signatures well enough to understand what they mean.
- Building a small SOC-style environment from scratch.

---

## What I Want to Do Next:

There are a few things I want to add as the next step:

- Look into Zeek logs and compare them to what Suricata picks up
- Add a Windows machine and start pulling in endpoint logs.
- Try bruteforce attempts, HTTP scanning, maybe some simple malware samples.
- Write some custom Suricata rules just to get a feel for how rule syntax works.

---

## Overall Takeaway:

The biggest thing this lab gave me was a more grounded understanding of how monitoring works in practice, how traffic flows, why alerts fire (or don't) and what breaks when the network isn't set up right.
