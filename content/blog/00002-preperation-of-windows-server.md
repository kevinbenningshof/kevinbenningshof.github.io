---
title: "Preperation of Windows Server"
slug: "preperation-of-windows-server"
date: "2025-03-01T21:00:00"
author: "Kevin Benningshof"
summary: "After the installation of Microsoft Windows Server has been completed, it's not ready for being being deployed and being active within production. Within this blog, we will preform the steps that are set in the 'checklist' to make sure this has been preformed before usage."
tags: [
    "Microsoft",
    "windows Server"
]
categories: [ "blog" ]
thumbnail: "/icons/windows-server-2022.png"
toc: true
draft: false
featured: true
---

## Introduction ##

After we have preformed the installation of Windows Server that has been preformed within [How to install Windows Server](/post/2025/02/23/how-to-install-windows-server-2022/), there are some default tasks that need to be performed to make sure the server is working properly before deploying it within your environment.

To make sure the servers that are freshly installed with Windows Server, i've made a quick checklist for myself to make sure these tasks are completed before deploying it within production.

## Install VirtIO drivers ##

To make sure that Windows Server is running properly within Proxmox, it's required to install the VirtIO drivers. This will make sure, the essential network card (NIC) driver is installed.

{{% notice note "VirtIO drviers" %}}
The drivers for the virtual machine can be downloaded through 

