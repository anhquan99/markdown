# Issue Detection
## Attack life cycle
![[image-17.png]]
- During the reconnaissance phase the attacker will try to understand your environment as much as possible. The more information they can collect, the more they can determine a way to exploit your environment, looking for a vulnerability. This phase may range from a simple port-scan to buying drinks for IT staff and starting conversations about difficulties at work; anything to get more information about the environment. This phase could last years and can be difficult to detect.
- With an idea of what exists, from hardware, to software, to personnel, the initial compromise phase begins by exploiting some weakness. This could be a zero-day exploit, or an ignored long-term vulnerability. This phase could last months, but if the reconnaissance phase went well, the attack should be focused and guaranteed to be quickly successful. If the attack does not work, the attacker will return to the reconnaissance phase. This phase is often the first time it’s possible to detect the attack.
- Once a system has been compromised, the next phase is to take over control, and to create ways to continue control even if the intrusion is detected and the original compromise vector is closed.
- The lateral movement phase can be difficult to detect, as the attack takes over more and more systems. Done well, these steps may look like common administrative tasks, such as updating software or modifying configurations.
- Once the valuable data has been located, the infrastructure has been documented, and multiple forms of compromise have been inserted, the target attainment phase completes. Remember that some compromises may be time-delayed, so that initial scans miss the eventual opening.
- The final phase of exfiltration, corruption or disruption begins when the attacker disrupts or steals data.
## Kill chain
![[image-18.png]]
- Kill chain, or cyber kill chain, is the attack progression. This refers to the steps taken by a malicious operator in order to compromise a target. Interrupting or preventing each step is an important part of cybersecurity.
### Reconnaissance
- Research, identification and selection of targets, often represented as crawling Internet websites, such as conference proceedings and mailing lists, for email addresses, social relationships, or information on specific technologies.
### Weaponization
- Coupling a remote access trojan with an exploit into a deliverable payload, typically by means of an automated tool (weaponizer). Increasingly, client application data files such as Adobe Portable Document Format (PDF) or Microsoft Office documents serve as the weaponized deliverable.
### Delivery
- Transmission of the weapon to the targeted environment.
### Exploitation
- After the weapon is delivered to the victim host, exploitation triggers the intruders’ code. Most often, exploitation targets an application or operating system vulnerability, but it could also more simply exploit the users themselves or leverage an operating system feature that auto-executes code.
### Installation
- Installation of a remote access trojan or backdoor on the victim system allows the adversary to maintain persistence inside the environment.
### Command and Control (C2)
- Typically, compromised hosts must beacon outbound to an Internet controller server to establish a C2 channel. Once the C2 channel is established, intruders have hands on the keyboard access inside the target environment.
### Actions on Objectives
- Only now, after progressing through the first six phases, can intruders take actions to achieve their original objectives. Typically, this objective is data exfiltration which involves collecting, encrypting and extracting information from the victim environment; violations of data integrity or availability are potential objectives as well. Alternatively, the intruders may only desire access to the initial victim box for use as a hop point to compromise additional systems and move laterally inside the network.
## Host Intrusion Detection Systems (HIDS)
- Host Intrusion Detection Systems typically are installed or scan for anomalies on the local node. An agent or cron job generally runs on the node to check the current system resources against a known database of valid attributes for each resource. Attributes such as MD5 sums, time-stamps, permissions, etc., are checked for anomalies. This requires a known good state to be recorded and kept in more than once place to avoid an attacker updating the attributes after changing important files.
### Tools
- AIDE (Advanced Instruction Detection Environment)
- Tripwire
- OSSEC
## Network Intrusion Detection System (NIDS)
- Network Intrusion Detection Systems are tools that generally collect traffic from networking devices, typically routers, switches, and firewalls, and then analyze that traffic for attack signatures and other anomalies.
### Tools
- SNORT
- Suricata
## Physical Intrusion Detection System (PIDS)
 - Physical Intrusion Detection Systems have more to do with systems that ensure only authorized users are able to access the systems and environment. Consult with a physical security specialist when building the data center and workstation environment that has access to the data center.