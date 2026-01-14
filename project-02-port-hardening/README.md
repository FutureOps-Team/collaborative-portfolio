# Project 02 – SMB Port Hardening Laboratory on Windows Host

## Objective

The objective of this laboratory is to assess and strengthen the security posture of a Windows operating system by identifying and hardening commonly exploited network services and ports. The analysis, hardening, and validation were performed from a Kali Linux virtual machine connected through VirtualBox, simulating an external attacker perspective.

## Laboratory Environment

The laboratory environment consisted of a Windows 11 Pro host system and a Kali Linux virtual machine running on VirtualBox. Initial scans were performed using NAT networking, followed by validation scans using a Bridged Adapter to avoid virtualization-related false positives. The primary tools used were Nmap for network scanning, PowerShell for system configuration, and Windows Defender Firewall for traffic filtering.

## Initial Port Scanning

An initial SYN scan was executed from the Kali Linux virtual machine using Nmap.

Command executed:

    nmap -sS 192.168.X.X

The scan identified several open ports commonly associated with Windows networking services:

- TCP 135 (Microsoft RPC)
- TCP 139 (NetBIOS Session Service)
- TCP 445 (SMB / Microsoft-DS)
- TCP 5357 (HTTPAPI – Web Services on Devices)

These services are part of the Windows networking stack and are frequently targeted by attackers. Vulnerabilities and attack techniques such as EternalBlue, Pass-the-Hash, and ransomware campaigns like WannaCry rely heavily on misconfigured or exposed SMB and RPC services.

## SMB Version Verification

To verify which SMB protocol versions were supported by the host system, an Nmap SMB enumeration script was executed.

Command executed:

    nmap --script smb-protocols -p445 192.168.X.X

The results showed support for SMB 2.x and SMB 3.x, while SMBv1 was disabled. This confirmed that the most insecure and deprecated SMB version was not enabled on the system.

## Windows Host Hardening

All hardening actions were executed on the Windows host using PowerShell with administrative privileges.

First, SMBv1 was explicitly disabled to eliminate legacy protocol exposure.

Command executed:

    Disable-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol"

Next, a Windows Defender Firewall rule was created to block inbound SMB traffic on TCP port 445 across all profiles and network interfaces.

Commands executed:

    New-NetFirewallRule -DisplayName "Block SMB TCP 445" -Direction Inbound -LocalPort 445 -Protocol TCP -Action Block
    Set-NetFirewallRule -DisplayName "Block SMB TCP 445" -Profile Any -InterfaceType Any

Network discovery and file sharing were disabled through the Advanced Sharing Settings in the Network and Sharing Center to further reduce the attack surface.

Finally, the SMB server service (LanmanServer), responsible for file and printer sharing, was stopped and permanently disabled.

Commands executed:

    Stop-Service LanmanServer
    Set-Service LanmanServer -StartupType Disabled

## Verification of Hardening Measures

Firewall configuration was validated using PowerShell to ensure the blocking rule was active and correctly applied.

Command executed:

    Get-NetFirewallRule -DisplayName "Block SMB TCP 445" | Format-List DisplayName, Enabled, Profile, Direction, Action

The expected result confirmed that the rule was enabled, inbound, applied to all profiles, and blocking traffic.

The SMB server service status was also verified.

Command executed:

    Get-Service LanmanServer

The service was confirmed to be stopped and disabled.

## Post-Hardening Validation Scan

After switching the Kali Linux virtual machine network adapter to Bridged mode, a targeted Nmap scan was performed to validate the effectiveness of the applied hardening measures.

Command executed:

    nmap -p135,139,445,5357 192.168.X.X

Final scan results:

- TCP 135 marked as filtered
- TCP 139 marked as filtered
- TCP 445 marked as filtered
- TCP 5357 marked as closed

These results indicate that the Windows host no longer responds to unsolicited inbound connections on critical RPC and SMB-related ports.

## Findings and Lessons Learned

This laboratory demonstrated that ports 135, 139, 445, and 5357 represent a high-risk attack surface when exposed to a network. It also highlighted that VirtualBox NAT mode can generate misleading scan results, as responses may originate from the virtualization layer rather than the actual host operating system. Combining firewall rules with service hardening provides effective defense-in-depth, and understanding virtual network behavior is essential for accurate interpretation of reconnaissance scans.

## Contributors

Federico Arce Minuet – Windows security configuration, system hardening, and documentation  
Manuel Rossi Sanz – Network scanning, validation, and analysis from Kali Linux

## Final Result

SMB and RPC port hardening was successfully implemented and validated. External scans confirm that critical ports are filtered or closed, demonstrating the effectiveness of the applied firewall policies and service hardening measures.
