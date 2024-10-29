# Building a SOC + Honeynet in Azure [Live Traffic]
![fris](https://github.com/user-attachments/assets/a976e65e-17f3-414f-9426-67c10f4973c6)


## Introduction

In this project, I created a small honeynet within Azure, directing log sources from various resources into a Log Analytics workspace. This workspace was then utilized by Microsoft Sentinel to develop attack maps, trigger alerts, and generate incidents. I monitored security metrics in the unsecured environment for 24 hours, implemented security controls to strengthen the setup, and re-measured the metrics over the next 24 hours. The results are presented below, showcasing the following metrics:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Technologies, Regulations, and Azure Components Employed:

- Azure Virtual Network (VNet)
- Azure Network Security Group (NSG)
- Virtual Machines (2x Windows, 1x Linux)
- Log Analytics Workspace with Kusto Query Language (KQL) Queries
- Azure Key Vault for Secure Secrets Management
- Azure Storage Account for Data Storage
- Microsoft Sentinel for Security Information and Event Management (SIEM)
- Microsoft Defender for Cloud to Protect Cloud Resources
- Windows Remote Desktop for Remote Access
- Command Line Interface (CLI) for System Management
- PowerShell for Automation and Configuration Management
- NIST SP 800-53 Revision 5 for Security Controls
- NIST SP 800-61 Revision 2 for Incident Handling Guidance

## Methodology
- Creating the honeynet: Initially, I deployed multiple vulnerable virtual machines in Azure to simulate an insecure environment.

- Monitoring and analysis: Azure was configured to collect log data from various resources into a log analytics workspace. Using Microsoft Sentinel, I constructed attack maps, triggered alerts, and managed incidents based on the gathered information.

- Security metrics assessment: Over a 4-hour period, I monitored the environment while it remained insecure, capturing essential security metrics. This served as a benchmark for comparison post-implementation of remedial actions.

- Incident response and remediation: Following the identification of vulnerabilities and resolution of incidents, I initiated the hardening process by implementing security best practices and adhering to Azure-specific recommendations.

- Post-remediation evaluation: Subsequently, I conducted another 4-hour observation of the environment to reassess security metrics, comparing them against the initial baseline.

## Architecture Before Hardening / Security Controls
![bf](https://github.com/user-attachments/assets/6d0c1a77-f61b-41e6-888c-f5ad9197474f)

In Azure, I set up a resource group and deployed both a Windows and a Linux virtual machine. To create a vulnerable environment, I disabled the Windows Firewall via Remote Desktop Protocol (RDP) on the Windows VM. I also altered the network security group (NSG) linked to the Windows VM, enabling all inbound traffic by using the wildcard rule *. Similarly, I configured the NSG for the Linux VM to allow unrestricted inbound traffic through the wildcard *.

During the "BEFORE" phase of the project, all resources were deployed with open internet exposure. This intentionally insecure configuration aimed to attract potential cyber attackers for observation of their tactics. Both Virtual Machines had their NSGs and internal firewalls configured to allow unrestricted access from any source. Additionally, other resources, such as storage accounts and databases, were exposed with public endpoints, lacking the added security of Private Endpoints.


## Architecture After Hardening / Security Controls
![af](https://github.com/user-attachments/assets/35adefc3-7d65-419e-8414-2300ad2b5a4e)

To strengthen our architecture, I implemented a comprehensive suite of hardening measures and security controls aimed at improving the security posture of the environment. These efforts included:

- **Network Security Groups (NSGs):** I refined NSGs to allow inbound and outbound traffic only from my designated public IP address, ensuring that only authorized traffic could access the virtual machines.

- **Built-in Firewalls:** I configured firewalls specific to each VM to restrict unauthorized access, creating rules to minimize attack vectors and protect critical resources.

- **Private Endpoints:** I replaced public endpoints with Private Endpoints for Azure resources, such as storage accounts and databases. This change limited access to the virtual network, securing sensitive resources against external threats and unauthorized access.

By comparing pre- and post-implementation security metrics, I was able to demonstrate the effectiveness of these measures in enhancing the Azure environment's overall security posture.

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

# Metrics Before Hardening / Security Controls
The following table shows the metrics we measured in our insecure environment for 4 hours plus:

| Start Time 2024-10-23  6:00:47
| Stop Time 2024-10-23 11:00:55

| Metric                    | Count |
|---------------------------|-------|
| SecurityEvent             | 1988  |
| Syslog                    | 11    |
| SecurityAlert             | 22    |
| SecurityIncident          | 23    |
| AzureNetworkAnalytics_CL  | 2190  |

# Attack Maps After Hardening / Security Controls
All map queries actually returned no results due to no instances of malicious activity for the 4 hour period after hardening.

Metrics After Hardening / Security Controls
The following table shows the metrics we measured in our environment for another 4 hours, but after we have applied security controls:

| Start Time 2024-10-23 15:09:28
| Stop Time 2024-10-23 19:20:20

| Metric                    | Count |
|---------------------------|-------|
| SecurityEvent             | 3894  |
| Syslog                    | 6     |
| SecurityAlert             | 0     |
| SecurityIncident          | 0     |
| AzureNetworkAnalytics_CL  | 0     |

![result](https://github.com/user-attachments/assets/e7e47e8b-5698-41fb-84dd-8e73ad58dcff)

## KQL Queries

| Metric                                  | Query                                                                                                                                                       |
|-----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Start/Stop Time                         | range x from 1 to 1 step 1 <br> \| project StartTime = ago(8h), StopTime = now()                                                                            |
| Security Events (Windows VMs)           | SecurityEvent <br> \| where TimeGenerated >= ago(8h) <br> \| count                                                                                           |
| Syslog (Linux VMs)                      | Syslog <br> \| where TimeGenerated >= ago(8h) <br> \| count                                                                                                  |
| SecurityAlert (Microsoft Defender)      | Security Alert <br> \| where DisplayName !startswith "CUSTOM" and DisplayName !startswith "TEST" <br> \| where TimeGenerated >= ago(8h) <br> \| count       |
| Security Incident (Sentinel Incidents)  | SecurityIncident <br> \| where TimeGenerated >= ago(8h) <br> \| count                                                                                        |
| NSG Inbound Malicious Flows Allowed     | AzureNetworkAnalytics_CL <br> \| where FlowType_s == "MaliciousFlow" and AllowedInFlows_d > 0 <br> \| where TimeGenerated >= ago(8h) <br> \| count           |


## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.

Signed by --Osamudiamen Eweka--
