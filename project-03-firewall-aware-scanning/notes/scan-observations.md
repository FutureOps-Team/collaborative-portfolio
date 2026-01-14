# Scan Observations – Project 03

## Overview
This document contains technical observations and analysis derived from the network discovery and port scanning activities performed during Project 03. All scans were conducted against a Windows system with Windows Defender Firewall enabled.

## Observed Behavior
- The target host was successfully identified during network discovery across the /24 subnet.
- All TCP-based port scans resulted in filtered or no-response states.
- No open TCP ports were identified.
- ACK scan results indicated the presence of packet filtering.

## Firewall Impact Analysis
The scan results indicate that Windows Defender Firewall is actively blocking unsolicited inbound traffic. SYN packets sent during TCP-based scans were dropped, preventing service enumeration. This behavior remained consistent across multiple scan techniques.

ACK scan behavior confirmed the presence of a stateful host-based firewall, as packets not associated with an established connection were filtered.

## Security Interpretation
The absence of open ports does not represent a vulnerability or misconfiguration. Instead, it reflects a hardened host configuration designed to minimize exposure to network-based attacks.

## Professional Assessment
From a defensive security perspective, the observed behavior represents an expected and correct security posture for a Windows workstation operating within a private network. This configuration aligns with standard host hardening principles.

## Conclusion
Understanding why ports are filtered is as important as identifying open services. This project demonstrates firewall-aware reconnaissance and professional interpretation of Nmap scan results within a controlled laboratory environment.

