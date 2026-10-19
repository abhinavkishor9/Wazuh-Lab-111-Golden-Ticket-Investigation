# Lab 111 — Investigation Notes

## 1. Investigation Objective

Assess the availability of Windows authentication telemetry relevant to a potential Kerberos Golden Ticket investigation using the existing Windows endpoint and Wazuh deployment.

The investigation focuses on evidence collection, audit policy validation, event visibility, and the distinction between workstation telemetry and domain-controller evidence.

## 2. Environment Assessment

The endpoint was identified as:

- Hostname: `DESKTOP-9MMM37V`
- Domain: `WORKGROUP`
- PartOfDomain: `False`
- DomainRole: `0`
- Monitoring platform: Wazuh
- Operating system: Windows 11 Pro

The endpoint is not joined to an Active Directory domain. Consequently, the environment does not provide the domain-controller perspective required to investigate Kerberos TGT and service-ticket activity comprehensively.

## 3. Evidence Collection

The investigation workspace was created at:

`C:\WazuhGoldenTicketLab111`

The following directories were created:

- `Evidence`
- `Notes`

The initial environment details were saved under the Notes directory. A Security event export was also attempted for Event IDs 4768, 4769, 4770, 4624, and 4672, covering the previous seven days.

The initial export contained no matching records.

## 4. Wazuh Configuration Review

The Wazuh agent configuration was inspected at:

`C:\Program Files (x86)\ossec-agent\ossec.conf`

The following event channels were present:

- `Security`
- `Microsoft-Windows-Sysmon/Operational`

Both were configured with the `eventchannel` log format.

The Security-channel configuration also contained an event-selection query excluding certain event IDs. The exclusions observed included 5145, 5156, and 5447. These exclusions do not directly explain missing Event IDs 4624, 4672, 4768, or 4769.

Configuration presence alone does not prove that every relevant event was forwarded, matched a rule, or indexed.

## 5. Audit Policy Review

The initial audit policy checks reported:

- Logon: No Auditing
- Special Logon: No Auditing

These settings provided a plausible explanation for the lack of expected successful-logon and special-privilege records.

Success auditing was subsequently enabled for both subcategories during troubleshooting.

A later Wazuh record confirmed the presence of Event ID 4672.

## 6. Event Evidence

### Event ID 4672

A Security event was observed in Wazuh with these details:

- Computer: `DESKTOP-9MMM37V`
- Channel: `Security`
- Event ID: `4672`
- Event record ID: `2416`
- Account: `NETWORK SERVICE`
- Account domain: `NT AUTHORITY`
- Logon ID: `0x3E4`
- Process ID: `1216`

The event listed these privileges:

- `SeAssignPrimaryTokenPrivilege`
- `SeAuditPrivilege`
- `SeImpersonatePrivilege`

This confirms that a special-privileges event was visible in Wazuh. The event alone does not indicate Golden Ticket activity or establish that the associated account was compromised.

### Event ID 4624

Event ID 4624 was included in the initial local investigation query, but the initial query returned no matching records. Subsequent investigation identified authentication telemetry as a focus for audit-policy validation.

Any claim about a particular 4624 record should be supported by its event details and timestamp.

### Events 4768 and 4769

The local workstation query did not return Events 4768 or 4769.

Because the endpoint was in a workgroup, these results were consistent with the absence of an Active Directory domain-controller role. The relevant Kerberos evidence would need to be collected from a domain controller in an appropriate lab environment.

## 7. Investigation Assessment

### Confirmed

- The endpoint was in a workgroup.
- The Windows Security log was enabled and contained records.
- The Wazuh configuration included Security and Sysmon Operational channels.
- Logon and Special Logon success auditing were initially disabled.
- A Security Event ID 4672 record was visible in Wazuh.
- The workstation did not provide the required domain-controller evidence for a conclusive Golden Ticket investigation.

### Not established

- Whether any Golden Ticket attack occurred.
- Whether a forged Kerberos TGT was used.
- Whether a domain controller generated or received suspicious Kerberos requests.
- Whether every locally generated Security event was forwarded and indexed by Wazuh.

## 8. Conclusion

The lab validated part of the endpoint authentication telemetry workflow and identified a domain-controller visibility limitation.

The observed Event ID 4672 is a valid investigation artifact, but it does not establish malicious activity. A conclusive Golden Ticket investigation requires correlated Kerberos evidence from the appropriate Active Directory infrastructure.
