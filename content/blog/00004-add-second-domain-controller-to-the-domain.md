---
title: "Add a second domain controller to the domain"
slug: "add-second-domain-controller-to-the-domain"
date: "2025-03-02T21:00:00"
author: "Kevin Benningshof"
summary: "Active Directory is a directory service that runs on Microsoft Windows Server. It is used for identity and access management. But what if that one server goes down? To prevent this issue, we can create, configure the second domain controller to provide fault tolerance."
tags: [
    "Microsoft",
    "windows Server",
    "Active Directory"
]
categories: [ "blog" ]
thumbnail: "/icons/windows-server-2022.png"
toc: true
draft: false
featured: true
---

## Introduction ##
A second domain controller (DC) is a cornerstone of a resilient and efficient network infrastructure. By adding this redundancy, organizations ensure business continuity, improve load distribution, and safeguard against unforeseen failures. In this blog, you will learn how to add a second Domain Controller to the an existing domain.

## Active Directory Domain Services ##

### Preperation ###
When this is the first article you read, i will recommend you to preform the following blogs.
This will make sure, that the domain controller has been supplied with a baseline regarding the installation and configuration.

For this, you can follow the blogs on for more information

* [How to install Windows Server](/blog/2025/02/24/how-to-install-windows-server/)
* [Preperation of Windows Server](/blog/2025/03/01/preperation-of-windows-server/)

### Changing the DNS ###

To make sure, the server can be joined to the domain. We will need to change the Preferred DNS Server to IP addres of the main domain controller.
1. Open **Control Panel**, click on **Network and Sharing Center** and then on the connection **Ethernet**
![](/images/00004-add-second-domain-controller-to-the-domain/change-ip-address-01.png)
2. Within **Ethernet Status**, click on **Details..**
3. Within Networking, select **Internet Protocol Version 4 (TCP/IPv4)** and click on **Properties**
4. Within **Internet Protocol Version 4 (TCP/IPv4)**, change the Preferred DNS Server to the IP address of the main domain controller and click on **OK** to apply the settings.
![](/images/00004-add-second-domain-controller-to-the-domain/change-ip-address-02.png)

### Adding the server to the domain ###

1. Open the **File Explorer**, rightclick on **This PC** and then on **Properties**.
![](/images/00004-add-second-domain-controller-to-the-domain/add-server-to-domain-01.png)
2. Within About, click on Advanced Settings.
3. On **System Properties**, click on **Change**
4. On **Computer Name/Domain Changes**, select **Domain** within **Member of** and enter the domain name.
![](/images/00004-add-second-domain-controller-to-the-domain/add-server-to-domain-02.png)
5. You will now be prompted to enter your credentials of an account with the correct permissions and click on **OK**. In this case the account **CORP\Administrator** will be appropriate.
![](/images/00004-add-second-domain-controller-to-the-domain/add-server-to-domain-03.png)
6. When the server succesfully joined the domain, it will welcome you to the domain. Click on **OK** to proceed and restart the server to complete the process.
![](/images/00004-add-second-domain-controller-to-the-domain/add-server-to-domain-04.png)

### Installation of Active Directory Domain Services (AD DS) ###
Now that the server has been added to the domain, we are able to install Active Directory Domain Services (AD DS) using the following steps.

1. Open **Server Manager**, click on **Manager** and then **Add Roles and Features**
![](/images/00004-add-second-domain-controller-to-the-domain/install-second-domain-controller-01.png)
2. On **Before you begin**, click on **Next**
![](/images/00004-add-second-domain-controller-to-the-domain/install-second-domain-controller-02.png)
3. On **Select installation type**, click on **Next**
![](/images/00004-add-second-domain-controller-to-the-domain/install-second-domain-controller-03.png)
4. On **Select destination server**, click on **Next**
![](/images/00004-add-second-domain-controller-to-the-domain/install-second-domain-controller-04.png)
5. On **Select server role**, enable **Active Directory Domain Services**
6. Click on **Add Features**, to make sure these are installed.
![](/images/00004-add-second-domain-controller-to-the-domain/install-second-domain-controller-05.png)
7. On **Select server role**, enable **DNS Server**.
8. Click on **Add Features** and the on **Next**
![](/images/00004-add-second-domain-controller-to-the-domain/install-second-domain-controller-06.png)
9. On **Select features**, click on **Next**
![](/images/00004-add-second-domain-controller-to-the-domain/install-second-domain-controller-07.png)
10. On **Active Directory Domain Services**, click on **Next**
![](/images/00004-add-second-domain-controller-to-the-domain/install-second-domain-controller-08.png)
11. On **DNS Server**, click on **Next**
![](/images/00004-add-second-domain-controller-to-the-domain/install-second-domain-controller-09.png)
12. On **Confirmation installation selections**, enable **Restart the destination server automatically if required** and click on **Install**
![](/images/00004-add-second-domain-controller-to-the-domain/install-second-domain-controller-10.png)

