---
title: "Installation of Active Directory Domain Services"
slug: "installation-preperation-of-active-directory-domain-services"
date: "2025-02-22T21:00:00"
author: "Kevin Benningshof"
summary: "Active Directory is a directory service that runs on Microsoft Windows Server. It is used for identity and access management. AD DS stores and organizes information about the people, devices and services connected to a network."
tags: [
    "Microsoft",
    "windows Server",
    "Active Directory"
]
categories: [ "Blog" ]
thumbnail: "/images/blog/00003/windows-server-2022.png"
toc: true
draft: false
featured: true
---

# Introduction
What is Active Directory Domain Services?

Active Directory Domain Services (AD DS) is a server role in Active Directory that allows admins to manage and store information about resources from a network, as well as application data, in a distributed database.

AD DS helps admins manage network elements, both computing devices and users and reorder them into a custom hierarchical structure. AD DS also integrates security by authenticating logons and controlling access to directory resources.

This blog will show you how AD DS (Active Directory Domain Services) can be installed and promoted to Domain Controller.

## Active Directory Domain Services ##

### Preperation ###
When this is the first article you read, i will recommend you to preform the following blogs.
This will make sure, that the domain controller has been supplied with a baseline regarding the installation and configuration.

For this, you can follow the blogs on 

* [How to install Windows Server](/blog/2025/02/24/how-to-install-windows-server/)
* [Preperation of Windows Server](/blog/2025/03/01/preperation-of-windows-server/)


### Installation of Active Directory (AD DS) ###

1. Open **Server Manager**, go to the **Dashboard** on the left side.
2. After selecting the **Dashboard**, click on **Manage** and then on **Add Roles and Features**.
![](/images/00003-installation-of-active-directory/installing-active-directory-01.png)
3. On **Before you begin**, click on **Next**
![](/images/00003-installation-of-active-directory/installing-active-directory-02.png)
4. On **Select installation type**, click on **Next**
![](/images/00003-installation-of-active-directory/installing-active-directory-03.png)
5. On **Select destination server**, click on **Next**
![](/images/00003-installation-of-active-directory/installing-active-directory-04.png)
6. On **Select server role**, enable **Active Directory Domain Services**
![](/images/00003-installation-of-active-directory/installing-active-directory-05.png)
7. Click on **Add Features**, to make sure these are installed.
![](/images/00003-installation-of-active-directory/installing-active-directory-06.png)
8. On **Select server role**, enable **DNS Server**.
![](/images/00003-installation-of-active-directory/installing-active-directory-07.png)
9. Click on **Add Features** and the on **Next**
![](/images/00003-installation-of-active-directory/installing-active-directory-08.png)
10. On **Select Features**, click on **Next**
![](/images/00003-installation-of-active-directory/installing-active-directory-09.png)
11. On **Active Directory Domain Services (AD DS)**, click on **Next**
![](/images/00003-installation-of-active-directory/installing-active-directory-10.png)
12. On **DNS Server**, click on **Next**
![](/images/00003-installation-of-active-directory/installing-active-directory-11.png)
13. On **Confirmation installation selections**, enable **Restart the destination server automatically if required** and click on **Install**
![](/images/00003-installation-of-active-directory/installing-active-directory-12.png)
14. The installation of **Active Directory Domain Services** & **DNS** wil start.
![](/images/00003-installation-of-active-directory/installing-active-directory-13.png)

### Promoting to Domain Controller ###

Within the previous steps, we have installed the Active Directory Domain Services and DNS features. Now that these are installed, we can continue with the configuration. 

1. To start the configuration, click on **Promote this server to a domain controller**
![](/images/00003-installation-of-active-directory/promoting-domain-controller-01.png)
2. Select **Add a new forest** and enter the **Root Domain Name** and click on **Next**
![](/images/00003-installation-of-active-directory/promoting-domain-controller-02.png)
3. On **DNS Options**, click on **Next**
![](/images/00003-installation-of-active-directory/promoting-domain-controller-04.png)
4. On Additional Options, enter the **NetBIOS domain name**
![](/images/00003-installation-of-active-directory/promoting-domain-controller-05.png)
5. On **Paths**, click on **Next**
![](/images/00003-installation-of-active-directory/promoting-domain-controller-06.png)
6. On **Review options**, you can view the configuration that can also be executed through PowerShell through **View script**
![](/images/00003-installation-of-active-directory/promoting-domain-controller-07.png)
{{% notice tip "PowerShell" %}}
The installation and configuration of the domain, can also be executed through PowerShell with the following script.  
```pwsh
    #
    # Windows PowerShell script for AD DS Deployment
    #

    Import-Module ADDSDeployment
    Install-ADDSForest `
    -CreateDnsDelegation:$false `
    -DatabasePath "C:\Windows\NTDS" `
    -DomainMode "WinThreshold" `
    -DomainName "benningshof-it.edu" `
    -DomainNetbiosName "CORP" `
    -ForestMode " "WinThreshold"" `
    -InstallDns:$true `
    -LogPath "C:\Windows\NTDS" `
    -NoRebootOnCompletion:$false `
    -SysvolPath "C:\Windows\SYSVOL" `
    -Force:$true
```
{{% /notice %}}
7. On **Prerequisites check**, click on **Install** when it passed the check succesfully.
![](/images/00003-installation-of-active-directory/promoting-domain-controller-09.png)
8. The installation has been started and this will take a few minutes to complete.
![](/images/00003-installation-of-active-directory/promoting-domain-controller-10.png)
9. When the promotion has been completed, the server will automatically reboot after several minutes.
![](/images/00003-installation-of-active-directory/promoting-domain-controller-11.png)
10. After the reboot, you can see that it's possible to sign-in with the Administator account.
![](/images/00003-installation-of-active-directory/promoting-domain-controller-12.png)

## Conclusion ##
Within this blog, i've shown you the steps that are needed to install and configure Active Directory Domain Services to create the first domain controller.