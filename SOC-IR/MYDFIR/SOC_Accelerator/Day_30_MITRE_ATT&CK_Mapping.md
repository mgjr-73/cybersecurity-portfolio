# SOC Accelerator - Day 33: MITRE ATT&CK Mapping

## Overview

For Day 33 of the MYDFIR SOC Accelerator, I completed an exercise focused on mapping adversary behavior to the MITRE ATT&CK framework.

The exercise presented a ransomware incident and challenged me to identify the tactics and techniques associated with the attacker's actions, from initial access through impact.

The objective was to practice breaking down an incident narrative, identifying attacker behavior, and mapping those behaviors to the appropriate ATT&CK tactics and techniques.

Rather than simply matching keywords to technique IDs, I approached the exercise by separating the scenario into individual actions and considering the objective behind each one.

## Scenario

The exercise described a ransomware incident in which the initial entry point was identified as a malicious PDF delivered through phishing.

The attacker subsequently:

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

## My ATT&CK Mapping

The following table summarizes my mapping decisions.

| Observed behavior | Tactic | Technique |
|---|---|---|
| Malicious PDF delivered through phishing | TA0001 Initial Access | T1566.001 Spearphishing Attachment |
| Execution of malicious content and use of LOLBins | TA0002 Execution | T1204 User Execution (considered) |
| Malware installation using LOLBins | TA0011 Command and Control | T1105 Ingress Tool Transfer (initial interpretation) |
| Scheduled task creation | TA0003 Persistence | T1053 Scheduled Task/Job |
| Searching for additional hosts using `nslookup` | TA0007 Discovery | T1018 Remote System Discovery |
| Creation of a domain account | TA0003 Persistence | T1136.002 Domain Account |
| Disabling antivirus | TA0112 Defense Impairment | T1685 Disable or Modify Tools |
| Connecting to other machines through RDP | TA0008 Lateral Movement | T1021.001 Remote Desktop Protocol |
| Creation of `1.7z` | TA0009 Collection | T1560.001 Archive via Utility |
| Large outbound traffic toward transfer.sh | TA0010 Exfiltration | T1567 Exfiltration Over Web Service |
| Ransomware deployment | TA0040 Impact | T1486 Data Encrypted for Impact |

Some mappings required more interpretation than others. The scenario provided limited technical detail, so I treated certain selections as candidate mappings rather than confirmed findings.

### Execution and Living-off-the-land Binaries

The scenario stated that the attacker used living-off-the-land binaries to install additional malware.

In my initial notes, I associated this activity with T1105 Ingress Tool Transfer under Command and Control. During the comparison with the instructor's answers, I reconsidered the activity from an Execution perspective.

The instructor identified two techniques:

- T1204 User Execution
- T1059 Command and Scripting Interpreter

I considered T1204 as a possible mapping because the malicious PDF was executed. However, the scenario did not identify the specific binaries or execution methods used to install the additional malware. I therefore treated the more specific execution mappings cautiously.

### Discovery: Remote System Discovery vs. Network Service Discovery

One difference between my mapping and the instructor's was the discovery activity.

The scenario explicitly stated that the attacker searched for additional hosts using `nslookup`.

I selected T1018 Remote System Discovery because `nslookup` performs DNS queries that can identify hostnames and their associated IP addresses. This information can help an attacker identify other systems in an environment.

The instructor selected T1046 Network Service Discovery.

I retained T1018 because the scenario described identifying additional hosts, not discovering open ports or services running on those hosts. Nmap, for example, can be used for host discovery, port scanning, and service detection depending on how it is configured.

This was a useful reminder that the tool itself does not determine the ATT&CK technique. The behavior and objective matter.

### Domain Account Creation and Privilege Assignment

Another difference involved the creation of a new domain account with domain administrative privileges.

I selected:

- TA0003 Persistence
- T1136.002 Domain Account

My focus was on the account creation behavior. MITRE provides a specific technique for creating domain accounts to maintain access.

The instructor instead selected TA0004 Privilege Escalation and T1078 Valid Accounts.

After comparing the answers, I recognized that the scenario could describe several related but distinct activities:

1. Creating a domain account.
2. Assigning administrative privileges to the account.
3. Using the account to access other systems.

These actions can support different ATT&CK mappings. For example, T1136.002 describes domain account creation, while T1078 describes the use of valid accounts. Adding an account to a privileged group may also warrant a separate mapping.

I retained T1136.002 for the account creation behavior because that was the specific action I was mapping.

## Comparing My Results with the Instructor

After completing my initial mapping, I compared my answers with the instructor's.

Most of the high-level tactic selections were aligned, but there were differences in the specific techniques and how certain behaviors were interpreted.

The most useful comparisons were:

| Behavior | My selection | Instructor's selection | My assessment |
|---|---|---|---|
| Execution and LOLBins | T1204 considered; T1105 in initial notes | T1204 and T1059 | Reasonable candidates, but more execution detail would help validate the mappings |
| Host discovery using `nslookup` | T1018 | T1046 | I retained T1018 because the scenario described discovering hosts rather than services |
| Domain account creation | T1136.002 | T1078 | I focused on account creation; privilege assignment and subsequent account usage may represent separate behaviors |

The comparison helped me identify where my reasoning needed refinement and where I had made a different interpretation based on the wording of the scenario.

I did not treat the instructor's answers as automatically definitive. Instead, I considered the rationale behind each selection and whether the scenario supported it.

## ATT&CK Version Considerations

The instructor's material also included an update dated April 28, 2026, discussing changes introduced with ATT&CK v19.

One relevant change was the removal of the former Defense Evasion tactic and the introduction of separate classifications, including Stealth and Defense Impairment.

For the antivirus-disabling activity, the updated mapping was:

- TA0112 Defense Impairment
- T1685 Disable or Modify Tools

This was relevant because the original exercise material included older terminology. The instructor's update provided the current classification used in the exercise.

It was a useful reminder that ATT&CK mappings should be considered in the context of the framework version being used.

## Challenges and Considerations

This exercise was based on a summarized incident narrative rather than raw logs, endpoint telemetry, or forensic artifacts.

That limited how specifically I could map certain behaviors. For example:

- The scenario did not identify the specific LOLBins used or exactly how additional malware was installed.
- The use of `nslookup` was described as host discovery, but no DNS query results were provided.
- The domain account was described as having administrative privileges, but the process used to assign those privileges was not explained.
- The file `1.7z` suggested archiving, but its contents and creation method were not provided.
- Large outbound traffic toward transfer.sh indicated likely exfiltration, but the underlying transfer details were not available.

As a result, some mappings represent reasonable interpretations of the scenario rather than independently verified findings.

## Key Takeaways

A few lessons stood out from this exercise:

- **Context matters.** A tool or command does not automatically determine the ATT&CK technique. The behavior and objective behind its use are important.
- **One technique can be associated with different tactics.** T1078 Valid Accounts is a good example. Its associated tactic depends on how the account is being used.
- **Separate related behaviors.** Account creation, privilege assignment, and account usage may represent distinct activities and should not automatically be treated as one event.
- **Recognize the limits of available information.** When a scenario lacks technical detail, some mappings will involve interpretation. It is important to distinguish what is explicitly described from what is inferred.
- **Keep framework knowledge current.** ATT&CK classifications evolve, so older mappings and detection content may need to be reviewed against newer versions.

The exercise reinforced that MITRE ATT&CK is more than a lookup table. Applying it effectively requires understanding the behavior, considering the available context, and being able to explain why a particular mapping makes sense.

Even when two analysts arrive at different mappings, the important part is being able to support the decision with a clear rationale and recognize when additional evidence is needed.