### Promoting to Domain Controller ###
1. Click on **Promote this server to a domain controller**.
![](/images/00004-add-second-domain-controller-to-the-domain/promote-second-domain-controller-01.png)
2. Click on Change and enter the username and password of the administrator account within the domain and click on **OK** and then on **Next**.
![](/images/00004-add-second-domain-controller-to-the-domain/promote-second-domain-controller-02.png)
3. Change the **Site Name** that is required (because this hasn't been changed on the first domain controller it will have the name Default-First-Site-Name) and enter a password for **Directory Services Restore Mode (DSRM)** and click on **Next**
![](/images/00004-add-second-domain-controller-to-the-domain/promote-second-domain-controller-03.png)
4. On **DNS Options**, click on **Next**
![](/images/00004-add-second-domain-controller-to-the-domain/promote-second-domain-controller-04.png)
5. On **Additional Options**, select the main domain controller on **Replicate from** (in this case msvs-addc-22-01) and click on **Next**.
![](/images/00004-add-second-domain-controller-to-the-domain/promote-second-domain-controller-05.png)
6. On **Paths**, click on **Next**
![](/images/00004-add-second-domain-controller-to-the-domain/promote-second-domain-controller-06.png)
7. On **Review Options**, you can view the configuration that can also be executed through 
![](/images/00004-add-second-domain-controller-to-the-domain/promote-second-domain-controller-07.png)
PowerShell through **View script**.
{{% notice tip "PowerShell" %}}
The installation and configuration of the domain, can also be executed through PowerShell with the following script.  
```pwsh
    #
    # Windows PowerShell script for AD DS Deployment
    #

    Import-Module ADDSDeployment
    Install-ADDSDomainController `
    -NoGlobalCatalog:$false `
    -CreateDnsDelegation:$false `
    -Credential (Get-Credential) `
    -CriticalReplicationOnly:false `
    -DatabasePath "C:\Windows\NTDS" `
    -DomainName "benningshof-it.edu" `
    -InstallDns:$true `
    -LogPath "C:\Windows\NTDS" `
    -NoRebootOnCompletion:$false `
    -ReplicationSourceDC "msvs-addc-22-01.benningshof-it.edu" `
    -SiteName "Default-First-Site-Name" `
    -SysvolPath "C:\Windows\SYSVOL" `    
    -Force:$true
```
{{% /notice %}}
8. On **Prerequisites Check**, click on **Install** when the **prerequisite checks has passed succesfully**.
![](/images/00004-add-second-domain-controller-to-the-domain/promote-second-domain-controller-09.png)
9. During this step, you will see that the installation is in progress. This will take a few minutes to take place and complete.
![](/images/00004-add-second-domain-controller-to-the-domain/promote-second-domain-controller-10.png)
10. When the installation, promotion and replication operation has been completed, the server will be rebooted automatically within a minute.
![](/images/00004-add-second-domain-controller-to-the-domain/promote-second-domain-controller-11.png)

## Correcting the Domain Controllers ##

### Change primary and secondairy DNS addresses ###

When the domain controller has been installed and it has been rebooted, it will provide itself with a new DNS server that can be contacted for DNS requests. When this is done, with the second domain controller it will be set to 127.0.0.1 (forcing the communication with itself)

This will not be the best way and will cause problems in the future. So for this, the best thing we can do is assign the domain controllers to target eachother (as preffered DNS server) and if one of them is not available to communicate to the other, then it has to access his own DNS register.
![](/images/00004-add-second-domain-controller-to-the-domain/ip-setting-domain-controllers-01.png)

X                     | msvs-addc-22-01 | msvs-addc-22-02
----------------------|-----------------|----------------
Preferred DNS server: | 10.11.2.2       | 10.11.2.1
Alternate DNS server: |127.0.0.1        | 127.0.0.1

### Checking Active Directory replication ###

If everything goes like it should, the replication of the domain controllers will be running smoothly. to check this, we can preform the following steps to confirm the replication is being executed like it should.

First, we will need to see what the current replication status is. This can be done with the following command.

{{< highlight html >}}
    PS C:\Users\Administrator> repadmin /showrepl *

    Repadmin: running command /showrepl against full DC msvs-addc-22-01.benningshof-it.edu
    Default-First-Site-Name\MSVS-ADDC-22-01
    DSA Options: IS_GC
    Site Options: (none)
    DSA object GUID: f060b532-5d65-44f7-ac42-c92f605e9031
    DSA invocationID: f060b532-5d65-44f7-ac42-c92f605e9031

    ==== INBOUND NEIGHBORS ======================================

    DC=benningshof-it,DC=edu
        Default-First-Site-Name\MSVS-ADDC-22-02 via RPC
            DSA object GUID: 965a792f-4a19-48af-9268-f9a96c7672ae
            Last attempt @ 2025-03-06 20:48:55 was successful.

    CN=Configuration,DC=benningshof-it,DC=edu
        Default-First-Site-Name\MSVS-ADDC-22-02 via RPC
            DSA object GUID: 965a792f-4a19-48af-9268-f9a96c7672ae
            Last attempt @ 2025-03-06 20:48:55 was successful.

    CN=Schema,CN=Configuration,DC=benningshof-it,DC=edu
        Default-First-Site-Name\MSVS-ADDC-22-02 via RPC
            DSA object GUID: 965a792f-4a19-48af-9268-f9a96c7672ae
            Last attempt @ 2025-03-06 20:48:55 was successful.

    DC=DomainDnsZones,DC=benningshof-it,DC=edu
        Default-First-Site-Name\MSVS-ADDC-22-02 via RPC
            DSA object GUID: 965a792f-4a19-48af-9268-f9a96c7672ae
            Last attempt @ 2025-03-06 20:48:55 was successful.

    DC=ForestDnsZones,DC=benningshof-it,DC=edu
        Default-First-Site-Name\MSVS-ADDC-22-02 via RPC
            DSA object GUID: 965a792f-4a19-48af-9268-f9a96c7672ae
            Last attempt @ 2025-03-06 20:52:56 was successful.

    Repadmin: running command /showrepl against full DC msvs-addc-22-02.benningshof-it.edu
    Default-First-Site-Name\MSVS-ADDC-22-02
    DSA Options: IS_GC
    Site Options: (none)
    DSA object GUID: 965a792f-4a19-48af-9268-f9a96c7672ae
    DSA invocationID: 804c9404-591f-4ae7-b7d6-1010cb8d4c34

    ==== INBOUND NEIGHBORS ======================================

    DC=benningshof-it,DC=edu
        Default-First-Site-Name\MSVS-ADDC-22-01 via RPC
            DSA object GUID: f060b532-5d65-44f7-ac42-c92f605e9031
            Last attempt @ 2025-03-06 21:09:29 was successful.

    CN=Configuration,DC=benningshof-it,DC=edu
        Default-First-Site-Name\MSVS-ADDC-22-01 via RPC
            DSA object GUID: f060b532-5d65-44f7-ac42-c92f605e9031
            Last attempt @ 2025-03-06 20:56:07 was successful.

    CN=Schema,CN=Configuration,DC=benningshof-it,DC=edu
        Default-First-Site-Name\MSVS-ADDC-22-01 via RPC
            DSA object GUID: f060b532-5d65-44f7-ac42-c92f605e9031
            Last attempt @ 2025-03-06 20:56:07 was successful.

    DC=DomainDnsZones,DC=benningshof-it,DC=edu
        Default-First-Site-Name\MSVS-ADDC-22-01 via RPC
            DSA object GUID: f060b532-5d65-44f7-ac42-c92f605e9031
            Last attempt @ 2025-03-06 20:56:07 was successful.

    DC=ForestDnsZones,DC=benningshof-it,DC=edu
        Default-First-Site-Name\MSVS-ADDC-22-01 via RPC
            DSA object GUID: f060b532-5d65-44f7-ac42-c92f605e9031
            Last attempt @ 2025-03-06 20:56:07 was successful.

{{< /highlight >}}

To check if there are any replication requests queued, this can be seen through the command.

{{< highlight html >}}
    PS C:\Users\Administrator> repadmin /queue

    Repadmin: running command /queue against full DC localhost
    Queue contains 0 items.

{{< /highlight >}}

To check if the current replication status, this can be seen through the command.
{{< highlight html >}}
    PS C:\Users\Administrator> repadmin /replsummary

    Replication Summary Start Time: 2025-03-06 21:12:29

    Beginning data collection for replication summary, this may take awhile:
    .....


    Source DSA          largest delta    fails/total %%   error
    MSVS-ADDC-22-01           16m:22s    0 /   5    0
    MSVS-ADDC-22-02           23m:34s    0 /   5    0


    Destination DSA     largest delta    fails/total %%   error
    MSVS-ADDC-22-01           23m:34s    0 /   5    0
    MSVS-ADDC-22-02           16m:21s    0 /   5    0

{{< /highlight >}}

To force the KCC to recalculate the topology outside of the 15 minutes check if the connection has been established between domain controllers, we can run the following command
{{< highlight html >}}
    PS C:\Users\Administrator> repadmin /replsummary

    Repadmin: running command /kcc against full DC localhost
    Default-First-Site-Name
    Current Site Options: (None)
    Consistancy check on localhost succesful.

{{< /highlight >}}

## Conclusion ##

With this, we have learned how it's possible to create the second domain controller within the domain. This has been installed and configured with the basic settings and will make sure that atleast one of the devices will be online, during for example updates or maintenance. We have also configured the DNS addresses, so they can talk to eachother for the information that is requested and as a fallback retrieve that information from themself.

At the last step, we have checked through repadmin if the replication between the domain controllers has been preforming succesfully without any errors.