* [Proxmox VirtIO Drivers](https://pve.proxmox.com/wiki/Windows_VirtIO_Drivers#Using_the_ISO)
* [VirtIO latest stable (direct link)](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso)
{{% /notice %}}

1. When the ISO has been mounted, start the installation by executing **virtio-win-guest-tools.exe**
2. Select **I agree to the license terms and conditions** Click on **Install**
![](/images/00002-windows-server-post-installation/install-drivers-01.png)
3. On **Custom Setup**, click on **Next**
![](/images/00002-windows-server-post-installation/install-drivers-02.png)
4. On **Ready to install Virtio-win-driver-install**, click on **Install**
![](/images/00002-windows-server-post-installation/install-drivers-03.png)
5. When the installation is done, click on **Finish**
![](/images/00002-windows-server-post-installation/install-drivers-04.png)
6. After the final check, click on **Close**
![](/images/00002-windows-server-post-installation/install-drivers-05.png)
7. After this, you can see that the NIC driver has been installed.
![](/images/00002-windows-server-post-installation/install-drivers-06.png)

## Change computer name ##

To change the computername, there are several ways this can be preformed. This will also make sure that the correct server is identified.
In my case, there is a standard of the devicename that is applied. So the server will be supplied with the name **MSVS-ADDC-22-01**

1. Open **Server Manager**, click on **Local Server** and then on **Computer name**. 
2. Within **System Properties**, click on **Change**
3. Within **Computer Name/Domain Changes** enter the new devicename and click on **OK**
4. After the change has been preformed, it's required to preform a restart to make sure te new name is applied.
![](/images/00002-windows-server-post-installation/set-computername-01.png)
5. It’s also possible, to preform these changes through PowerShell 
{{% notice tip "PowerShell" %}}
To change the computername through PowerShell, this can be preformed with the following command.  
```pwsh
    PS C:\Users\Administrator> Rename-Computer msvs-addc-22-01
    WARNING: The change will take effect after you restart the computer WIN-2L3H1EP69NO
```

To restart the computer, run the following command
```pwsh
    PS C:\Users\Administrator> restart-computer
```

To verify the computername, run the following command
```pwsh
    PS C:\Users\Administrator> hostname
    msvs-addc-22-01
```

{{% /notice %}}

## Set a static IP address ##
To make sure, that the server is able to communicate with the internet and other devices within the IP range the static IP address needs to be configured.

1. Open **Control Panel**, click on **Network and Shareing Center** and click on the NIC that you want to set the static IP to.
2. Within **Ethernet Status**, click on **Properties**.
3. Within **Ethernet Properties**, double click on **Internet Protocol Version 4 (TCP/IPv4)**.
4. Within **Internet Protocol Version 4 (TCP/IPv4)**, enter the IP, Subnet mask, Default Gateway and the DNS server(s) and click on **OK** to apply the settings.
![](/images/00002-windows-server-post-installation/set-ip-address-01.png)
5. It's also possible, to preform these changes through PowerShell
{{% notice tip "PowerShell" %}}
To check the current IP address, gateway and DNS settings.  
```pwsh
    PS C:\Users\Administrator> Get-NetIPConfiguration

    InterfaceAlias       : Ethernet
    InterfaceIndex       : 4
    InterfaceDescription : Red Hat VirtIO Ethernet Adapter
    NetProfile.Name      : Network
    IPv4Address          : 192.168.2.2
    IPv6DefaultGateway   :
    IPv4DefaultGateway   : 192.168.1.1
    DNSServer            : 192.168.1.1
                           8.8.8.8
```

To set the new IP address, subnet and gateway on the specific InterfaceIndex
```pwsh
    PS C:\Users\Administrator> New-NetIPAddress `
    -InterfaceIndex 4 `
    -IPAddress 10.11.2.1 `
    -PrefixLength 26 `
    -DefaultGateway 10.11.2.62
```

To set the new DNS on the specific InterfaceIndex
```pwsh
    PS C:\Users\Administrator> Set-DnsClientServerAddress `
    -InterfaceIndex 4 `
    -ServerAddresses ("1.1.1.1","8.8.8.8")
```
{{% /notice %}}

## Set the timezone ##

1. Open the **start menu** and navigate to the **Control Panel**
2. Within the **Control Panel**, open **Date and Time** 
3. within **Date and Time**, click on **Change time zone...**
4. Within **Time Zone Settings**, select the timezone and click on **OK** to apply the settings.
![](/images/00002-windows-server-post-installation/set-timezone-01.png)
5. It's also possible, to preform these changes through PowerShell
{{% notice tip "PowerShell" %}}
To view all timezones, that are available, in my case this is **W. Europe Standard Time** 
```pwsh
    PS C:\Users\Administrator> Get-TimeZone -ListAvailable

```

To set the the **Timezone**
```pwsh
    PS C:\Users\Administrator> Set-TimeZone -Id 'W. Europe Standard Time'

```

To check if the **timezone** is applied.
```pwsh
    PS C:\Users\Administrator> Get-TimeZone
    Id                         : W. Europe Standard Time
    HasIanaId                  : False
    DisplayName                : (UTC+01:00) Amsterdam, Berlin, Bern, Rome, Stockholm, Vienna
    StandardName               : W. Europe Standard Time
    DaylightName               : W. Europe Daylight Time
    BaseUtcOffset              : 01:00:00
    SupportsDaylightSavingTime : True
```
{{% /notice %}}

## Update Windows Server through Windows update ##
To make sure, all the new updates are installed we need to trigger the search.

1. Click on **Start** and then on **Settings**.
2. Now click on **Update & Security** and then on **Check for Updates**.
3. When updates are available, Windows will start the download and installation procedure.
4. After the installation of all the patches has been completed, click on **Restart now**.
![](/images/00002-windows-server-post-installation/install-windows-updates-01.png)
5. The updates will take some time to install, also it's important that this needs to be preformed several times untill Windows Updates notifies **You're up to date**.
![](/images/00002-windows-server-post-installation/install-windows-updates-02.png)


## Join the domain ##
To make sure the server is member of the domain, we can preform the following steps. (if this exists, with exclusion of the first domain controller)
1. Open **Server Manager**, click on **Local Server** and then within **Properties**, click on **WORKGROUP**.
2. Within **System Properties**, click on **Change**.
3. Within **Computer Name / Domain Changes**, in **Member of** select **Domain** and enter the domainname (for example: CORP)
![](/images/00002-windows-server-post-installation/join-domain-01.png)
4. When this is applied, the device needs to reboot.

{{% notice tip "PowerShell" %}}
To join the domain **CORP**, after this you need to apply the username and password.
```pwsh
    PS C:\Users\Administrator> Add-Computer -DomainName CORP

```
{{% /notice %}}

 

## Conclusion ##

Within this post, the steps have been shown that are needed to make sure the server is configured (at basic level) before using it within production. Keep in mind, that this can be different within every environment and is based on my personal 'checklist'.

