---
title: "Installation and configuration of Windows Server Update Services"
slug: "installation-and-configuration-of-windows-server-update-services"
date: "2025-04-12T20:00:00"
author: "Kevin Benningshof"
summary: "Within the infrastructure of the company you are working for, there are several servers that need to be patched with the latest updates of Microsoft. This is a situation that you don't want to preform manually. We can use WSUS to fully manage the distribution of updates that are released through Microsoft Update to computers on your network from a central source."
tags: [
    "Microsoft",
    "windows Server",
    "Windows Server Update Services"
]
categories: [ "blog" ]
thumbnail: "/images/blog/00010/windows-server-2022.png"
toc: true
draft: false
featured: true
---

# Introduction
Within the infrastructure of the company you are working for, there are several servers that need to be patched with the latest updates of Microsoft. This is a situation that you don't want to preform manually. We can use WSUS to fully manage the distribution of updates that are released through Microsoft Update to servers on your network from a central source. Within this blog, we will preform the installation, configuration of the server role, setup a Group Policy and deploy the latest patch for Windows Server 2022. 

## 1. Installation of Windows Server Update Services
1. Open **Server Manager**, click on **Manage** and then on **Add Roles and Features**.
2. On **Before you begin**, click on **Next**.
![](/images/00010-windows-server-update-services/install-wsus-role-01.png)
3. On **Installation type**, select **Role-based or feature-based installation** and click on **Next**
![](/images/00010-windows-server-update-services/install-wsus-role-02.png)
4. On **Server Selection**, select the server that is listed that needs to install the WSUS role and click on **Next**
![](/images/00010-windows-server-update-services/install-wsus-role-03.png)
5. On **Server Roles**, enable **Windows Server Update Services** (this will show the popup for the missing features) and click on **Add Features** to enable the pre-requisite **Roles and Features** for WSUS and click on **Next**
![](/images/00010-windows-server-update-services/install-wsus-role-04.png)
6. On **Features** there are no further selections required, click on **Next**
![](/images/00010-windows-server-update-services/install-wsus-role-07.png)
7. On **Windows Server Update Services**, click on **Next**
![](/images/00010-windows-server-update-services/install-wsus-role-08.png)
8. On **Role Services**, make sure that the following options are enabled and click on **Next**
* WID Connectivity (Windows Internal Database)
* WSUS Services
![](/images/00010-windows-server-update-services/install-wsus-role-09.png)
9. On **Content**, provide a local path where WSUS will store the update files (In my case, this will be a seperate drive) and click on **Next**.
![](/images/00010-windows-server-update-services/install-wsus-role-10.png)
10. On **Web Server Role (IIS)**, click on **Next**
![](/images/00010-windows-server-update-services/install-wsus-role-11.png)
11. On **Role Services**, there are no further services that need to be enabled. Click on **Next**
![](/images/00010-windows-server-update-services/install-wsus-role-12.png)
12. On **Confirm installation selection**, enable **Restart the destination server automatically if required** and click on **Install**.
![](/images/00010-windows-server-update-services/install-wsus-role-13.png)
13. The installation procedure for Windows Server Update Services will take some time to complete.
![](/images/00010-windows-server-update-services/install-wsus-role-14.png)
14. When the installation has been finished succesfully, we will need to preform the **Post-Installation task**, during the time it is preforming the configuration. During this, you will see the status as **Please wait while your server is configured**. When the status changes to **Configuration succesfully completed**, we are done with the setup and we can closed the **Installation Progress**.
![](/images/00010-windows-server-update-services/install-wsus-role-18.png)

