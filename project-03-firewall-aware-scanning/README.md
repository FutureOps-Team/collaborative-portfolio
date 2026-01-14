# Project 03 – Firewall-Aware Network Scanning and Analysis

## Overview
This project documents a complete network reconnaissance and port scanning exercise performed against a Windows system protected by an active host-based firewall. The goal is not to bypass security controls, but to demonstrate correct methodology, tool usage, and professional interpretation of filtered scan results in a defensive environment.

## Lab Environment
Attacker Machine: Kali Linux  
Target Machine: Windows (Windows Defender Firewall enabled)  
Virtualization Platform: VirtualBox  
Network Configuration: Host-Only Adapter  
Network Scope: /24 local subnet  

## Project Scope and Objectives
- Discover live hosts within a local network segment
- Identify a Windows host protected by a host-based firewall
- Perform multiple Nmap scan techniques against the target
- Observe and document firewall behavior
- Interpret filtered and no-response scan results accurately
- Produce professional-grade documentation suitable for a security portfolio

## Methodology

### Network Discovery
Network discovery was performed to identify active hosts within the local network range.

Command used:
nmap -sn 192.168.56.0/24

The Windows machine was identified as a live host within the scanned subnet.

### Target Identification
After identifying active hosts, the Windows system was selected as the target for further analysis. Its IPv4 address was recorded and used consistently throughout the project.

### Port Scanning – Firewall Active
All scans were executed while Windows Defender Firewall was enabled, without modifying default firewall rules.

#### Default TCP Scan
Command used:
nmap <TARGET_IP>

#### TCP SYN Scan
Command used:
nmap -sS -p 1-1000 <TARGET_IP>

#### No-Ping Scan
Command used:
nmap -Pn <TARGET_IP>

#### ACK Scan (Firewall Detection)
Command used:
nmap -sA <TARGET_IP>

## Tools Used
Nmap  
Kali Linux  
Windows Defender Firewall  

## Results
All TCP-based scans returned filtered or no-response results. No open ports were exposed by the target system. ACK scan results confirmed packet filtering behavior consistent with a correctly configured host-based firewall.

## Conclusion
This project demonstrates the importance of understanding defensive security mechanisms during reconnaissance activities. Correct interpretation of filtered scan results is essential for accurate security assessments and reflects real-world defensive security conditions.

## Contributors
Federico Arce
Manuel Rossi
