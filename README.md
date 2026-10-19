# Lab 111 — Golden Ticket Investigation

## Overview

This lab explores the telemetry and investigation requirements for identifying potential Kerberos Golden Ticket activity using Windows Security logs and Wazuh.

The investigation began by checking the available Windows authentication events and validating the existing Wazuh agent configuration. The endpoint was found to be running in a workgroup rather than an Active Directory domain, which limited the availability of domain-controller Kerberos telemetry.

The lab focuses on understanding which events are available, validating audit policy settings, examining Wazuh event visibility, and documenting the limitations that prevent a conclusive Golden Ticket investigation.

## Objectives

- Understand Golden Ticket attacks and the role of the `krbtgt` account.
- Identify the purpose of Windows Security Event IDs 4768, 4769, 4624, and 4672.
- Validate the Windows Security log and relevant audit policy settings.
- Examine Wazuh collection settings for the Security and Sysmon Operational channels.
- Investigate the availability of authentication-related events in Wazuh.
- Distinguish endpoint authentication evidence from domain-controller Kerberos evidence.
- Document confirmed observations, telemetry gaps, and investigation limitations.

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

## Conclusion

The investigation established that Windows Security telemetry and Wazuh visibility can be examined on the existing endpoint. It also identified an audit-policy limitation and the absence of domain-controller evidence required for a conclusive Golden Ticket investigation.

The available evidence does not confirm Golden Ticket activity. Further investigation would require appropriate Active Directory domain-controller telemetry and correlated authentication evidence.

## Disclaimer

This lab is intended for defensive security learning and investigation practice. Findings are limited to the evidence collected in the documented environment. Missing telemetry must not be interpreted as proof of an attack.
