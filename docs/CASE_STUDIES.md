# SOC Investigation Findings

This document summarizes five investigations from a controlled, single-endpoint Windows 11 lab. Security conclusions apply **only to the evidence examined**.

## F01 — .NET Runtime application error
- **Windows event:** Application Event ID 1022
- **Wazuh:** Rule 61020, level 5
- **Observation:** Failure to initialize .NET profiling attach infrastructure (`0x80004005`).
- **Assessment:** Application/runtime failure; no evidence in the event of unauthorized access.
- **Response:** Monitor for recurrence; investigate application stability if symptoms persist.

## F02 — Cryptographic operation audit failure
- **Windows event:** Security Event ID 5061
- **Wazuh:** Rule 60104, level 5
- **Observation:** Microsoft Software Key Storage Provider reported error `0x80090016` (NTE_BAD_KEYSET).
- **Assessment:** Cryptographic failure, **not** a failed-login event. Root cause not determined.
- **Response:** Correlate further only if recurring or affecting application functionality.

## F03 — Historical failed logons
- **Windows event:** Security Event ID 4625 (two events, 18 Nov 2026)
- **LogonType:** 2 (interactive); **Status:** `0xC000006D`; **SubStatus:** `0xC000006A` (incorrect password).
- **SourceIP:** not recorded; **associated process:** ASUS Software Manager executable located in Windows DriverStore.
- **Verification:** The executable existed and returned a Valid Authenticode signature from Microsoft Windows Hardware Compatibility Publisher.
- **Assessment:** The records do not show a network source, confirmed unauthorized access, or brute force. Who submitted the credentials is unknown.
- **Evidence boundary:** The events happened **before** Wazuh agent enrollment and were obtained from local Windows Security logs, not from a Wazuh-generated historical alert.

## F04 — Windows Error Reporting diagnostic
- **Windows event:** Application Event ID 1001
- **Wazuh:** Rule 60608, level 4
- **Observation:** Windows Error Reporting identified `SnippingTool.exe` and event name `RADAR_PRE_LEAK_64`.
- **Assessment:** Possible memory-related diagnostic condition; not proof of a crash, confirmed leak, or malicious activity.
- **Response:** Monitor recurrence and application performance.

## F05 — McAfee service configuration changes
- **Windows events:** System Event IDs 7040 (startup type changed), 7045 (service installation)
- **Wazuh:** Rule 61104, level 3 for the 7040 change
- **Observation:** A broader local query found 24 Event ID 7040 records in the selected 24-hour window. Some involved McAfee-related services; others involved Windows services.

### Correlated events, 20 Nov 2026

| Local time | Event | Evidence |
| --- | --- | --- |
| 17:07:59 | 7045 | McAfee Dynamicappdownloader service installation recorded; demand start, LocalSystem |
| 17:08:37 | 7040 | Same service name: demand start → disabled |
| 17:09:55 | 7045 | Another service installation event referencing the same executable |
| 17:10:06 | 7040 | Same service name: demand start → disabled |

- **Signature check:** `mc-dad.exe` existed and its Authenticode signature was Valid, signer **McAfee, LLC**.
- **Current service lookup:** No matching Dynamicappdownloader service was listed when queried.
- **Protection check:** Microsoft Defender Antivirus was reported On / Active.
- **Limitations:** The records do not identify the initiator or confirm why the service was subsequently absent. Repeated events might reflect a transient software workflow, but this is unverified.
- **Disposition:** No confirmed security incident in the examined records. No containment justified; monitoring and validation recommended.

## Analyst lessons

1. Distinguish events, detection rules, alerts, and verified security incidents.
2. Use event IDs, context, and timestamps rather than relying on severity labels alone.
3. A valid signature supports authenticity verification; it is not a guarantee of safe behavior.
4. Record unknowns and investigation limitations explicitly.
5. Keep a private original evidence set and publish only redacted copies.
