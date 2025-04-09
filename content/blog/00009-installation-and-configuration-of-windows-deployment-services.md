---
title: "Installation and configuration of Windows Deployment Services"
slug: "installation-and-configuration-of-windows-deployment-services"
date: "2025-04-01T20:00:00"
author: "Kevin Benningshof"
summary: "Installing Windows 10 or 11 through Windows Deployment Services is a handy for the delivery of devices to multiple end users. Within this blog, we will preform the basic installation of Windows Deployment Services and Microsoft Deployment Toolkit."
tags: [
    "Microsoft",
    "windows Server",
    "Microsoft Deployment Toolkit",
    "Windows Deployment Services"
]
categories: [ "blog" ]
thumbnail: "/images/blog/00009/windows-server-2022.png"
toc: true
draft: false
featured: true
---

# Introduction
Within the company you are working for, there are multiple devices (desktop and-/or laptops) that need to be reinstalled with Windows 10 or Windows 11.  To make sure this will be automated up to a certain level, we can use Windows Deployment Services in combination with Microsoft Deployment Toolkit to automate this proces. Through this blog, we preform the installation, basic configuration and image one of the devices.

## Preperation
For the entire blog, it is required to download the software for the installation of Windows Deployment Services and Microsoft Deployment Kit. 

Name                                                  | Link                                                                                  | Usage
------------------------------------------------------|---------------------------------------------------------------------------------------|-----------
Microsoft Deployment Toolkit                          | [Download](https://www.microsoft.com/en-us/download/details.aspx?id=54259)            | Required
Windows ADK Windows 10, version 2004                  | [Download](https://go.microsoft.com/fwlink/?linkid=2289980)                           | Required
Windows PE add-on 10.1.26100.2454                     | [Download](https://go.microsoft.com/fwlink/?linkid=2120253)                           | Required
Windows 10 Enterprise (22H2)                          | [Download](https://www.microsoft.com/en-us/evalcenter/download-windows-10-enterprise) | Additional
Windows 11 Enterprise (24H2)                          | [Download](https://www.microsoft.com/en-us/evalcenter/download-windows-11-enterprise) | Additional

## Installation of Microsoft Deployment Services
1. Open **Server Manager**
2. Within **Server Manager**, click on **Manage** and then on **Add Roles and Features**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-wds-feature-01.png)
3. On **Installation Type**, select **Role-based or feature-based installation** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-wds-feature-02.png)
4. On **Server Selection**, select the default server *(in this case, msvs-wds-22-01)* and click on **Next** 
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-wds-feature-03.png)
5. On **Select server roles**, enable **Windows Deployment Services** and accept the request for **Additional Features** to be enabled.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-wds-feature-04.png)
6. On **Select features**, click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-wds-feature-05.png)
7. On **WDS**, click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-wds-feature-06.png)
8. On **Select role services**, select both options **Deployment Share** and **Transport Server**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-wds-feature-07.png)
9. On **Confirm installation selections**, click on **Installation**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-wds-feature-08.png)
10. When the installation has completed, click on **Close**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-wds-feature-09.png)

The installation of Windows Deployment Services can be confirmed through the following PowerShell cmdlet.

{{< highlight html >}}
    PS C:\Windows\system32> Get-WindowsFeature *WDS*                                                                                                     

    Display Name                                            Name                       Install State
    ------------                                            ----                       -------------
    [X] Windows Deployment Services                         WDS                            Installed
        [X] Deployment Server                               WDS-Deployment                 Installed
        [X] Transport Server                                WDS-Transport                  Installed
            [X] Windows Deployment Services Tools           WDS-AdminPack                  Installed
{{< /highlight >}}