## 2. Preperation of Windows Server Update Services
Now it's time to preform the basic configuration of WSUS.
1. Navigate to the start menu and open Windows Server Update Services, within **Before You Begin** click on **Next**.
![](/images/00010-windows-server-update-services/prep-wsus-role-01.png)
2. When you want to particiate with the Microsoft Update Improvement Program, you can enable it by selecting **Yes, I would like to join the Microsoft Update Improvement Program**. But in my case, I will not to participate. 
![](/images/00010-windows-server-update-services/prep-wsus-role-02.png)
3. Within **Choose Upsteam Server**, select **Synchronize from Microsoft Update** because this is the first and main server that is being configured. Within this we can select **Synchronize from Microsoft Update** and click on **Next**.
![](/images/00010-windows-server-update-services/prep-wsus-role-03.png)
4. On **Specify Proxy Server**, click on **Next**
![](/images/00010-windows-server-update-services/prep-wsus-role-04.png)
5. On **Specify Proxy Server**, click on **Start Connecting** to start the download and synchronize information about the available products from Microsoft. This will take some time (in my case 20 minutes), so be patient.
![](/images/00010-windows-server-update-services/prep-wsus-role-06.png)
6. When the connection with the **Upstream Server** has been established, click on **Next**
![](/images/00010-windows-server-update-services/prep-wsus-role-07.png)
7. On **Choose Languages**, it's possible to select multiple language(s) that WSUS will download. This can be configured in a later state and is not required to be selected at this point except the selection of one language. Click **Next** to continue.
![](/images/00010-windows-server-update-services/prep-wsus-role-08.png)
8. Within **Choose Products**, we can select the different products we want to recieve the updates for. In my case, i will only select **Microsoft Server Operating System-21H2** (*Windows Server 2022*). 
![](/images/00010-windows-server-update-services/prep-wsus-role-09.png)
9. Within Choose Classifcations, select the default which in this case is **Critical Updates**, **Definition Updates** and **Security Updates**. When other updates are needed, this can be modified after the configuration.
* **Critical Updates** 
    * (A widely released fix for a specific problem that addresses a critical, non-security-related bug.)
