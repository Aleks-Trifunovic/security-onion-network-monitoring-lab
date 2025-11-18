
# Findings & Learnings

This lab was my attempt on building a small monitoring environment with Security Onion, Kali, and a Linux target machine. The goal was to generate some real traffic, watch how Security Onion reacts, and get a feel for how network-based detection actually works in practice. Here are the main things I learned along the way.

---

## What I Learned About the Network Setup

One of the biggest lessons was that the network layout matters a lot more than I expected. Security Onion will only alert on traffic that actually reaches its sniffing interface. If the traffic goes through NAT or the wrong adapter, nothing appears.

I also learned why it’s important to keep management and monitoring separate. The bridged adapter is great for accessing the web interface, but it should never be used for monitoring. The host-only network ended up being the perfect place to run the attacker and victim machines, because everything stays contained but still visible to Security Onion.

Another interesting point: my sniffing interface does have an IP (192.168.56.101), but it has no gateway, so it’s basically isolated. Suricata doesn’t care whether the interface has an IP or not — it just listens to packets directly. Having the IP actually made troubleshooting easier, because I could confirm the interface was up without breaking anything.

---

## A few interesting points about Security Onion Alert dashboard

<img width="1169" height="603" alt="so-alert-guided-4" src="https://github.com/user-attachments/assets/4f967ef0-0f64-47bb-859e-d2aebfddb5f5" />
<img width="1170" height="468" alt="so-alert-guided-3" src="https://github.com/user-attachments/assets/1ed54551-de49-494b-a244-5d348e541c97" />
<img width="1337" height="424" alt="so-alert-guided-5" src="https://github.com/user-attachments/assets/b2db6ea0-0f9b-4c21-b189-fe624232b874" />
<img width="1920" height="1080" alt="so-alert-guided" src="https://github.com/user-attachments/assets/83c45254-8101-49e8-af4d-2ada44794314" />
<img width="1478" height="713" alt="so-alerts" src="https://github.com/user-attachments/assets/0466c87f-89a7-4476-826b-1f3609407f5c" />
<img width="1170" height="564" alt="so-alert-guided-2" src="https://github.com/user-attachments/assets/8c4aeaef-8bd8-4f9a-a896-0c2050eb2c63" />
<img width="1920" height="1080" alt="so-alert-details-2" src="https://github.com/user-attachments/assets/ab49c282-d999-42b0-a956-23018022102c" />
<img width="1920" height="1080" alt="so-alert-details" src="https://github.com/user-attachments/assets/8e781d42-69f7-4cd7-84fe-6698ce7a8361" />


---

## What Worked Well

Once everything was wired correctly, the alerts came in exactly as expected. A simple Nmap scan from Kali triggered multiple Suricata signatures. Seeing the alerts show up in the Security Onion dashboards helped me understand the whole detection pipeline much better.

The lab stayed stable, and the isolation from the rest of my network made me comfortable, it was smooth sailing from there on.

---

## Challenges I Ran Into

At first I couldn’t get any alerts at all, which was frustrating. The main issue turned out to be how the NICs were configured. I had to rethink which adapter should be sniffing the traffic and make sure promiscuous mode was set up the right way. I also realized that NAT traffic will never be monitored, so everything needed to stay on the host-only network if I wanted Security Onion to see it.

Enabling the ET Open ruleset also helped seeing the alerts consistently. Once the rules were active, though, things started behaving the way they were supposed to.

---

## Skills I Gained

- Understanding how Security Onion, Suricata, and the Elastic interface fit together. 
- Seeing firsthand how network traffic turns into alerts.
- Getting better at VirtualBox networking and how different adapters behave.  
- Troubleshooting why alerts weren’t firing.
- Reading Suricata signatures and understanding what they match.
- Building a small SOC-style environment from scratch.

---

## What I Want to Improve Next

There are a few things I want to add as the next step:

- Try out Zeek logs and compare them to Suricata.
- Add a Windows machine and start collecting endpoint logs.
- Test things like brute-force attempts, HTTP scanning, and maybe even some simple malware samples.
- Write some custom Suricata rules, just to understand how they work.

---

## Final Thoughts

Overall, this lab helped me understand how a monitoring setup works in real life, not just in theory. It gave me a clearer picture of how traffic flows, why alerts happen, and what can go wrong when the network isn’t configured the right way. It was a good hands-on introduction to SOC.
