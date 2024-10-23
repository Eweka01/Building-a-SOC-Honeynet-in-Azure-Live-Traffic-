# Building a SOC + Honeynet in Azure (Live Traffic)
![68747470733a2f2f692e696d6775722e636f6d2f5a5778653033652e6a7067](https://github.com/user-attachments/assets/9ae2b82f-4fd4-49f2-8015-43c04078355a)


## Introduction

In this project, I created a small honeynet within Azure, directing log sources from various resources into a Log Analytics workspace. This workspace was then utilized by Microsoft Sentinel to develop attack maps, trigger alerts, and generate incidents. I monitored security metrics in the unsecured environment for 24 hours, implemented security controls to strengthen the setup, and re-measured the metrics over the next 24 hours. The results are presented below, showcasing the following metrics:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Architecture Before Hardening / Security Controls
![2](https://github.com/user-attachments/assets/9bdeff84-a993-403f-8a9e-539b407cc939)


## Architecture After Hardening / Security Controls
![3](https://github.com/user-attachments/assets/e20aafab-be59-4631-847a-ff6dc29f3bef)


The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

## Attack Maps Before Hardening / Security Controls
![4](https://github.com/user-attachments/assets/95e683b3-3651-449c-a5a7-7e41e2113745)<br>
![5](https://github.com/user-attachments/assets/9d57503a-206b-478f-983d-dba62baf31a2)<br>
![6](https://github.com/user-attachments/assets/f91fe16d-a5b4-428c-8a14-5b0b2194b99d)<br>

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-03-15 17:04:29
Stop Time 2023-03-16 17:04:29

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 19470
| Syslog                   | 3028
| SecurityAlert            | 10
| SecurityIncident         | 348
| AzureNetworkAnalytics_CL | 843

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2024-09-18 15:37
Stop Time	2024-09-19 15:37

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 8778
| Syslog                   | 25
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