* **Definition Updates** 
    * (A widely released and frequent software update that contains additions to a product's definition database. Definition databases are often used to detect objects that have specific attributes, such as malicious code, phishing websites, or junk mail.)
* **Security Updates**
    * (A widely released fix for a product-specific, security-related vulnerability. Security vulnerabilities are rated by their severity. The severity rating is indicated in the Microsoft security bulletin as critical, important, moderate, or low.)
![](/images/00010-windows-server-update-services/prep-wsus-role-10.png)
10. By default, we select **Synchronize Manually**. In a later step we will initiate the first sync and set the automatic sync timestamp. Click on **Next**.
![](/images/00010-windows-server-update-services/prep-wsus-role-11.png)
11. On **Finished**, click on **Finish** to end the **initial configuration**.
![](/images/00010-windows-server-update-services/prep-wsus-role-12.png)

## 3. Initial synchronization
After the initial configuration of Windows Server Update Services, we can start the first synchronization. 
1. Open **Windows Server Update Services** and navigate to **Update Services** > **FQDN** > **Synchronizations** and click on **Synchronize Now** 
![](/images/00010-windows-server-update-services/initial-wsus-sync-01.png)
2. The Synchronization will take some time.
![](/images/00010-windows-server-update-services/initial-wsus-sync-02.png)
3. When the synchronization has completed, you will see the amount of new updates that are available.
![](/images/00010-windows-server-update-services/initial-wsus-sync-03.png)

## 4. Setup Group Policy for Windows Server Update Services

Within this part, we are going to configured the Group Policy for WSUS. This will make sure that the devices that are required to preform the communcation and update through WSUS will be filtered. For example, when we are using several user devices and servers, we want to make sure that all the servers will contact the WSUS server and that the client will still reach for Windows Update through the internet. This will make sure that curtain update can be deployed to specific groups (Example: Windows Server 2019, 2022 and-/or Windows 10, 11)

1. Within WSUS, navigate to **Options** and then open **Computers**. Next change the setting from **User the Update Services console** to **Use Group Policy or registry settings on computers** and click on **Apply**
![](/images/00010-windows-server-update-services/configure-wsus-gpo-01.png)
2. Also, we will need to create the Computer Group. **Right-click** on **All Computers** and then on **Add Computer Group...** and enter the name that has will be used through the Group Policy Objects.
![](/images/00010-windows-server-update-services/configure-wsus-gpo-02.png)
3. Now we will need to create the Group Policy Object, to make sure the server will contact the WSUS server for it's patches. On one of the domain controllers, open **Group Policy Management**. Within my environment, i will create the GPO with the name **_BIT-GP-NL_SVR-WSUS**
![](/images/00010-windows-server-update-services/configure-wsus-gpo-06.png)
5. Navigate to the following location **Computer Configuration** > **Policies** > **Administrative Templates** > **Windows Components** > **Windows Update**
![](/images/00010-windows-server-update-services/configure-wsus-gpo-07.png)
6. Open **Configure Automatic Updates** and select **Enable**.
![](/images/00010-windows-server-update-services/configure-wsus-gpo-03.png)
7. Open **Specify intranet Microsoft update service location**, to specify which WSUS server needs to be contacted for the updates. This will need to be provided with the URL to WSUS, for example http://servername.domainname.edu:8530
![](/images/00010-windows-server-update-services/configure-wsus-gpo-04.png)
8. Open **Enable client-side targeting** and enter the name the will be configured in WSUS as the target group.
![](/images/00010-windows-server-update-services/configure-wsus-gpo-05.png)
9. To ensure that the settings are applied, we will need to wait for 90 to 120 minutes before the policy will be applied to the servers. To make sure the policies are applied on demand, we can open **Command Prompt** and preform group policy update.

{{< highlight html >}}
    C:\Users\Administrator> gpupdate /force
{{< /highlight >}}

## 5. Approving the update through Windows Server Update Services.

After sometime, you will see that the servers that have been provided with the information which WSUS server needs to be contacted to for updates.
1. Open Windows Server Update Services and navigate to the group that has been created **Update Services** > **Servername** > **Computers** > **All Computers** > **SVR-Datacenter**
![](/images/00010-windows-server-update-services/deploy-patch-through-wsus-01.png)
2. To approve the update, navigate to **Update** > **All Updates** within WSUS and **right-click** on the patch that is available.
![](/images/00010-windows-server-update-services/deploy-patch-through-wsus-02.png)
3. Now we can approve the update, by clicking on **Approved for install** and then on **OK**
![](/images/00010-windows-server-update-services/deploy-patch-through-wsus-03.png)
4. After the approval, we can see that it has been removed from the **Unassigned** group and is approved for the **SVR-Datacenter** group. Keep in note, that it will take some time to preform the full download of the update depending on the internet speed.
![](/images/00010-windows-server-update-services/deploy-patch-through-wsus-04.png)
5. As verification, it's possible to see that the update is available for the members of the group **SVR-Datacenter**
![](/images/00010-windows-server-update-services/deploy-patch-through-wsus-05.png)

## 6. Installing the patch
Within the following steps, we will preform the update procedure from another server where Windows Server Update Service isn't installed.

1. Open **Settings**, navigate to **Windows Updates** and click on **Check for updates**
![](/images/00010-windows-server-update-services/installing-patch-from-wsus-01.png)
2. During this, we can see that the patch that has been approved will be downloaded and installed.
![](/images/00010-windows-server-update-services/installing-patch-from-wsus-02.png)
3. When the patch has been installed, we need to preform the default restart procedure. When the server has been rebooted, we can preform another request for updates. this will then communicate with WSUS to see if any new patches are available. As we can see, the patch has been installed on **msvs-wds-22-01**.
![](/images/00010-windows-server-update-services/installing-patch-from-wsus-03.png)

## Conclusion ##
Within this blog, we have preformed the basic installation, configuration and deployment of patches for Windows Server 2022. This method will allow to deploy Windows Updates from a central source, instead of 50 seperate servers downloading the updates need to prevented or installed on a later date or time.