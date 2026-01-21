# Project 7 – WinRM Authentication Attempt Detection over VPN

## Project Type
Blue Team / SOC Operations

## Difficulty Level
Intermediate – Junior SOC Tier 1 / Tier 2

## Project Objective
The objective of this project is to analyze and document Windows authentication-related security events generated during remote administration attempts using WinRM (Windows Remote Management) over a VPN network.

The project focuses on detecting privileged authentication activity rather than successful access, simulating a realistic internal attack or lateral movement scenario.

---

## Lab Environment
- Attacker Machine: Kali Linux
- Target Machine: Windows 10 / Windows 11
- Network Connectivity: Encrypted VPN network
- Firewall: Windows Defender Firewall (enabled)
- Service Analyzed: WinRM (TCP 5985 – HTTP)

---

## Scope and Assumptions
- VPN connectivity is assumed to be operational
- VPN tunnel establishment is out of scope
- The project focuses on detection and analysis
- All actions are performed in a controlled lab environment
- No successful authentication is performed

---

## Step 1 – Baseline Network Scan
A baseline scan is executed from Kali Linux to assess the initial exposure state of the Windows host.

Command executed:  nmap <VPN_WINDOWS_IP>

Observed result:
- Host reachable
- All common ports filtered
- No exposed services

This confirms a hardened baseline configuration.

---

## Step 2 – WinRM Service Verification
The WinRM service state is verified on the Windows host.

Command executed in PowerShell:  Get-Service WinRM

Observed result:
- Service initially stopped

---

## Step 3 – WinRM Service Enablement
WinRM is enabled in a controlled manner.

Command executed:  winrm quickconfig

Observed result:
- WinRM service started
- Listener configured on TCP 5985
- Default firewall rules applied

---

## Step 4 – Targeted WinRM Port Scan
A targeted scan is performed from Kali to verify WinRM port behavior.

Command executed:  nmap -p 5985 <VPN_WINDOWS_IP>

Observed result:
- 5985/tcp filtered (wsman)

This indicates that WinRM is active but restricted by the firewall.

---

## Step 5 – Initial WinRM Authentication Attempt
A remote authentication attempt is performed from Kali without modifying firewall rules.

Command executed:  evil-winrm -i <VPN_WINDOWS_IP> -u fakeuser -p fakepassword

Observed behavior:
- Connection attempt hangs during initialization
- Authentication layer not reached

---

## Step 6 – Firewall Adjustment (VPN Scoped)
A firewall rule is created to allow WinRM traffic only from the VPN subnet.

Command executed in PowerShell:  New-NetFirewallRule
-DisplayName "Allow WinRM from VPN"
-Direction Inbound
-Protocol TCP
-LocalPort 5985 
-Action Allow
-RemoteAddress <VPN_SUBNET>


This allows controlled WinRM authentication attempts.

---

## Step 7 – WinRM Authentication Attempt (Failed)
The authentication attempt is repeated from Kali using invalid credentials.

Command executed:  evil-winrm -i <VPN_WINDOWS_IP> -u fakeuser -p fakepassword

Observed result:
- WinRM::WinRMAuthenticationError

This confirms that the authentication request reached the Windows authentication layer and was rejected.

---

## Step 8 – Windows Event Log Analysis
Windows Security logs are reviewed following the authentication attempt.

Observed event:
- Event ID 4672 – Special privileges assigned to new logon

Event characteristics:
- Generated during WinRM authentication processing
- Indicates evaluation of privileged access
- Associated with remote administrative activity

No Event ID 4625 was generated during this process.

---

## Step 9 – Cleanup and Hardening
The temporary firewall rule is removed to restore the hardened state.

Command executed:  Remove-NetFirewallRule -DisplayName "Allow WinRM from VPN"

WinRM returns to a restricted configuration.

---

## Lessons Learned
- WinRM authentication attempts may generate privileged log events even when authentication fails
- Event ID 4672 is highly relevant for detecting sensitive remote access activity
- Firewall scoping directly impacts authentication visibility
- Not all failed authentication attempts generate Event ID 4625

---

## Skills Demonstrated
- WinRM and PowerShell Remoting analysis
- Windows Security Event investigation
- Firewall rule management
- VPN-scoped access control
- SOC-level detection reasoning
- Accurate security documentation

---

## Conclusion
This project demonstrates how privileged authentication-related events can be detected and analyzed during WinRM access attempts over a VPN. The presence of Event ID 4672 provides valuable visibility into sensitive remote activity, even in the absence of traditional failed logon events.
