
# Findings & Learnings

This lab was my attempt on building a small monitoring environment with Security Onion, Kali, and a Linux target machine. The goal was to generate some real traffic, watch how Security Onion reacts, and get a feel for how network-based detection actually works in practice. Here are the main things I learned along the way.

---

## What I Learned About the Network Setup

One of the biggest lessons was that the network layout matters a lot more than I expected. Security Onion will only alert on traffic that actually reaches its sniffing interface. If the traffic goes through NAT or the wrong adapter, nothing appears.

I also learned why it’s important to keep management and monitoring separate. The bridged adapter is great for accessing the web interface, but it should never be used for monitoring. The host-only network ended up being the perfect place to run the attacker and victim machines, because everything stays contained but still visible to Security Onion.

Another interesting point: my sniffing interface does have an IP (192.168.56.101), but it has no gateway, so it’s basically isolated. Suricata doesn’t care whether the interface has an IP or not — it just listens to packets directly. Having the IP actually made troubleshooting easier, because I could confirm the interface was up without breaking anything.

---

## Interesting points about Security Onion Alert dashboard

This is how the alert dashboard looks like when the ET Open rules fire off due to a nmap scan:
<img width="1478" height="713" alt="so-alerts" src="https://github.com/user-attachments/assets/84b3aa6c-bc94-41e5-a755-0ac6a07f29d6" />
<img width="1914" height="1022" alt="so-alert-details" src="https://github.com/user-attachments/assets/5b73c535-4556-4220-8465-e4ac6d7f9129" />
<img width="1907" height="1022" alt="so-alert-details-2" src="https://github.com/user-attachments/assets/dff7518d-9f44-4bfe-aead-ca889cd06818" />



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
