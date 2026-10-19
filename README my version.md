# Wazuh-Lab-111-Golden-Ticket-Investigation
## Overview
A Golden Ticket is a forged Kerberos Ticket Granting Ticket (TGT) that an attacker may create after compromising the secret key associated with the Active Directory krbtgt account.

In a normal Active Directory environment, Kerberos authenticates users and helps them obtain access to network services. A Golden Ticket attack abuses the trust placed in Kerberos tickets to potentially impersonate users or obtain unauthorized access.

1. Normal authentication

A user requests a Kerberos TGT from the domain controller.
|
V
2. Potential domain compromise

An attacker obtains the krbtgt secret key.
|
V
3. Forged ticket may be used

The attacker attempts to use a forged TGT to impersonate an account.
|
V
4. SOC investigation

Correlate authentication events, ticket details, endpoint activity, and domain-controller evidence.


This lab explores the telemetry and investigation requirements for identifying potential Kerberos Golden Ticket activity using Windows Security logs and Wazuh.

The investigation began by checking the available Windows authentication events and validating the existing Wazuh agent configuration. The endpoint was found to be running in a workgroup rather than an Active Directory domain, which limited the availability of domain-controller Kerberos telemetry.

The lab focuses on understanding which events are available, validating audit policy settings, examining Wazuh event visibility, and documenting the limitations that prevent a conclusive Golden Ticket investigation.

## Lab Objectives

- Understand the role of Kerberos authentication and the security risks associated with Golden Ticket attacks.
- Identify the Windows Security events relevant to investigating suspicious authentication activity, including Event IDs 4624, 4672, 4768, 4769, and 4770.
- Validate whether the available Windows endpoint generates and records the expected authentication-related security events.
- Review Windows audit policy settings and understand how auditing configuration affects the visibility of logon and privileged-logon activity.
- Verify the Wazuh agent's Security event-channel collection configuration and examine the available endpoint telemetry.
- Correlate observed authentication and privilege-related events while distinguishing routine system activity from potentially suspicious behavior.
- Document missing Kerberos events, investigate plausible explanations for their absence, and identify the limitations of investigating domain-level attacks from a standalone workstation.
- Establish what evidence would be required from a domain controller to support a more conclusive Golden Ticket investigation.
- Produce evidence-based investigation notes that clearly separate confirmed observations, reasonable interpretations, and unresolved questions.

## Lab Scenario

A security analyst is reviewing authentication telemetry collected from a Windows endpoint monitored by Wazuh. The investigation focuses on understanding whether the available logs provide sufficient visibility to support a potential **Golden Ticket attack investigation**.

A Golden Ticket attack involves forging a Kerberos Ticket-Granting Ticket (TGT), typically after an attacker compromises the `krbtgt` account secret in an Active Directory domain. Because this activity depends on domain-level Kerberos authentication, investigating it requires appropriate domain controller logs and supporting endpoint evidence.

During the investigation, the analyst reviews Windows Security events, examines audit policy settings, and validates the Wazuh agent's event collection configuration. Events **4624 (successful logon)** and **4672 (special privileges assigned to a new logon)** are available, while Kerberos-related events **4768 (TGT requested)** and **4769 (service ticket requested)** are absent from the reviewed local telemetry.

The endpoint is configured as a standalone workgroup computer rather than a member of an Active Directory domain. This limits the Kerberos evidence available locally and means that the missing domain-controller events cannot be treated as proof of an attack or a detection failure.

The investigation aims to determine what the existing evidence establishes, identify telemetry and environment limitations, and document which additional logs would be needed for a stronger assessment.

### Investigation Focus

- Review available authentication and privileged-logon events.
- Validate relevant Windows audit settings and Wazuh event collection.
- Examine the absence of expected Kerberos event IDs in the available evidence.
- Distinguish confirmed observations from assumptions about attacker activity.
- Document the limitations of investigating a domain-level Golden Ticket attack without domain controller telemetry.

**Expected outcome:** An evidence-based investigation record describing the available telemetry, collection findings, unresolved questions, and additional evidence required for a conclusive Golden Ticket investigation. This lab evaluates investigation readiness and telemetry visibility; it does not claim that a Golden Ticket attack was executed or detected.

## Lab Environment

| Component | Details |
|---|---|
| Endpoint | `DESKTOP-9MMM37V` |
| Operating system | Windows 11 Pro |
| Domain membership | Workgroup |
| Domain name reported | `WORKGROUP` |
| PartOfDomain | `False` |
| DomainRole | `0` |
| SIEM / monitoring | Wazuh |
| Wazuh agent configuration | `C:\Program Files (x86)\ossec-agent\ossec.conf` |
| Security event channel | `Security` |
| Sysmon event channel | `Microsoft-Windows-Sysmon/Operational` |
| Evidence directory | `C:\WazuhGoldenTicketLab111\Evidence` |
| Notes directory | `C:\WazuhGoldenTicketLab111\Notes` |

## Investigation Scope

The investigation covers:

- Windows Security event availability.
- Successful logon and special-privilege auditing.
- Wazuh agent collection configuration.
- Authentication event visibility in Wazuh Discover.
- Missing Kerberos event evidence and its implications.

This lab does not simulate a Golden Ticket attack or establish that ticket forgery occurred.

## Relevant Windows Event IDs

| Event ID | Description | Investigation relevance |
|---|---|---|
| `4768` | Kerberos authentication ticket requested | Investigates TGT request activity on a domain controller |
| `4769` | Kerberos service ticket requested | Investigates service-ticket activity on a domain controller |
| `4770` | Kerberos service ticket renewed | Provides additional ticket activity context |
| `4624` | Successful logon | Supports authentication and logon-session investigation |
| `4672` | Special privileges assigned to a new logon | Helps investigate privileged logon sessions |

Events 4768, 4769, and 4770 are particularly relevant on Active Directory domain controllers. Their absence from a standalone workstation does not establish malicious activity.

## Investigation Workflow

1. Check endpoint domain membership and establish the investigation scope.
2. Create dedicated evidence and notes directories.
3. Query the local Windows Security log for relevant event IDs.
4. Inspect the existing Wazuh agent configuration.
5. Review Windows Logon and Special Logon audit policy settings.
6. Validate the presence of relevant events in Wazuh Discover.
7. Document evidence availability and the limitations affecting the investigation.

## Findings Summary

- The endpoint was identified as a workgroup computer rather than a domain-joined workstation.
- The Wazuh agent configuration included the Windows Security and Sysmon Operational channels.
- The Security channel was enabled and contained event records.
- Initial local queries returned no matching records for Event IDs 4624, 4672, 4768, and 4769 within the selected seven-day period.
- Logon and Special Logon success auditing were initially disabled.
- A Wazuh Security Event ID 4672 record was subsequently observed for `DESKTOP-9MMM37V`.
- Events 4768 and 4769 were not found in the workstation's local Security log during the checks performed.

