# Windows Authentication Log Investigation: Detecting Suspicious Login Activity

## 1. Project Overview

This project demonstrates a basic Security Operations Center (SOC) investigation of suspicious Windows authentication activity using a simulated Windows Security log dataset.

The investigation focuses on identifying repeated failed logon attempts, correlating failed logons with subsequent successful authentication, developing an incident timeline, assessing potential risk, and recommending appropriate response actions.

The project simulates a common SOC investigation scenario involving possible password guessing against user accounts.

**Project Type:** Security Monitoring and Incident Investigation
**Focus Area:** SOC Analysis
**Dataset:** Simulated Windows authentication logs
**Tools:** CSV, Microsoft Excel or Apple Numbers

## 2. Objectives

The objectives of this investigation were to:

* Understand Windows Security Event IDs 4624 and 4625.
* Identify repeated failed authentication attempts.
* Determine which accounts received the most failed login attempts.
* Correlate failed logons with subsequent successful authentication.
* Construct an incident timeline.
* Assign a preliminary severity based on available evidence.
* Recommend investigation and incident response actions.

## 3. Dataset Description

The dataset contains 13 simulated Windows authentication events recorded on `WIN-SERVER-01` on 10 October 2026.

The CSV file contains the following fields:

| Field         | Description                                    |
| ------------- | ---------------------------------------------- |
| `timestamp`   | Date and time of the event                     |
| `host`        | Hostname associated with the event             |
| `event_id`    | Windows Security Event ID                      |
| `event_type`  | Successful or failed logon                     |
| `target_user` | Account involved in the authentication attempt |
| `source_ip`   | Source IP recorded in the event                |
| `logon_type`  | Type of logon                                  |
| `status`      | Authentication result or status code           |
| `sub_status`  | Additional authentication failure information  |

The dataset is simulated for educational purposes. It is not a collection of logs from a live Windows environment.

## 4. Windows Event IDs Investigated

### Event ID 4624 — Successful Logon

Records a successful logon. In this investigation, successful authentication events were examined to determine whether they followed repeated failed attempts.

### Event ID 4625 — Failed Logon

Records a failed logon attempt. Repeated failures involving the same account and source IP may indicate password guessing, although legitimate causes must also be considered.

### Relevant Authentication Status Codes

* `0xC000006D`: General logon failure.
* `0xC000006A`: Incorrect password, represented here as the sub-status.

These values help describe the authentication failure recorded in the simulated dataset.

## 5. Investigation Methodology

The investigation followed these steps:

1. Opened the CSV dataset in a spreadsheet application.
2. Reviewed the Event ID column to distinguish successful and failed logons.
3. Counted authentication events by Event ID.
4. Grouped failed logons by target account and source IP.
5. Examined the sequence of failed and successful logons.
6. Developed a timeline of the suspicious authentication pattern.
7. Assessed the potential risk and documented recommended next steps.

## 6. Investigation Findings

### Authentication Event Summary

| Event ID  | Event Type                |  Count |
| --------- | ------------------------- | -----: |
| 4624      | Successful logon          |      4 |
| 4625      | Failed logon              |      9 |
| **Total** | **Authentication events** | **13** |

### Failed Logons by Account

| Target Account   | Failed Attempts |
| ---------------- | --------------: |
| `j.morgan`       |               4 |
| `administrator`  |               3 |
| `service_backup` |               2 |
| **Total**        |           **9** |

### Key Observation

Source IP `10.20.5.44` generated repeated failed logon attempts against multiple accounts, including `administrator` and `j.morgan`.

The `j.morgan` account experienced four failed authentication attempts between 09:12:20 and 09:12:38.

At 09:12:45, the same account successfully authenticated from `10.20.5.44`, seven seconds after the final recorded failure.

This sequence is consistent with a possible password-guessing attempt followed by successful authentication. However, the available evidence does not establish whether the activity was malicious or whether the successful login was authorised.

