# Day 33: MITRE ATT&CK Mapping

## Overview

For Day 33 of the MYDFIR SOC Accelerator, I completed an exercise focused on mapping adversary behavior to the MITRE ATT&CK framework.

The exercise presented a ransomware incident and challenged me to identify the tactics and techniques associated with the attacker's actions, from initial access through impact.

The objective was to practice breaking down an incident narrative, identifying attacker behavior, and mapping those behaviors to the appropriate ATT&CK tactics and techniques.

Rather than simply matching keywords to technique IDs, I approached the exercise by separating the scenario into individual actions and considering the objective behind each one.

## Scenario
> A client experienced a ransomware attack a couple months ago and the incident response (IR) team had identified the initial entry point. It was determined that a malicious PDF document had been delivered via phishing and executed on January 1st, 2023. The threat actor leveraged "living off the land" binaries to install additional malware, created scheduled tasks for persistence, and searched for additional hosts via nslookup. They also created a new domain account with domain administrative privileges, which was used to disable antivirus and was observed connecting to other machines via Remote Desktop Protocol (RDP), eventually compromising the domain controller. Furthermore, the new domain account created a file named "1.7z" and there is evidence of large outbound traffic towards transfer.sh indicating likely successful exfiltration. Subsequently, a ransomware binary was deployed rendering critical hosts inaccessible.

The breakdown:

- Entry point was identified as a malicious PDF delivered through phishing.
- Used living-off-the-land binaries to install additional malware.
- Created scheduled tasks for persistence.
- Used `nslookup` to search for additional hosts.
- Created a new domain account with domain administrative privileges.
- Used the account to disable antivirus.
- Connected to other machines using Remote Desktop Protocol (RDP).
- Created a file named `1.7z`.
- Generated large amounts of outbound traffic toward transfer.sh, indicating likely successful exfiltration.
- Deployed ransomware, rendering critical hosts inaccessible.

Based on these findings, I broke the scenario down into individual behaviors and identified the corresponding ATT&CK mappings.

## My ATT&CK Mapping vs Instructor Mapping

The following table summarizes my mapping decisions vs the instructor's answers.

| Notable Findings | My Tactic Picks | My Technique Picks | Instructor's Tactic Picks | Instructor's Technique Picks |
| --- | --- | --- | --- | --- |
| (1) malicious PDF document had been delivered via phishing and executed on January 1st, 2023. | ✅ TA0001 Initial Access | ✅ T1566.001 Spearphishing Attachment | TA0001 Initial Access | T1566 Phishing |
| (2) threat actor leveraged "living off the land" binaries to install additional malware | ✅ TA0002 Execution | ⭕ T1204 User Execution | TA0002 Execution | 1) T1204 User Execution 2) T1059 Command and Scripting Interpreter |
| (3) created scheduled tasks for persistence | ✅ TA0003 Persistence | ✅ T1053 Scheduled Task/Job | TA0003 Persistence | 1) T1053 Scheduled Task/Job 2) T1078 Valid Accounts |
| (4) searched for additional hosts via nslookup | ✅ TA0007 Discovery | ⭕ T1018 Remote System Discovery | TA0007 Discovery | T1046 Network Service Discovery |
| (5) created a new domain account with domain administrative privileges | ⭕ TA0003 Persistence | ⭕ T1136.002 Domain Account | TA0004 Privilege Escalation | T1078 Valid Accounts |
| (6) disable antivirus | ✅ TA0112 Defense Impairment | ✅ T1685 Disable or Modify Tools | TA0112 Defense Impairment | n/a |
| (7) connecting to other machines via Remote Desktop Protocol (RDP) | ✅ TA0008 Lateral Movement | ✅ T1021.001 Remote Desktop Protocol | TA0008 Lateral Movement | T021 Remote Services |
| (8) new domain account created a file named 1.7z | ✅ TA0009 Collection | ✅ T1560.001 Archive via Utility | TA0009 Collection | T1560 Archive Collected Data |
| (9) large outbound traffic towards transfer.sh indicating likely successful exfiltration | ✅ TA0010 Exfiltration | ✅ T1567 Exfiltration Over Web Service | TA0010 Exfiltration | T1567 Exfiltration Over Web Service |
| (10) ransomware binary was deployed rendering critical hosts inaccessible. | ✅ TA0040 Impact | ✅ T1486 Data Encrypted for Impact | TA0040 Impact | T1486 Data Encrypted for Impact |

