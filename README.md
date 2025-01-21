# SMB-Map-RMIT-Cybersecurity-Project
One of the Red teaming activities for my [RMIT Cybersecurity Project](https://github.com/Kazu010101/RMIT-Cybersecurity-Project/blob/main/README.md)

## Objective

- Weaponization: install smbclient on Kali Linux
- Discover shared SMB folders with smbclient or smbmap
- Exfiltrate any valuable assets found

## The Scenario

At this stage, the attacker has gained information from [nmap scan](https://github.com/Kazu010101/Nmap-Scan-RMIT-Cybersecurity-Project/blob/main/README.md) about the existance of SMB service in one of the hosts. Also, the [Zphisher phishing attack](https://github.com/Kazu010101/Zphisher-RMIT-Cybersecurity-Project) against one of the employees allowed the attacker to obtain the username and password for logging into Windows 10.

## Lab Setup

![nmap scan red team](https://github.com/user-attachments/assets/b2a83336-6849-4f89-84b0-6fa195d3352a)

*Figure 2: We still use the same setup from my [Nmap Scan Lab](https://github.com/Kazu010101/Nmap-Scan-RMIT-Cybersecurity-Project/blob/main/README.md), in which 2 Virtual Machines were used for the victim in Windows 10 (192.168.1.254 /24) and the attacker in Kali Linux (192.168.1.33 /24). Both network setting is internal mode.* 

## Weaponization: Install smbclient on Kali

Since the target has SMB service enabled, the appropriate 'weapon' to exploit this vulnerability is to instal smbclient on Kali with these commands:

- sudo apt-update
- sudo apt-upgrade
- sudo apt-get install smbclient cifs-utils

## SMB Folder Detection Method 1: smbclient -L

- smbclient -L //192.168.1.254 -U Client1
- Enter Client1 password

![SMB1](https://github.com/user-attachments/assets/8213e85d-27c8-4091-b025-d9139feec42b)

*Figure 2: By default, this command executes the connection from Kali using SMB1 which makes the connection attempt failed. However, the real purpose for this intentionally failed attempt is to detect shared folders name in Windows 10 VM.  In this case, there are two folders named "Office Shared Folder" and "SMB-Office-Folder". Folder's name is a crucial information for the attacker in order to carry out the next attack.* 

## SMB Folder Detection Method 2: SMBMAP -H 192.168.1.254 -u Client1 -p admin123

![smb6](https://github.com/user-attachments/assets/b7baf4e3-4346-41f4-b082-434feafb7545)

*Figure 3: Alternatively, smbmap tool can be used to enumerate and map SMB shares on a target server. It helps identify shared directories and their access permissions. Notice that both methods require target's credentials to be successful.* 

## Data Exfiltration

- smbclient "//192.168.1.254/Office Shared Folder" -U Client1 --option='client min protocol=SMB2'
- Enter Client1 password

![SMB3](https://github.com/user-attachments/assets/a8220500-8d8e-4db2-bbe4-b5f2028a375f)


*Figure 4: This command executes the connection using SMB2 into the folder named "Office Shared Folder". Notice that the connection is successful as shown by smb: \>. After typing smb: \> ls, a potential valuable data called Client's Data.txt is found.* 

![SMB4](https://github.com/user-attachments/assets/e8aad418-a416-4756-8544-1d47ce957e26)

*Figure 5: Now the attacker could attempt to exfiltrate the data from Windows 10 to Kali successfully.*

## Verification of Data Exfiltration

Exit the smb session, and run ls and cat command on Kali.

![SMB5](https://github.com/user-attachments/assets/cd4c9fab-0ea3-451a-a375-4ed21e950d62)

*Figure 6: Successful exfiltration of "Client's Data.txt" is verified because the file is located on Kali. Also, the attacker compromised the confidentiality of information contained as shown by successful cat command.*

## Result

Smbmap and smbclient were successfully utilized to identify and exfiltrate sensitive data from a Windows 10 machine to a Kali Linux system. Using smbmap, the shared directories on the target machine were enumerated, revealing accessible folders. With smbclient, files were navigated and extracted, demonstrating the ability to transfer sensitive data, such as "Client's Data.txt," from the target to the Kali system. This exercise highlights the importance of securing SMB shares and understanding potential vulnerabilities.

## Mitigation Plan

- Restrict SMB Share Access to ensure that only authorized users have access to sensitive SMB shares.
- Conduct cybersecurity awareness training on identifying phishing attacks and social engineering tactics to reduce the risk of stolen credentials.
- Implement Network Segmentation to isolate SMB services to a secure internal network, preventing exposure to unauthorized devices originating from malicious guests
