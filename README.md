# SMB-Map-RMIT-Cybersecurity-Project
One of the Red teaming activities for my [RMIT Cybersecurity Project](https://github.com/Kazu010101/RMIT-Cybersecurity-Project/blob/main/README.md)

## Objective

- Weaponization: Install SMB MAP on Kali Linux
- Find asset
- Exfiltrate the asset

## The Scenario


*Figure 1: In this follow-up scenario, the attacker has successfully scan the network using nmap and found several host with open TCP ports.*

## Lab Setup

- Windows 10 VM: 192.168.1.254 /24 | 
![nmap scan red team](https://github.com/user-attachments/assets/b2a83336-6849-4f89-84b0-6fa195d3352a)

*Figure 2: We still use the same setup from my [Nmap Scan Lab](https://github.com/Kazu010101/Nmap-Scan-RMIT-Cybersecurity-Project/blob/main/README.md), in which 2 Virtual Machines were used for the victim in Windows 10 (192.168.1.254 /24) and the attacker in Kali Linux (192.168.1.33 /24).* 

## DoS Attack using Ettercap

Once verified that both machines could ping each other, the following Dos Attack is conducted by following these steps:
sudo apt-get update
sudo apt-get install smbclient cifs-utils

## Result

The Wireshark pcap log from this lab verifies the successful DoS attack. The repeated TCP Packets with of RST, ACK flags across multiple packets, especially in a short time span, suggests that the victim (192.168.1.254) is being overwhelmed with connection attempts or resets by the attacker (192.168.1.33).

## Mitigation Plan

- The Office Network needs to be subnetted into at least two subnets, each seperately for the internal staff and the guest network.
- Additional security tools such as IPS and firewalls are required to protect internal staff network from malicious traffic originiating from outside and guest network.
- Turn on the Windows Firewall on Windows 10.
