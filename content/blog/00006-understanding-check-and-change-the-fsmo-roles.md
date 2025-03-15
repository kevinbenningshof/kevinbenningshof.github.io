---
title: "Understand, check and change the FSMO roles"
slug: "understanding-check-and-change-the-fsmo-roles"
date: "2025-03-09T21:00:00"
author: "Kevin Benningshof"
summary: "After the installation and configuration of Active Directory Domain Services the Domain Controllers, we are reliant on the communication between them about who is responsible for the FSMO roles."
tags: [
    "Microsoft",
    "windows Server",
    "FSMO"
]
categories: [ "Blog" ]
thumbnail: "/images/blog/00006/windows-server-2022.png"
toc: true
draft: false
featured: true
---

## Introduction
After the installation and configuration of Active Directory Domain Services on one or more domain controllers, we are reliant on the communication between them. With FSMO we can assign certain responsibility to these domain controllers

## What is FSMO
FSMO stands for **Flexible Single Master Operations**. FSMO roles ensure that functioning an consistency of Active Directory accross the Windows Domain by assigning specific tasks to the Domain Controllers and providing single authority for processing changes.

## Which FSMO roles exist
Within Active Directory there are five unique FSMO roles, each role has its own purpose

* **Schema master**
    * This Domain Controller is responsible for processing updates to the directory schema, which are then replicated to all other DCs in the directory. The schema is the blueprint of an Active Directory.
* **Domain naming master**
    * This Domain Controller is responsible for adding and removing domain within the forest. This will ensure that each domain within the forest has a unique name.
* **RID master**
    * This Domain Controller is responsible for processing RID pool requests for all Domain Controllers within the domain. Each user, group and other security principals is assigned a unique security identifier (DIS) when it is created. The RID is in that case unique and when the pool is reached, it will request a new RID pool from the RID master.
* **PDC emulator**
    * Within each domain, there is one Domain Controller that is assigned as PDC emulator. This Domain Controller is responsible for time synchronizing. This is important for password changes, authentication events, account lockouts and authentication failures.
* **Infrastructure master**
    * The Domain Controller assigned as Infrastructure Master Role is reponseble for updating object's SID and distinguished names within a cross-domain object.

## How to check the FSMO roles ##

**Command Prompt**

To retrieve the information, which server has certain responsibility within the domain we can use the following and simple command.

1. Open **Command Prompt** and run the cmdlet 
[**netdom query fsmo**](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc835089(v=ws.11))

{{< highlight html >}}
    C:\Windows\system32>netdom query FSMO
{{< /highlight >}}

3. This command will show which server is responsible for the 5 roles regarding FSMO.

{{< highlight html >}}
    Schema master               msvs-addc-22-01.benningshof-it.edu
    Domain naming master        msvs-addc-22-01.benningshof-it.edu
    PDC                         msvs-addc-22-01.benningshof-it.edu
    RID pool manager            msvs-addc-22-01.benningshof-it.edu
    Infrastructure master       msvs-addc-22-01.benningshof-it.edu

    The command completed successfully.
{{< /highlight >}}

**PowerShell**

The same results, can be found through PowerShell. But this is required to use 2 different commands for the forest and the domain.

