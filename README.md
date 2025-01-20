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

## Weaponization: Install smbclient on Kali

Once verified that both machines could ping each other, the following Dos Attack is conducted by following these steps:
- sudo apt-get update
- sudo apt-upgrade
- sudo apt-get install smbclient cifs-utils

##Attack

- smbclient -L //192.168.1.254 -U Client1
- Enter Client1 password
![SMB1](https://github.com/user-attachments/assets/8213e85d-27c8-4091-b025-d9139feec42b)
*Figure 2: By default, this command executes the connection using SMB1 which is unable to connect. However, the real purpose for this intentionally failed attempt is to detect shared folders name in Windows 10 VM.  In this case, there are two folders named "Office Shared Folder" and "SMB-Office-Folder". Folder's name is a crucial information for the attacker in order to carry out the next attack.* 


## Result

The Wireshark pcap log from this lab verifies the successful DoS attack. The repeated TCP Packets with of RST, ACK flags across multiple packets, especially in a short time span, suggests that the victim (192.168.1.254) is being overwhelmed with connection attempts or resets by the attacker (192.168.1.33).

## Mitigation Plan

- The Office Network needs to be subnetted into at least two subnets, each seperately for the internal staff and the guest network.
- Additional security tools such as IPS and firewalls are required to protect internal staff network from malicious traffic originiating from outside and guest network.
- Turn on the Windows Firewall on Windows 10.
