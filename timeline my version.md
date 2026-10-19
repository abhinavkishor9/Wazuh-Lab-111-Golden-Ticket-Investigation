# Investigation Timeline

| Sequence | Activity | Evidence / Observation |
|---|---|---|
| 1 | Checked domain membership | `DESKTOP-9MMM37V` reported `WORKGROUP`, `PartOfDomain = False`, and `DomainRole = 0`. |
| 2 | Created investigation workspace | Created `C:\WazuhGoldenTicketLab111\Evidence` and `C:\WazuhGoldenTicketLab111\Notes`. Directory timestamps showed 19 October 2026 at approximately 06:54. |
| 3 | Exported Windows Security events | Queried Event IDs 4768, 4769, 4770, 4624, and 4672 for the previous seven days. The initial export contained no matching records. |
| 4 | Reviewed Wazuh agent configuration | Confirmed Security and Sysmon Operational channel collection blocks were present. |
| 5 | Checked Security log readability | The Security log returned recent events, including 4719, 6417, and 4688. |
| 6 | Checked audit policy | Logon and Special Logon success auditing were initially set to `No Auditing`. |
| 7 | Reviewed Security channel status | The channel was enabled and reported 1,692 records at the time of the check. |
| 8 | Enabled success auditing | Enabled success auditing for Logon and Special Logon during troubleshooting. |
| 9 | Examined Wazuh event evidence | A Security Event ID 4672 record was observed for `DESKTOP-9MMM37V`. |
| 10 | Assessed Kerberos telemetry availability | Events 4768 and 4769 were not found in the workstation's local Security log during the checks performed. |
| 11 | Documented the investigation limitation | The workgroup environment did not provide the domain-controller evidence needed to confirm or rule out Golden Ticket activity. |

