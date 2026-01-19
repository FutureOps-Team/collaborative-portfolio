# Project 6 – Windows SMB Authentication Detection over VPN

## Project Type
Blue Team / SOC Foundations

## Difficulty Level
Intermediate – Junior SOC Tier 1

## Project Objective
The objective of this project is to analyze and document Windows SMB authentication attempts over a VPN network, focusing on the generation and analysis of security events produced by failed network logons.

This project simulates a realistic internal attack or lateral movement scenario and demonstrates how Windows logs authentication failures when SMB services are accessed over a restricted network.

---

## Lab Environment
- Attacker Machine: Kali Linux
- Target Machine: Windows 10 / Windows 11
- Network Connectivity: Encrypted VPN network
- Firewall: Windows Defender Firewall (enabled)
- Service Analyzed: SMB (TCP 445)

---

## Scope and Assumptions
- VPN connectivity between systems is assumed to be operational
- VPN deployment and tunnel validation are out of scope
- The project focuses on detection, not exploitation
- All actions are performed in a controlled lab environment

---

## Step 1 – Baseline Scan
A baseline scan is executed from the Kali Linux machine to identify the initial exposure state of the Windows host.

Command executed:  nmap <VPN_WINDOWS_IP>


Observed result:
- Host responds
- All 1000 common ports are filtered
- No services are exposed

This confirms a hardened baseline state.

---

## Step 2 – Verify SMB Service on Windows
The SMB Server service is verified on the Windows system.

Command executed in PowerShell:  Get-Service LanmanServer


Observed result:
- Service status: Running

This confirms that the SMB service is active and capable of processing network authentication requests.

---

## Step 3 – Targeted SMB Port Scan
A targeted scan is executed from Kali to assess SMB port visibility.

Command executed:  nmap -p 445 <VPN_WINDOWS_IP>


Observed result:
- 445/tcp filtered (microsoft-ds)

This indicates that the service exists but is restricted by the firewall.

---

## Step 4 – SMB Enumeration Attempt (No Credentials)
An unauthenticated SMB enumeration attempt is performed.

Command executed:  smbclient -L //<VPN_WINDOWS_IP> -N


Observed result:
- NT_STATUS_IO_TIMEOUT

This confirms that the host receives SMB traffic but does not allow unauthenticated access.

---

## Step 5 – SMB Authentication Attempt (Firewall Restricted)
An explicit authentication attempt is performed using invalid credentials.

Command executed:
smbclient //<VPN_WINDOWS_IP>/IPC$ -U fakeuser


Observed result:
- NT_STATUS_IO_TIMEOUT

At this stage, the firewall blocks authentication before credential processing.

---

## Step 6 – Firewall Adjustment (VPN Scoped)
A firewall rule is created to allow SMB access only from the VPN subnet.

Command executed in PowerShell:  New-NetFirewallRule
-DisplayName "Allow SMB from VPN"
-Direction Inbound 
-Protocol TCP
-LocalPort 445 
-Action Allow
-RemoteAddress <VPN_SUBNET>



This allows controlled authentication attempts while maintaining restricted exposure.

---

## Step 7 – SMB Authentication Attempt (Failed Login)
The authentication attempt is repeated from Kali using invalid credentials.

Command executed:  smbclient //<VPN_WINDOWS_IP>/IPC$ -U fakeuser


Observed result:
- NT_STATUS_LOGON_FAILURE

This confirms that the authentication request reached the Windows authentication layer.

---

## Step 8 – Windows Event Log Analysis
Windows Event Viewer is reviewed to analyze generated security events.

Logs reviewed:
- Windows Logs → Security

Observed events:
- Event ID 4625 (Failed Logon)

Event details:
- Logon Type: 3 (Network)
- Account Name: fakeuser
- Source Network Address: Kali VPN IP
- Failure Reason: Authentication failure

No Event ID 4776 was generated, which is consistent with certain SMB authentication flows.

---

## Step 9 – Cleanup and Hardening
The temporary firewall rule is removed to restore the hardened state.

Command executed:  Remove-NetFirewallRule -DisplayName "Allow SMB from VPN"


A final state confirms SMB is no longer accessible for authentication attempts.

---

## Lessons Learned
- SMB authentication attempts reliably generate security logs
- Event ID 4625 provides critical SOC visibility
- Logon Type 3 indicates network-based authentication
- Firewall scoping is essential for controlled exposure
- Absence of certain events (e.g., 4776) does not invalidate detection

---

## Skills Demonstrated
- Windows authentication analysis
- SMB and NTLM behavior understanding
- Firewall rule management
- VPN-restricted access control
- SOC event investigation
- Professional security documentation

---

## Conclusion
This project demonstrates a realistic SOC scenario involving SMB authentication attempts over a VPN network. The generation and analysis of Windows security events highlight how failed network logons are detected and investigated in real-world environments.