To find out which Domain Controller are responsible for **DomainNamingMaster** and **SchemaMaster**, run the cmdlet [Get-ADForest](https://learn.microsoft.com/en-us/powershell/module/activedirectory/get-adforest?view=windowsserver2025-ps)

{{< highlight html >}}
    PS C:\Windows\system32> Get-ADForest | select DomainNamingMaster, SchemaMaster | format-list

    DomainNamingMaster : msvs-addc-22-01.benningshof-it.edu
    SchemaMaster       : msvs-addc-22-01.benningshof-it.edu
{{< /highlight >}}

To find out which of the Domain Controllers is responsible for **PDCEmulator**, **RIDMaster** and **InfrastructureMaster**, run the cmdlet [Get-ADDomain](https://learn.microsoft.com/en-us/powershell/module/activedirectory/get-addomain?view=windowsserver2025-ps)

{{< highlight html >}}
    PS C:\Windows\system32> Get-ADDomain | select PDCEmulator,RIDMaster,InfrastructureMaster | format-list

    PDCEmulator          : msvs-addc-22-01.benningshof-it.edu
    RIDMaster            : msvs-addc-22-01.benningshof-it.edu
    InfrastructureMaster : msvs-addc-22-01.benningshof-it.edu
{{< /highlight >}}

**Windows Server**

Before it's possible to check which server is responsible for the different FSMO rules, we need to register a specific *.DLL file. This will make it possible to that the plugin Active Directory Schema can be used through **Microsoft Management Console (MMC)**

1. Open **PowerShell**
2. Run the following command **regsvr32 schmmgmt.dll** to register Active Directory Schema.
![](/images/00006-understanding-check-and-change-the-fsmo-roles/check-fsmo-through-mmc-01.png)
3. Within **PowerShell**, run the command **mmc.exe**
4. When mmc has been opened, click on **File** and then on **Add/Remove Snap-in..**
![](/images/00006-understanding-check-and-change-the-fsmo-roles/check-fsmo-through-mmc-02.png)
5. Within **Available Snap-ins**, select **Active Directory Schema** and press on  **Add >** and then on **OK**
![](/images/00006-understanding-check-and-change-the-fsmo-roles/check-fsmo-through-mmc-03.png)
6. Right-click on **Active Directory Schema** and select one of the options **Operations Master** to see which Domain Controller is the **Schema Master**.
![](/images/00006-understanding-check-and-change-the-fsmo-roles/check-fsmo-through-mmc-04.png)
7. To check which Domain Controller is the **Operations Master**, open **Active Directory Domains and Trusts** and **right-click** on **Active Directory Domains and Trust** and then click on **Operations Master...** to see the **Domain naming master**.
![](/images/00006-understanding-check-and-change-the-fsmo-roles/check-fsmo-through-mmc-05.png)
8. To check which Domain Controller is responsible as **RID-master**, **PDC-emulator** and **Infrastructure master**, open **Active Directory Users and Computers**, right-click on the domain name and click opn **Operations Masters...**
![](/images/00006-understanding-check-and-change-the-fsmo-roles/check-fsmo-through-mmc-06.png)
9. Within the Operations Masters, you will see the tabs **RID**, **PDC** and **Infrastructure**. Within these you can see which is the **Operations Master**
![](/images/00006-understanding-check-and-change-the-fsmo-roles/check-fsmo-through-mmc-07.png)

## How to change the FSMO roles ##

### Why to transfer the FSMO roles? ###
After the installation of Active Directory Domain Services all the five FSMO roles are assigned to the first Domain Controller within the forest root domain. To preform the transfer of the FSMO roles, there are several reasons why this should be done.

* Upgrading the operating system (*For example: Windows Server 2019 to 2022*)
* Changing the IP addresses on a domain controller
* Demoting a domain controller.
* Taking a domain controller offline for maintenance
* Performance issues

### How to transfer the FSMO roles? ###

**PowerShell**
It's recommended to return to **How to check the FSMO roles**, to confirm which server is the **RID master**, **PDC Emulator**, **Infrastructure master**, **Schema Master** and-/or **Domain naming master**.

Within these steps, the FSMO roles will be transfere from the **msvs-addc-22-01** to the **msvs-addc-22-02**

{{< highlight html >}}
    PS C:\Windows\system32> netdom query FSMO

    Schema master               msvs-addc-22-01.benningshof-it.edu
    Domain naming master        msvs-addc-22-01.benningshof-it.edu
    PDC                         msvs-addc-22-01.benningshof-it.edu
    RID pool manager            msvs-addc-22-01.benningshof-it.edu
    Infrastructure master       msvs-addc-22-01.benningshof-it.edu

    The command completed successfully.
{{< /highlight >}}

**PDC Emulator**
{{< highlight html >}}
    PS C:\Windows\system32> Move-ADDirectoryServerOperationMasterRole -Identity "msvs-addc-22-02" PDCEmulator

    Move Operation Master Role
    Do you want to move role 'PDCEmulator' to server 'msvs-addc-22-02.benningshof-it.edu' ?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"): A
{{< /highlight >}}

**RID Master**
{{< highlight html >}}
    PS C:\Windows\system32> Move-ADDirectoryServerOperationMasterRole -Identity "msvs-addc-22-02" RIDMaster

    Move Operation Master Role
    Do you want to move role 'RIDMaster' to server 'msvs-addc-22-02.benningshof-it.edu' ?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"): A
{{< /highlight >}}

**Infrastructure master**
{{< highlight html >}}
    PS C:\Windows\system32> Move-ADDirectoryServerOperationMasterRole -Identity "msvs-addc-22-02" Infrastructuremaster

    Move Operation Master Role
    Do you want to move role 'InfrastructureMaster' to server 'msvs-addc-22-02.benningshof-it.edu' ?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"): A
{{< /highlight >}}

**Domain naming master**
{{< highlight html >}}
    PS C:\Windows\system32> Move-ADDirectoryServerOperationMasterRole -Identity "msvs-addc-22-02" DomainNamingmaster

    Move Operation Master Role
    Do you want to move role 'DomainNamingMaster' to server 'msvs-addc-22-02.benningshof-it.edu' ?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"): A
{{< /highlight >}}

**Schema Master**
{{< highlight html >}}
    PS C:\Windows\system32> Move-ADDirectoryServerOperationMasterRole -Identity "msvs-addc-22-02" SchemaMaster

    Move Operation Master Role
    Do you want to move role 'SchemaMaster' to server 'msvs-addc-22-02.benningshof-it.edu' ?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"): A
{{< /highlight >}}

Now to preform the check and see if the changes have been preformed, run the following command

{{< highlight html >}}
    PS C:\Windows\system32> netdom query FSMO

    Schema master               msvs-addc-22-02.benningshof-it.edu
    Domain naming master        msvs-addc-22-02.benningshof-it.edu
    PDC                         msvs-addc-22-02.benningshof-it.edu
    RID pool manager            msvs-addc-22-02.benningshof-it.edu
    Infrastructure master       msvs-addc-22-02.benningshof-it.edu

    The command completed successfully.
{{< /highlight >}}

**Windows Server**

{{% notice note "Attention before proceding" %}}
It's important that the these steps are preformed on the server that is recieving the **FSMO roles**. So, if these are moved from **msvs-addc-22-01** to **msvs-addc-22-02** the steps need to be preformed on the **msvs-addc-22-02**.  
{{% /notice %}}

1. Open **mmc.exe**
2. When **mmc.exe** has been opened, click on **File** and then on **Add/Remove Snap-in..**
![](/images/00006-understanding-check-and-change-the-fsmo-roles/change-fsmo-through-mmc-01.png)
3. Within **Available snap-ins**, select **Active Directory Schema** and click on **Add >**
![](/images/00006-understanding-check-and-change-the-fsmo-roles/change-fsmo-through-mmc-02.png)
4. Right-click **Active Directory Schema** and then click on **Operations Master...**
![](/images/00006-understanding-check-and-change-the-fsmo-roles/change-fsmo-through-mmc-03.png)
5. Within **Change Schema Master**, click on **Change**. To confirm the change, click on **Yes**
![](/images/00006-understanding-check-and-change-the-fsmo-roles/change-fsmo-through-mmc-04.png)
6. After the change has been preformed, the verification pop-up will be shown.
![](/images/00006-understanding-check-and-change-the-fsmo-roles/change-fsmo-through-mmc-05.png)
7. To verify the change, you can see that the current schema master (online) has been changed from **msvs-addc-22-02** to **msvs-addc-22-01**.
![](/images/00006-understanding-check-and-change-the-fsmo-roles/change-fsmo-through-mmc-06.png)
8. To preform the change for **Domain naming master**, open **Active Directory Domains and Trusts**, **Right-click** on Active Directory Domains and Trusts and then click on **Operations Master...**
![](/images/00006-understanding-check-and-change-the-fsmo-roles/change-fsmo-through-mmc-07.png)
9. Within **Operations Master**, click on **Change...**. To confirm, click on **YES**
![](/images/00006-understanding-check-and-change-the-fsmo-roles/change-fsmo-through-mmc-08.png)
10. As conformation, we can see that the Domain naming master has been changed from **msvs-addc-22-02** to **msvs-addc-22-01**.
![](/images/00006-understanding-check-and-change-the-fsmo-roles/change-fsmo-through-mmc-09.png)
11. Open **Active Directory Users and Computers**, right-click on the **Domain Name** and then on **Operations Master...**
![](/images/00006-understanding-check-and-change-the-fsmo-roles/change-fsmo-through-mmc-10.png)
12. On the tabs **RID**, **PDC** and **Infrastructure**, click on **Change...**. To apply the settings, click on **YES**
![](/images/00006-understanding-check-and-change-the-fsmo-roles/change-fsmo-through-mmc-11.png)
13. When the change has been preformed, click on **OK** within the **Active Directory Domain Services** pop-up.
![](/images/00006-understanding-check-and-change-the-fsmo-roles/change-fsmo-through-mmc-12.png)

## Conclusion ##

Within this blog, we have explained the meaning meaning of FSMO roles within the domain and forest, how to preform the check which server has this responsibility and how it's possible to change the Master of these roles. Because this is necessary and not a daily repeating task, it's important to share this information with required steps that should help you when it becomes the time to move these roles.