## 7. Incident Timeline

| Timestamp         | Event            | Observation                                          |
| ----------------- | ---------------- | ---------------------------------------------------- |
| 09:12:03          | Failed logon     | An attempt against `administrator` from `10.20.5.44` |
| 09:12:08          | Failed logon     | Another failed attempt against `administrator`       |
| 09:12:14          | Failed logon     | A third failed attempt against `administrator`       |
| 09:12:20–09:12:38 | Failed logons    | Four failed attempts against `j.morgan`              |
| 09:12:45          | Successful logon | `j.morgan` authenticated from the same source IP     |

**Timeline assessment:** The close sequence of failed and successful authentication events warrants further investigation to establish whether the successful login was legitimate.

## 8. Preliminary Severity Assessment

**Initial Severity: Medium**

**Status: Under Investigation**

The activity was assigned a preliminary medium severity because of repeated failed logons from a single source IP, attempts against multiple accounts, and a successful authentication shortly after several failures against `j.morgan`.

The pattern may indicate password guessing followed by successful authentication.

However, the available evidence does not confirm account compromise, unauthorised access, or subsequent malicious activity. The final severity would depend on the organisation's incident response policy, the importance of the affected asset, and additional evidence.

## 9. Incident Response Recommendations

### Investigation Actions

1. **Validate the source IP:** Determine whether `10.20.5.44` belongs to an authorised workstation, server, VPN, or administrative system.
2. **Verify the successful login:** Confirm whether the `j.morgan` authentication at 09:12:45 was expected.
3. **Review related authentication events:** Examine additional logs for repeated failures, successful logons, and activity involving other accounts or systems.
4. **Investigate post-authentication activity:** Review available process creation, account management, privilege changes, and network activity logs.
5. **Preserve evidence:** Retain relevant logs and document the investigation timeline.
6. **Contain if warranted:** If unauthorised access is supported by the evidence, follow established incident response procedures. Depending on the findings, this may include restricting the affected account, revoking sessions, or blocking the source where appropriate.
7. **Escalate and reassess:** Escalate according to organisational policy and revise the severity as new evidence becomes available.

### Preventive Measures

* Configure appropriate account lockout or authentication throttling policies.
* Enable alerts for repeated failed logons followed by successful authentication.
* Enforce multifactor authentication, especially for privileged and remote access.
* Review privileged accounts and access permissions.
* Centralise Windows Security logs in a SIEM for correlation and alerting.
* Monitor account creation and privilege changes.

## 10. Investigation Limitations

This project uses a simulated dataset containing only 13 authentication events. It does not contain raw Windows Event Viewer exports or additional endpoint telemetry.

The investigation therefore cannot establish:

* Whether the source IP was authorised.
* Whether the successful authentication was malicious.
* Whether the account was compromised.
* Whether suspicious commands or processes were executed.
* Whether data was accessed or transferred after authentication.

Further investigation would require additional authentication, endpoint, and network evidence.

## 11. Key Learnings

Through this project, I practised:

* Interpreting Windows Security Event IDs 4624 and 4625.
* Counting and grouping authentication events.
* Identifying repeated failed logon attempts.
* Correlating authentication failures with a subsequent successful login.
* Constructing an incident timeline.
* Assessing preliminary incident severity.
* Writing evidence-based incident response recommendations.
* Documenting investigation limitations and avoiding unsupported conclusions.

## 12. Conclusion

This investigation identified a suspicious authentication pattern involving repeated failed logon attempts from `10.20.5.44`, followed by a successful login to `j.morgan` from the same source IP.

The pattern warrants further investigation but does not, by itself, confirm a brute-force attack or account compromise.

The project demonstrates a foundational SOC workflow: review authentication logs, identify anomalies, correlate events, assess risk, document evidence, and recommend appropriate next steps.

**Disclaimer:** This project was completed using simulated authentication data for learning and portfolio demonstration. No real account compromise was investigated or confirmed.
