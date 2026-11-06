# Day 29 — Splunk Alert Creation: New User Account Detection

## Overview

I was tasked with creating a medium-severity alert to detect newly created user accounts. The alert needed to run hourly and send triggered events to Splunk's Triggered Alerts page.

Rather than immediately following the walkthrough, I attempted to develop the detection independently using the MITRE ATT&CK framework as my starting point.

## Detection Research

I identified [**T1136 — Create Account**](https://attack.mitre.org/techniques/T1136/) in MITRE ATT&CK. Reviewing its Detection Strategy provided useful guidance on the telemetry and activity to look for across Windows and Linux systems.

I used that information to develop two separate detections.

### 1. Linux — User Account Creation

For Linux, I searched the authentication logs for references to the `useradd` and `adduser` commands, commonly used to create user accounts.

My initial SPL query:

```spl
index="mydfir-lab1" source="auth.log" sourcetype=linux_auth_logs (useradd OR adduser)
| eval message=_raw
| table _time host message
| sort _time
```

Given the limited fields available in the Linux events, I kept the output simple:

- `_time` — When the event was recorded.
- `host` — The host reporting the event.
- `message` — The original log message.

<img width="1506" height="372" alt="image" src="https://github.com/user-attachments/assets/6ca65000-5191-4dfd-9998-96809b625a3e" />

I'm sure this query can be refined further once I learn to use `rex`, as the raw message contains additional information that could help confirm whether an account was successfully created.

### 2. Windows — User Account Creation

For Windows, I explored the available event data, including Windows Event Logs and Sysmon telemetry.

I ultimately settled on **Windows Security Event ID 4720**, which records the creation of a user account.

I selected fields that would provide useful context for an analyst investigating the event:

| Field | Purpose |
|---|---|
| `_time` | When the account was created |
| `host` | Splunk host reporting the event |
| `ComputerName` | Windows computer where the account was created |
| `Subject_Account_Name` | Account that initiated the creation |
| `New_Account_Account_Name` | Newly created account |
| `New_Account_Domain` | Domain or computer context of the new account |
| `Logon_ID` | Logon session identifier of the account creator |

I also expanded the Windows alert description to provide investigation guidance.

The `Logon_ID` can be used to correlate other Windows Security events generated during the creator's logon session. I also included related Event IDs worth investigating:

- **4722** — User account enabled.
- **4728** — User added to a security-enabled global group.
- **4732** — User added to a security-enabled local group.

Additionally, I recommended reviewing subsequent activity associated with the newly created account for signs of suspicious behavior or potential compromise.

## Alert Configuration

Both detections were configured as medium-severity alerts, following the requested naming convention:

`Endpoint - <MITRE ATT&CK ID> - <ACTIVITY>`

The alerts were configured to:

- Run every hour.
- Send triggered events to the Triggered Alerts page.
- Use the requested sharing permission: Share in App.
- Include descriptions to provide analysts with context and investigation guidance.

## Triage Considerations

Creating a new user account is not inherently malicious. It is also a routine administrative activity. However, attackers may create accounts as a persistence mechanism to maintain access to a compromised environment.

If this alert triggered in a production environment, I would consider the following during triage:

1. **Investigate the creator account.** Determine whether the account that initiated the creation was authorized to do so. Review its activity for signs of potential compromise.
2. **Investigate the newly created account.** Determine whether it was enabled, added to privileged groups, or used to authenticate.
3. **Correlate related events.** Use the available logon session information and related Windows Security events to build a clearer picture of the activity.
4. **Establish legitimacy.** Check whether the account creation was expected and associated with an approved administrative task or change.

The alert is assigned medium severity because account creation is a common occurrence and should not automatically be treated as malicious. Its priority should also be considered alongside other active alerts and the SOC's escalation procedures.

## Takeaways

I was pleased to find that my independent approach aligned closely with the walkthrough solution. I also went a step further by developing an additional Linux detection and expanding the Windows alert description with investigation guidance.

A few things I took away from this exercise:

- MITRE ATT&CK is useful not only for classifying adversary behavior but also for guiding detection development.
- Detection logic needs to account for differences in available telemetry across operating systems.
- Alert output should provide useful context to the analyst, not simply indicate that an event occurred.
- An alert is the starting point of an investigation, not a verdict. Context and correlation are necessary before determining whether activity is malicious.

Questions to ask:
- Was new account initiated by an authorized administrator or service account?
- Was there an approved change request or other legitimate business reason for the new account?
- Is it normal for the host to be used for account creation?
- Was the new account immediately enabled or added to privileged groups?
- Did the new account subsequently establish remote sessions or execute unusual processes?

**Next steps:** Continue improving the Linux query by learning to extract structured fields from raw log messages using Splunk's `rex` command.
