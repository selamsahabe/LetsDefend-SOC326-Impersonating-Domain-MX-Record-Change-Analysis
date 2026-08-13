# LetsDefend-SOC326-Impersonating-Domain-MX-Record-Change-Analysis
LetsDefend SOC326 - Impersonating Domain MX Record Change Detected
# LetsDefend: SOC326 - Impersonating Domain MX Record Change Detected

Hey everyone! In this write-up, I will walk you through my analysis of a Medium severity ThreatIntel alert on LetsDefend involving a typosquatted domain, malicious MX record changes, and an interactive phishing campaign that led to a host compromise.

---

## 🚨 Alert Overview
* **Rule Name:** SOC326 - Impersonating Domain MX Record Change Detected
* **Alert ID:** 304
* **Target Recipient:** `mateo@letsdefend.io`
* **Target Host:** Mateo's Host (`172.16.17.162`)
* **Device Action:** Allowed
* **Trigger Reason:** The MX record of a suspicious domain was changed, suggesting potential phishing activity.

Since the initial ThreatIntel alert indicated an allowed device action and an active MX record modification on a lookalike domain, I knew right away that this wasn't just a passive notification—an attacker was actively setting up infrastructure to target our organization.

---

## 🔍 Investigation Steps & Phishing Analysis

I immediately jumped into the **Email Security** and **Log Management** tabs to trace the attacker's footprint. The timeline and flow of the attack became incredibly clear:

1. **Infrastructure Setup (The Trap):** The threat actor registered a typosquatted domain—`letsdefwnd[.]io` (notice the malicious extra **'w'** mimicking the official `letsdefend.io`). They configured its MX (Mail Exchanger) record to point to `mail.mailerhost[.]net` to enable sending and receiving corporate-targeted emails.
2. **The Phishing Vector (The Hook):** Using this newly created infrastructure, the attacker sent a phishing email from `voucher@letsdefwnd[.]io` to our employee `mateo@letsdefend.io` with the enticing subject: *"Congratulations! You've Won a Voucher"*. The email contained a malicious hyperlink hidden inside the text.
3. **The Compromise (Hands-on-Keyboard):** When checking the exchange logs, I verified that the email was successfully **Delivered**. Moving over to the Log Management and Network Action tabs, the worst-case scenario was confirmed: Mateo fell for the bait. His host (`172.16.17.162`) clicked the link, establishing a live network connection to the malicious external IP address **`45.33.23.183`**.

---

## 💡 Lessons Learned & Playbook Logic (My Thought Process)

This case was a fantastic lesson in correlating Threat Intelligence with live Endpoint Logs:

* **Why True Positive?** While the alert initially started as a static threat intel flag regarding an external domain's MX record, digging into the proxy and network logs proved that the threat had successfully bridged into our network. It wasn't a false alarm; it was a fully executed, successful interactive phishing attack.
* **The Importance of Quick Triage:** Because the security device action was `Allowed`, the malicious link was live, and the user successfully initiated communication with the malicious external C2 infrastructure. This highlighted why immediate containment is vital before lateral movement begins.

---

## 🎯 Remediation & Conclusion

I successfully closed this case with a **100% success rate (20/20 Playbook Score)** as a **True Positive** by executing the following immediate response and containment steps:

* **Email Purge:** Successfully deleted/purged the malicious phishing email from Mateo’s corporate mailbox to prevent any further interaction or accidental clicks.
* **Host Isolation:** Instantly isolated (quarantined) Mateo's host (`172.16.17.162`) from the network to completely cut off the active connection to the malicious external IP and mitigate any secondary payload execution or lateral traversal.
* **IOC Blocklisting:** Extracted the malicious indicators (`letsdefwnd[.]io` and IP `45.33.23.183`) and added them to our threat intel and firewall blocklists.
* **MITRE ATT&CK Mapping:** Aligned the adversary behavior with industry standards: **T1598.003** (Phishing for Information), **T1566** (Phishing), and **T1656** (Impersonation).

**Final Status:** True Positive | Email Purged | Host Isolated | IOCs Blocked.
