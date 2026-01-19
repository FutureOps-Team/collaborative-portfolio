# Scan Observations – Project 6

## Baseline Observations
The baseline scan confirmed that the Windows host was reachable but fully filtered, with no exposed services. This established a secure starting point for the analysis.

---

## SMB Port Behavior
Targeted scanning revealed that TCP port 445 was filtered but responsive, indicating the presence of the SMB service behind firewall controls.

---

## Authentication Attempt Analysis
Initial SMB authentication attempts resulted in timeouts, confirming that the firewall blocked access before credential processing.

After allowing SMB access exclusively from the VPN subnet, authentication attempts reached the Windows authentication layer and resulted in explicit logon failures.

---

## Log Analysis Findings
Windows Security logs recorded Event ID 4625 for failed authentication attempts. The events contained the following critical details:
- Network logon type (Type 3)
- Source IP address from the VPN network
- Username used during the authentication attempt
- Failure reason

No Event ID 4776 entries were generated, which is consistent with certain NTLM authentication behaviors and does not reduce detection value.

---

## Security Interpretation
The observed behavior demonstrates how SMB authentication attempts are logged when they reach the authentication layer. Firewall scoping plays a critical role in determining whether authentication attempts are processed and logged.

---

## Analyst Takeaway
This project highlights:
- Reliable detection of SMB authentication failures
- The importance of logon type analysis
- The impact of firewall configuration on visibility
- Realistic SOC investigation workflows

These observations reflect real-world enterprise security monitoring scenarios.

