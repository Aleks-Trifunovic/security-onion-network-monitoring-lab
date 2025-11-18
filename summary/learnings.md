
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

You can immediately see useful details like the source and destination IP addresses, the destination port, and the name of the rule that fired. It also gives you the timestamp, severity level, and a short description of what Suricata thinks is happening. All of this helps you quickly understand what kind of activity took place. It’s a simple scan, but it’s a good example of how Security Onion turns network traffic into something you can actually interpret and act on.

When you open one of the alerts and look at the detailed view, you’ll also notice a section called “guided analysis.” This is a newer, experimental feature in Security Onion that seems to use some AI-assisted logic to help explain what the alert might mean. It’s a helpful extra layer when you’re still learning how to read alerts or when you want a second opinion on the potential threat.
This is how it looks like:

<img width="1910" height="1018" alt="so-alert-guided" src="https://github.com/user-attachments/assets/a6fd445c-b28b-4c9c-96ad-f911b72aefbc" />
<img width="1170" height="564" alt="so-alert-guided-2" src="https://github.com/user-attachments/assets/0c653c17-c584-4420-80a2-4c1a419cb42a" />
<img width="1170" height="468" alt="so-alert-guided-3" src="https://github.com/user-attachments/assets/3c0f5170-5426-4a74-900b-6a24f4d9d0cd" />
<img width="1169" height="603" alt="so-alert-guided-4" src="https://github.com/user-attachments/assets/a84ecb1f-fbad-4155-ac61-a87ba5c84442" />
<img width="1337" height="424" alt="so-alert-guided-5" src="https://github.com/user-attachments/assets/4ebec831-8eef-495d-918d-af241beb2119" />

Overall, the alerts dashboard and the guided analysis feature complement each other well, making it easier to quickly understand what’s happening on the network and decide how to move forward.

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