## Installation of Microsoft Deployment Toolkit
1. Open **MicrosoftDeploymentToolkit_x64.msi**
2. On the **Welcome to the Microsoft Deployment Toolkit Wizard**, click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-mdt-01.png)
3. On **End-User License Agreement**, enable **I accept the terms in the License Agreement** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-mdt-02.png)
4. On **Custom Setup**, click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-mdt-03.png)
5. On **Customer Experience Improvement Program**, select **I don't want to join the program at this time** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-mdt-04.png)
6. On **Ready to install Microsoft Deployment Toolkit**, click on **Install**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-mdt-05.png)
7. On **Completed the Microsoft Deployment Toolkit Setup Wizard**, click on Finish.

## Installation of Windows ADK
*Within the following steps, the download has already been preformed for the Windows ADK files.*
1. Navigate to the installation file of **Windows ADK**
2. **Right-click** on **ADKSetup.exe** and select **Run as administrator**
3. Select **Install the Windows Assessment and Deployment Kit to this computer** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-winadk-01.png)
4. On **Windows Kits Privacy**, select **No** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-winadk-02.png)
5. On **License Agreement**, click on **Accept**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-winadk-03.png)
6. On **Select the features you want to install**, enable the following features and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-winadk-04.png)
7. When the installation has been completed, click on **Close**.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-winadk-05.png)

## Installation of Windows PE add-on
*Within the followin steps, the download has already been preformed for the Windows ADKWinPEAddons files*
1. Navigate to the installation file of **Windows ADKWinPEAddons**
2. **Right-click** on **ADKWinPESetup.exe** and select **Run as administrator**
3. On Specify Location, click **Install the Windows Assessment and Deployment Kit Windows Preinstallation Environment Add-ons Windows 10 to this computer** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-adkwinpe-01.png)
4. On **Windows Kits Privacy**, select **No** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-adkwinpe-02.png)
5. On **License Agreement**, click on **Accept**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-adkwinpe-03.png)
6. On **Select features**, click on **Install**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-adkwinpe-04.png)
7. When the installation has been completed, click on **Close**.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/install-adkwinpe-05.png)

## Configuration of Deployment Share within Windows Deployment Services
1. Open **Windows Deployment Services**.
2. Extend **Servers**, **right-click** on the servername and click on **Configure Server**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-wds-mdt-01.png)
3. On **Before you begin**, click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-wds-mdt-02.png)
4. On **Install Options**, select **Integrated with Active Directory** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-wds-mdt-03.png)
5. On **Remote Installation Folder Location**, select the path where you want to create the RemoteInstall directory and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-wds-mdt-04.png)
6. On **PXE Server Initial Settings**, select **Respond to all client computer (known and unknown)** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-wds-mdt-05.png)
7. On **Operation Complete**, disable **Add images to the server now** and click on **Finish**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-wds-mdt-06.png)

## Configuration of Deployment Share within DeploymentWorkbench
1. Open **Deployment Workbench**.
2. **Right-click** on **Deployment Share** and click on **New Deployment Share**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-wds-mdt-07.png)
3. On Path, , select the path where you want to create the DeploymentShare directory and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-wds-mdt-08.png)
4. On **Share**, click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-wds-mdt-09.png)
5. On **Descriptive Name**, click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-wds-mdt-10.png)
6. On **Options**, deselect the options **Ask if a computer backup should be preformed**, **Ask if an image should be captured** and **Ask if BitLocker should be enabled** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-wds-mdt-11.png)
7. On **Summary**, click on **Next** to preform the configuration.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-wds-mdt-12.png)
8. When this is done, click on **Finish**

## Add Operating System to DeploymentShare
*Before proceeding, make sure the ISO is present and mounted on the server for Windows Deployment Services.*
1. Open **Deployment Workbench**.
2. Right-click on **Operating System** and click on **Import Operating System**.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/import-os-01.png)
3. On **OS Type**, select **Full set of source files** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/import-os-02.png)
4. On **Source**, select the directory of the mounted ISO file and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/import-os-03.png)
5. On **Destination**, change the name that is application to your naming scheme and click on **Next** *In my case, this will be Windows 11, Enterprise (24H2)*
![](/images/00009-installation-and-configuration-of-windows-deployment-services/import-os-04.png)
6. On **Summary**, click on **Next** to preform the import.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/import-os-05.png)
7. When the **import** has been completed, click on **Finish**

