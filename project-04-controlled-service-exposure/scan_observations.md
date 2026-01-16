# Scan Observations – Project 04

## Overview
This document contains technical observations derived from network scanning and firewall behavior analysis performed during Project 04. All activities were conducted over an encrypted VPN network.

---

## Baseline Observations
- The Windows host was reachable over the encrypted VPN.
- Initial Nmap scans returned filtered or no-response results.
- No TCP ports were exposed prior to firewall changes.
- Host-based firewall controls were enforced despite trusted connectivity.

---

## Firewall Change Impact
After creating a scoped firewall rule allowing ICMP Echo Requests from the VPN network, the following changes were observed:

- ICMP traffic was successfully permitted from the remote system.
- Host discovery via Nmap improved after ICMP was allowed.
- No TCP services became exposed as a result of the change.
- Firewall enforcement remained active for all other protocols.

---

## Security Interpretation
The observed behavior confirms that firewall rules are evaluated independently of encrypted network trust. Even when connectivity is established through a VPN, explicit firewall permissions are required to allow inbound traffic.

By scoping the rule to the VPN subnet, exposure was limited exclusively to trusted remote peers. This approach minimizes risk while enabling controlled functionality.

---

## Defensive Assessment
From a blue team perspective, the change represents a low-risk, well-scoped configuration adjustment. The ability to both apply and revert the rule demonstrates effective change management and adherence to least privilege principles.

---

## Conclusion
Project 04 reinforces the importance of firewall scoping and controlled exposure in remote collaboration environments. Understanding how encrypted connectivity interacts with host-based firewalls is essential for defensive security roles, particularly in SOC and system hardening contexts.

