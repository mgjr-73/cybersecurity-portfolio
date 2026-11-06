# Splunk — Windows Activity Dashboard Enhancement

**SOC Accelerator | Day 28 | Splunk | Windows Security Logs**

## Overview

As part of the SOC Accelerator training, I was given a scenario in which my SOC manager requested an additional panel on an existing Splunk dashboard called **Windows Activity**.

The objective was to monitor changes to the Windows Firewall Exception List, specifically newly added rules.

The requested panel needed to display:
- Time of the change
- Computer
- Profile Changed
- Rule Name
- Rule ID

Rather than immediately looking for the solution, I approached the task by examining the available events and researching the relevant Windows Security Event IDs.

## Investigation and Research

I started with a broad keyword search for `exception` in the `mydfir-lab1` index. This returned 106 events.

I then examined the available fields and narrowed down the relevant data source:

- **Host:** `winvm`
- **Source:** `WinEventLog:Security`
- **Sourcetype:** `WinEventLog`

While examining the events, I found two Event IDs associated with Windows Firewall exception list changes:

- **4946** — A Windows Firewall exception rule was added.
- **4948** — A Windows Firewall exception rule was deleted.

Since the task specifically requested newly added rules, I filtered for Event ID 4946, which returned 53 events.

I then expanded individual events to identify the exact field names needed for the dashboard panel.

## SPL Development

After identifying the relevant fields, I developed the following query:

```spl
index="mydfir-lab1" host=winvm source="WinEventLog:Security"
sourcetype=WinEventLog EventCode=4946
| table _time ComputerName Profile_Changed Rule_ID Rule_Name
| sort _time
```

The query filters for Windows Security Event ID 4946 and displays the requested information in chronological order, starting with the earliest event.

The selected fields provide an analyst with the basic context needed to review firewall rule additions, including when the change occurred, which computer reported it, and the name and identifier of the new rule.

## Dashboard Integration

I added the query as a new panel to the existing **Windows Activity** dashboard and named it **Firewall Exception Added**, following the SOC manager's instructions.

The dashboard brings together multiple types of Windows security activity, including newly created user accounts and firewall exception changes.

One observation from reviewing the dashboard was that the host `Desktop-IT.elnath.com` appeared as a common denominator across some of the displayed activity.

This provided an initial investigative lead and demonstrated the value of viewing related security events together. Of course, a shared host does not automatically establish that the activities are connected or malicious. Further investigation and correlation would be necessary.

## Additional Research: Event ID 2004 vs. 4946

While researching Windows Firewall events, I also came across Event ID 2004.

Both Event IDs report the addition of a Windows Firewall rule, but they originate from different logging channels:

| Event ID | Log Source | Description |
|---|---|---|
| 2004 | Windows Firewall Operational log | A firewall rule was added. |
| 4946 | Windows Security log | A change was made to the Windows Firewall exception list by adding a rule. |

An important distinction is that Event ID 4946 requires the appropriate Windows audit policy to be enabled. According to the reference material, it also does not generate when a rule is added through Group Policy.

Understanding these differences is useful when considering Windows Firewall monitoring and potential gaps in event visibility.

## Takeaways

This exercise helped reinforce several skills:

- **SPL development:** Using index, host, source, sourcetype, and Event ID filters to narrow down relevant events.
- **Windows event analysis:** Identifying and understanding the purpose of Event ID 4946.
- **Dashboard development:** Turning raw security events into a structured panel that provides useful context to an analyst.
- **Independent research:** Looking beyond the immediate exercise to understand related Windows Firewall events and their logging behavior.

One thing I took away from this exercise is that a dashboard is more useful when its panels provide context that can help an analyst identify relationships between events and determine where to investigate next.

The dashboard doesn't establish whether an activity is malicious. It helps bring relevant information together so an analyst has a starting point for further investigation.