## Create the Task Sequence
1. Open **Deployment Workbench**.
2. Right-click on **Task Sequences** and click on **New Task Sequence**.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/create-task-01.png)
3. On **General Settings**, enter the required information
**Example** 
* Task sequence ID: W11ENT24H2-NLLT / W11ENT24H2-NLDK
* Task sequence name:  Windows 11, Enterprise (24H2)
![](/images/00009-installation-and-configuration-of-windows-deployment-services/create-task-02.png)
4. On **Select Template**, select **Standard Client Task Sequence** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/create-task-03.png)
5. On **Select OS**, select the operating system that you want to use for the task sequence and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/create-task-04.png)
6. On **Specify Product Key**, select **Do not specify a product key at this time** and click on **Next**.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/create-task-05.png)
7. On **OS Settings**, enter the information that is required and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/create-task-06.png)
8. On **Admin Password**, enable **Use the specifiec local Administrator password** and enter a secure password, that will be used for the local administrator account and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/create-task-07.png)
9. On **Summary**, click on **Next**
10. On **Confirmation**, click on **Finish**

## Configure the DeploymentShare

1. Open **Deployment Workbench**
2. **Right-click** on **MDT Deployment share** and click on **Properties**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-mdt-share-01.png)
3. Within the tab **General**, deselect **x86**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-mdt-share-02.png)
4. Within the tab **Windows PE**, disable the setting **Generate a Lite Touch bootable ISO image** on both platforms **x86** and **x64**.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-mdt-share-03.png)
5. Within the tab **Monitoring**, enable **Enable monitoring for this deployment share**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/config-mdt-share-04.png)
6. Click on **Apply** and close the **MDT Deployment share** window.

## Update the DeploymentShare

To ensure the configuration is applied within the new config
1. Open **Deployment Workbench**
2. **Right-click** on **MDT Deployment Share** and click on **Update Deployment Share**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/update-mdt-share-01.png)
3. Because this is the first time we generate the boot image, select **Completely regenerate the boot images** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/update-mdt-share-02.png)
4. On **Summary**, click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/update-mdt-share-03.png)
5. When the boot image has been generated, click on **Finish**

## Import the Lite Touch PE boot image

1. Open **Windows Deployment Services**
2. Navigate to **Servers** > **servername**, right-click on **Boot Images** and click on **Add Boot Image...**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/import-litetouch-wim-01.png)
4. Within **Image File**, enter the location of **LiteTouchPE_x64.wim** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/import-litetouch-wim-02.png)
5. On **Image Metadata**, click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/import-litetouch-wim-03.png)
6. On **Summary**, click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/import-litetouch-wim-04.png)
7. When the boot image has been generated, click on **Finish**

### DeploymentShare rules

The use of Windows Deployment Services with Microsoft Deployment Toolkit is to making the installation and preperation of devices alot easier and less time consuming. Doing this the normal way, is to make sure the write settings are selected and the correct user and applications are selected. To make sure this is automated till a 

1. Open **DeploymentWorkbench**.
2. Navigate to **Deployment Shares** and right-click on **MDT Deployment Share** and click on **Properties**
3. Within MDT Deployment Share, navigate to the tab **Rules**

{{< highlight html >}}
    [Settings]
    Priority=Default
    Properties=MyCustomProperty

    [Default]
    OSInstall=Y
    SkipBDDWelcome=YES
    SkipCapture=YES
    SkipAdminPassword=YES
    SkipProductKey=YES
    SkipComputerBackup=YES
    SkipBitLocker=YES
    SkipUserData=YES
    SkipTimeZone=YES
    SkipLocaleSelection=YES

    JoinDomain=benningshof-it.edu
    MachineObjectOU=OU=Laptops,OU=Computers,OU=benningshof-it,OU=Netherlands,OU=AMEA,DC=benningshof-it,DC=edu
    KeyboardLocale=en-US
    TimeZoneName=W. Europe Standard Time

    EventService=http://msvs-wds-22-01:9800
{{< /highlight >}}

