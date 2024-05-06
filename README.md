# Building a SOC + Honeynet in Azure (Live Traffic)


![Untitled drawing (1)](https://github.com/timthecyberguy/Azure-cloud-soc-project/assets/16265352/45330231-4d27-4fb0-bacc-5f04ebb402cb)


## Introduction

In this project, I build a mini honeynet in Azure and ingest log sources from various resources into a Log Analytics workspace, which is then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. I measured some security metrics in the insecure environment for 24 hours, apply some security controls to harden the environment, measure metrics for another 24 hours, then show the results below. The metrics we will show are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)


## Azure Components Deployed for Cloud SOC & Honeynet

- Azure Virtual Network (VNet)
- Azure Network Security Group (NSG)
- Virtual Machines (2 Windows VM, 1 Linux VM)
- Azure Storage Account for Data Storage
- Log Analytics Workspace
- Kusto Query Language for querying ingested logs 
- Azure Key Vault 
- Windows Remote Desktop for Remote Access
- Windows Event Viewer
- SQL Server installed on Windows VM
- Command Line Interface (CLI) for System Management
- NIST SP 800-61 Revision 2 for Incident Handling Guidance
- NIST SP 800-53 Revision 5 for Security Controls
- NIST SP 800-61 Revision 2 for Incident Handling Guidance
- Microsoft Defender for Cloud to Protect Cloud Resources
- Microsoft Sentinel (SIEM)


## Architecture Before Hardening / Security Controls

![Untitled drawing (2)](https://github.com/timthecyberguy/Azure-cloud-soc-project/assets/16265352/d2ac45ee-7ef4-47bc-bb75-beb3b2baa900)


In the initial stage of the project, all resources were deliberately deployed with high internet exposure for the "BEFORE" metrics. This entailed mis-configuring  NSGs and built-in firewalls allowing all traffic, alongside other resources equipped with public endpoints accessible from the internet. Private Endpoints were not utilized during this phase. 

The objective was to create a virtual environment openly exposed to the internet, facilitating the observation of threat actors' attack patterns as they attempted to breach vulnerable systems. This involved deploying a Windows VM hosting a SQL database and a Linux server, both with NSG configurations set to "Allow All." Additionally, a storage account and key vault were deployed with publicly accessible endpoints. Throughout this phase, Microsoft Sentinel monitored the unsecured environment, leveraging logs aggregated by the Log Analytics workspace.

## Attack Maps Before Hardening / Security Controls

<h3>Mis-configured NSG Allowed Malicious Inbound Traffic </h3>  This attack map shows the traffic allowed by a Network Security Group with all traffic allowed inbound

![NSG Malicious traffic map](https://github.com/timthecyberguy/Azure-cloud-soc-project/assets/16265352/461f8d5a-4b7d-4f51-98af-ff8e049ea16d)<br>



<h3>Linux SSH Authentication Failures </h3>  The generated attack map illustrates the attempts by threat actors trying to gain remote access to the Linux virtual machine via SSH

![linux ssh auth fail map](https://github.com/timthecyberguy/Azure-cloud-soc-project/assets/16265352/b1921fc5-80dd-49ae-bdfa-c1b187eceaff)<br>


<h3>MSSQL Authentication Failures </h3>   This attack map shows all the attempts threat actors trying to access the Microsoft SQL Database Server within the Windows virtual machine

![MSSQL auth fail map](https://github.com/timthecyberguy/Azure-cloud-soc-project/assets/16265352/9bbe12a7-bdc8-403b-885f-347271a61fec)<br>


<h3>RDP Authentication Failures </h3>   This attack map reveals multiple instances of failed RDP and SMB connections, which highlights the ongoing efforts of threat actors to gain remote access to windows VM instance.

![windows rdp auth fail map](https://github.com/timthecyberguy/Azure-cloud-soc-project/assets/16265352/75f85eef-5f57-4dfc-bbf1-1e76321d9f98)<br>



## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:<br>
Start Time - 4/28/2024, 10:59:15 PM<br>
Stop Time - 4/29/2024, 10:56:15 PM<br>

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 74945
| Syslog                   | 4433
| SecurityAlert            | 16
| SecurityIncident         | 85
| AzureNetworkAnalytics_CL | 3205


## Approach to Security Posture Improvement

![Untitled drawing (4)](https://github.com/timthecyberguy/Azure-cloud-soc-project/assets/16265352/e0ee867d-7e1b-40cd-9dfa-099fe393612d)

To fortify the environment's overall security posture, a series of hardening measures and security controls were introduced:

<b>Built-in Firewalls</b><br>
Built-in firewalls were configured on the virtual machines to restrict access and safeguard resources against unauthorized connections.
This measure was instrumental in controlling inbound and outbound traffic, thereby reducing the attack surface.

<b>Network Security Groups (NSGs)</b>

Network Security Groups (NSGs) were strengthened by configuring rules to block all incoming and outgoing traffic, except for traffic originating from designated public IP addresses.
By implementing stringent access controls, the risk of unauthorized access was significantly mitigated.

<b>Private Endpoints</b>

Public endpoints were replaced with Private Endpoints to fortify the security of Azure resources.
This measure ensured that sensitive resources, such as databases and storage accounts, could only be accessed from within the virtual network, enhancing protection against external threats.

<b>Compliance Measures</b>

During the initial 24-hour study, several vulnerabilities were identified, primarily stemming from exposure to the public internet. To address these vulnerabilities and ensure compliance with industry standards:

- NIST 800-53 r4 Compliance
NIST 800-53 r4 compliance standards were activated within Microsoft Defender's compliance section to align with regulatory requirements.
Efforts were directed towards meeting the compliance standards outlined in NIST 800-53 r4, particularly focusing on Security Control SC-7 (Boundary Protection).
- Evaluation and Implementation of SC-7
Additional evaluations were conducted for Security Control SC-7, which pertains to boundary protection.
Measures were taken to enhance boundary protection mechanisms, thereby reducing the risk of unauthorized access and data breaches.

The implementation of these security enhancements and compliance measures signifies a proactive approach towards safeguarding the virtual network. By fortifying access controls, restricting unauthorized access, and ensuring compliance with industry standards, the virtual network infrastructure is better equipped to withstand potential security threats and protect sensitive assets.


## Attack Maps After Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:<br>
Start Time 5/1/2024 2:47:31 PM<br>
Stop Time	5/2/2024, 2:45:31 PM<br>
| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 1086
| Syslog                   | 35
| SecurityAlert            | 0
| SecurityIncident         | 2
| AzureNetworkAnalytics_CL | 0




## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