Some mappings required more interpretation than others. The scenario provided limited technical detail, so I treated certain selections as candidate mappings rather than confirmed findings.

### Execution and Living-off-the-land Binaries

The scenario stated that the attacker used living-off-the-land binaries to install additional malware.

In my initial notes, I associated this activity with T1105 Ingress Tool Transfer under Command and Control. During the comparison with the instructor's answers, I reconsidered the activity from an Execution perspective.

The instructor identified two techniques:

- T1204 User Execution
- T1059 Command and Scripting Interpreter

I considered T1204 as a possible mapping because the malicious PDF was executed. However, the scenario did not identify the specific binaries or execution methods used to install the additional malware.

### Discovery: Remote System Discovery vs. Network Service Discovery

One difference between my mapping and the instructor's was the discovery activity.

The scenario explicitly stated that the attacker searched for additional hosts using `nslookup`.

I selected T1018 Remote System Discovery because `nslookup` performs DNS queries that can identify hostnames and their associated IP addresses. This information can help an attacker identify other systems in an environment.

The instructor selected T1046 Network Service Discovery.

I retained T1018 because the scenario described identifying additional hosts, not discovering open ports or services running on those hosts. Nmap, for example, can be used for host discovery, port scanning, and service detection depending on how it is configured.

### Domain Account Creation and Privilege Assignment

Another difference involved the creation of a new domain account with domain administrative privileges.

I selected:

- TA0003 Persistence
- T1136.002 Domain Account

My focus was on the account creation behavior. MITRE provides a specific technique for creating domain accounts to maintain access.

The instructor selected TA0004 Privilege Escalation and T1078 Valid Accounts.

After comparing the answers, I recognized that the scenario could describe several related but distinct activities:

1. Creating a domain account.
2. Assigning administrative privileges to the account.
3. Using the account to access other systems.

These actions can support different ATT&CK mappings. 

I retained T1136.002 for the account creation behavior because that was the specific action I was mapping.

## Comparing My Results with the Instructor

Most of the high-level tactic selections were aligned, but there were differences in the specific techniques and how certain behaviors were interpreted.

The most useful comparisons were:

| Behavior | My selection | Instructor's selection | My assessment |
|---|---|---|---|
| Execution and LOLBins | T1204 considered; T1105 in initial notes | T1204 and T1059 | Reasonable candidates, but more execution detail would help validate the mappings |
| Host discovery using `nslookup` | T1018 | T1046 | I retained T1018 because the scenario described discovering hosts rather than services |
| Domain account creation | T1136.002 | T1078 | I focused on account creation; privilege assignment and subsequent account usage may represent separate behaviors |

The comparison helped me identify where my reasoning needed refinement and where I had made a different interpretation based on the wording of the scenario.

## Key Takeaways

A few lessons stood out from this exercise:

- **Context matters.** A tool or command does not automatically determine the ATT&CK technique. The behavior and objective behind its use are important.
- **One technique can be associated with different tactics.** T1078 Valid Accounts is a good example. Its associated tactic depends on how the account is being used.
- **Separate related behaviors.** Account creation, privilege assignment, and account usage may represent distinct activities and should not automatically be treated as one event.
- **Recognize the limits of available information.** When a scenario lacks technical detail, some mappings will involve interpretation. It is important to distinguish what is explicitly described from what is inferred.
- Applying MITRE ATT&CK effectively requires understanding the behavior, considering the available context, and being able to explain why a particular mapping makes sense.
- Even when two analysts arrive at different mappings, the important part is being able to support the decision with a clear rationale and recognize when additional evidence is needed.
