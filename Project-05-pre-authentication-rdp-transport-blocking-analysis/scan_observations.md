# Scan Observations – Project 05

## Baseline Observations
The initial baseline scan confirmed that the Windows system had no exposed TCP services prior to configuration changes. This established a hardened starting point for the analysis.

---

## Service Exposure Observations
After enabling the RDP service and applying a VPN-scoped firewall rule, TCP port 3389 responded as open during targeted scanning. This confirmed that the service was reachable at the transport level through the VPN network.

---

## Connection Attempt Observations
All RDP connection attempts from the Kali Linux system failed during the handshake or transport negotiation phase. Client-side errors consistently reported transport-level failures.

No interactive session or authentication attempt was completed.

---

## Log Analysis Findings
Windows Event Viewer analysis revealed:
- No successful logon events (Event ID 4624)
- No failed logon events (Event ID 4625)
- No RDP authentication-related security events

This confirms that credential processing never occurred.

---

## Security Interpretation
The lack of authentication logs indicates that access attempts were blocked before reaching the authentication layer. This behavior aligns with real-world security controls that terminate connections during protocol negotiation or transport enforcement.

---

## Analyst Takeaway
This project highlights that:
- Detection depends on where a connection is blocked
- Pre-authentication controls may prevent log generation
- Network behavior must be correlated with host telemetry
- Absence of logs does not imply absence of security controls

Understanding these limitations is critical for SOC investigations.

