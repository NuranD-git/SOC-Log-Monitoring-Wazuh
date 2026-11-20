# Lab Setup and Validation

## Components

- **Host:** Windows 11 with Wazuh Windows agent
- **Virtualization:** Oracle VirtualBox
- **SIEM:** Wazuh appliance 4.14.10 (manager, indexer, and dashboard)
- **VM resources:** 4 virtual processors, 8 GB RAM, 50 GB virtual disk
- **Network:** NAT for outbound VM connectivity; Host-only for private host ↔ manager communication

## High-level deployment

1. Obtain the official Wazuh OVA from the Wazuh documentation and import it into VirtualBox.
2. Configure the imported VM with the resource settings above and a private Host-only adapter.
3. Boot the VM, identify its Host-only IPv4 address, and verify that the host can reach its dashboard through HTTPS.
4. Change the default appliance and dashboard credentials using official Wazuh instructions.
5. Install the matching Wazuh Windows agent on the lab host, configure its manager address, and start `WazuhSvc`.
6. Confirm the endpoint is **Active** in the Wazuh Dashboard.
7. Investigate existing Wazuh alerts and read-only local Windows logs.

Do not use the default passwords for an exposed deployment. Avoid binding lab services to public-facing networks.

## Read-only verification commands (Windows PowerShell)

```powershell
# Check locally installed agent service
Get-Service WazuhSvc

# Test reachability to a private manager IP (replace placeholder)
Test-NetConnection <WAZUH_HOST_ONLY_IP> -Port 1514
Test-NetConnection <WAZUH_HOST_ONLY_IP> -Port 1515

# View Windows Security failed-logon records
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id = 4625
    StartTime = (Get-Date).AddDays(-7)
} -ErrorAction SilentlyContinue |
    Select-Object -First 10 TimeCreated, Id

# Read recent service configuration/installation activity
Get-WinEvent -FilterHashtable @{
    LogName = 'System'
    Id = 7040, 7045
    StartTime = (Get-Date).AddDays(-1)
} -ErrorAction SilentlyContinue |
    Select-Object -First 30 TimeCreated, Id, Message
```

## Practical limitations

- The lab monitors a single endpoint, not an enterprise estate.
- Previous events may appear in local Windows logs even if they predate Wazuh enrollment.
- A Wazuh rule hit does not necessarily establish an incident.
- System time and displayed dashboard timestamps should be checked before making exact cross-system correlations.
- The lab's initial HTTPS self-signed certificate warning is a trust-configuration issue, not evidence that it is safe to ignore warnings on unrelated sites.