4. Open **Edit Bootstrap.ini** and copy and past the following configuration and click on **Apply**

{{< highlight html >}}

    [Settings]
    Priority=Default

    [Default]
    DeployRoot=\\msvs-wds-22-01\DeploymentShare$

    UserID=
    UserDomain=benningshof-it.edu
    UserPassword=

    KeyboardLocalePE=1033:00001033

    SkipBDDWelcome=YES

{{< /highlight >}}

Now we will need to update the Deployment Share within DeploymentWorkbench to enable the new settings.

5. Right-click on **MDT Deployment Share** and click on **Update Deployment Share**.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/update-deployment-share-01.png)
6. On **Options**, select **Completely regenerate the boot images** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/update-deployment-share-02.png)
7. On **Summary**, click on **Next**.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/update-deployment-share-03.png)
8. When the new LiteTouchPE boot image has been created, click on **Finish**

## Update LiteTouchPE_x64 within Windows Deployment Services

1. Open **Windows Deployment Services**.
2. Navigate to **Servers** > **msvs-wds-22-01** > **Boot Images**.
3. **Right-click** on Lite Touch Windows PE (x64) and click on **Replace  Image...**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/update-deployment-share-04.png)
4. Click on **Browse...** and navigate to the where **LiteTouchPE_x64.wim** is located and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/update-deployment-share-05.png)
5. On **Available Images**, select **Lite Touch Windows PE (x64)** and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/update-deployment-share-06.png)
6. On **Image Metadata**, click on **Next**.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/update-deployment-share-07.png)
7. On **Summary**, click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/update-deployment-share-08.png)
8. When the Lite Touch Windows PE (x64) image has been replaced, click on **Finish**

## Installing Windows through PXE

To make sure that the device can PXE boot on the network, it is required to configured the DHCP scope with the Option Names **066 Boot Server Host Name** and **067 Bootfile Name** which will forward the boot request to the WDS server.

{{% notice info "Info" %}}
The following steps are preformed within a Virtual Machine running in Hyper-V. This shouldn't be different then in the production environment when DHCP has been configured with Option Names in the Scope and the configurataion 

Option Name                 | Vendor    | Value                     | Policy Name
----------------------------|-----------|---------------------------|-----------
003 Router                  | Standard  | 10.11.2.62                | None
006 DNS Servers             | Standard  | 10.11.2.1, 10.11.2.2      | None
015 DNS Domain Name         | Standard  | benningshof-it.edu        | None
066 Boot Server Host Name   | Standard  | 10.11.2.10                | None
067 Bootfile Name           | Standard  | \smsboot\x64\wdsnbp.com   | None
{{% /notice %}}

1. Start the device / virtual machine and press **F12** during the startup or change the boot order within the BIOS/EUFI.
2. When the PXE boot has been initiated and the device has been presented with the information regarding WDS Boot Manager press **ENTER** to start the network boot.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/pxe-boot-01.png)
3. On the **User Credentials**, enter the **Username** and **Password** of a domain account that member of the security group **Domain Admins**.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/pxe-boot-02.png)
4. Select the **Task Sequence** that has been configured for the installation and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/pxe-boot-03.png)
5. Enter the **Computer name** and the additional information and click on **Next**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/pxe-boot-04.png)
6. The installation will proceed and will take some time to complete.
![](/images/00009-installation-and-configuration-of-windows-deployment-services/pxe-boot-05.png)
7. When the installation has been completed, you will see the following **confirmation**
![](/images/00009-installation-and-configuration-of-windows-deployment-services/pxe-boot-06.png)

## Conclusion ##
Within this post, we've successfully configured a complete MDT environment for deploying Windows 11. This streamlined process allows for efficient and automated Operating System deployment through Microsoft Deployment Toolkit and Windows Deployment Services and the basic modification of bootstap.ini and Rules to steamline the deployment.