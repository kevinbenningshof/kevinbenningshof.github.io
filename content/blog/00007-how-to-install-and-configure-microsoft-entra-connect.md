---
title: "How to Install and Configure Microsoft Entra Connect"
slug: "how-to-install-and-configure-microsoft-entra-connect"
date: "2025-03-22T20:00:00"
author: "Kevin Benningshof"
summary: "Microsoft Entra Connect is an on-premises Microsoft application designed to meet and accomplish your hybrid identity goals. Within this blog, we will prepaire, install and check the first synchronization."
tags: [
    "Microsoft",
    "windows Server",
    "Entra"
]
categories: [ "Blog" ]
thumbnail: "/images/blog/00007/microsoft-entra-connect.png"
toc: true
draft: false
featured: true
---

# Introduction
Microsoft Entra Connect is an on-premises Microsoft application designed to meet and accomplish your hybrid identity goals. 

## What is Microsoft Entra Connect

Microsoft Entra Connect is the on-premises application that is designed to meet and accomplish you hybrid identity goals. Integrating your on-premises directories with Microsoft Entra ID makes your users more productive by providing a common identity for accessing both cloud and on-premises resources. This is the newer version and replacement of the identity integrations tools of DirSync and Azure AD Sync.

## Preperation

Before we begin the installation of AzureADConnect, we will need to prepare the requirements for AzureADConnect. From Microsoft it is required to [**force TLS 1.2 for Microsoft Entra Connect**](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/reference-connect-tls-enforcement). Therefor we will need to preform a change to the registery which can be done through Registry Editor (regedit.exe) or through PowerShell which we are going to do now.

To preform the check, if TLS 1.2 is in use we can preform the following script
{{< highlight html >}}
        Function Get-ADSyncToolsTls12RegValue
    {
        [CmdletBinding()]
        Param
        (
            # Registry Path
            [Parameter(Mandatory=$true,
                    Position=0)]
            [string]
            $RegPath,

    # Registry Name
            [Parameter(Mandatory=$true,
                    Position=1)]
            [string]
            $RegName
        )
        $regItem = Get-ItemProperty -Path $RegPath -Name $RegName -ErrorAction Ignore
        $output = "" | select Path,Name,Value
        $output.Path = $RegPath
        $output.Name = $RegName

    If ($regItem -eq $null)
        {
            $output.Value = "Not Found"
        }
        Else
        {
            $output.Value = $regItem.$RegName
        }
        $output
    }

    $regSettings = @()
    $regKey = 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319'
    $regSettings += Get-ADSyncToolsTls12RegValue $regKey 'SystemDefaultTlsVersions'
    $regSettings += Get-ADSyncToolsTls12RegValue $regKey 'SchUseStrongCrypto'

    $regKey = 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319'
    $regSettings += Get-ADSyncToolsTls12RegValue $regKey 'SystemDefaultTlsVersions'
    $regSettings += Get-ADSyncToolsTls12RegValue $regKey 'SchUseStrongCrypto'

    $regKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server'
    $regSettings += Get-ADSyncToolsTls12RegValue $regKey 'Enabled'
    $regSettings += Get-ADSyncToolsTls12RegValue $regKey 'DisabledByDefault'

    $regKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client'
    $regSettings += Get-ADSyncToolsTls12RegValue $regKey 'Enabled'
    $regSettings += Get-ADSyncToolsTls12RegValue $regKey 'DisabledByDefault'

    $regSettings
{{< /highlight >}}

As we can see, TLS 1.2 hasn't been configured yet

{{< highlight html >}}

    Path                                                                                       Name                     Value
    ----                                                                                       ----                     -----
    HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319                              SystemDefaultTlsVersions Not Found
    HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319                              SchUseStrongCrypto       Not Found
    HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319                                          SystemDefaultTlsVersions Not Found
    HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319                                          SchUseStrongCrypto       Not Found
    HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server Enabled                  Not Found
    HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server DisabledByDefault        Not Found
    HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client Enabled                  Not Found
    HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client DisabledByDefault        Not Found

{{< /highlight >}}

To enable TLS 1.2, we can preform the following script and make sure the server will rebooted for the changes to take affect.
{{< highlight html >}}

    If (-Not (Test-Path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319'))
    {
        New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null
    }
    New-ItemProperty -Path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Name 'SystemDefaultTlsVersions' -Value '1' -PropertyType 'DWord' -Force | Out-Null
    New-ItemProperty -Path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Name 'SchUseStrongCrypto' -Value '1' -PropertyType 'DWord' -Force | Out-Null

    If (-Not (Test-Path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319'))
    {
        New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null
    }
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Name 'SystemDefaultTlsVersions' -Value '1' -PropertyType 'DWord' -Force | Out-Null
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Name 'SchUseStrongCrypto' -Value '1' -PropertyType 'DWord' -Force | Out-Null

    If (-Not (Test-Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server'))
    {
        New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
    }
    New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Name 'Enabled' -Value '1' -PropertyType 'DWord' -Force | Out-Null
    New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Name 'DisabledByDefault' -Value '0' -PropertyType 'DWord' -Force | Out-Null

    If (-Not (Test-Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client'))
    {
        New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
    }
    New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Name 'Enabled' -Value '1' -PropertyType 'DWord' -Force | Out-Null
    New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Name 'DisabledByDefault' -Value '0' -PropertyType 'DWord' -Force | Out-Null

    Write-Host 'TLS 1.2 has been enabled. You must restart the Windows Server for the changes to take affect.' -ForegroundColor Cyan

{{< /highlight >}}

Now, when we preform the check if TLS 1.2 is enforced we will recieve the following information.
{{< highlight html >}}

    Path                                                                                       Name                     Value
    ----                                                                                       ----                     -----
    HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319                              SystemDefaultTlsVersions     1
    HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319                              SchUseStrongCrypto           1
    HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319                                          SystemDefaultTlsVersions     1
    HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319                                          SchUseStrongCrypto           1
    HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server Enabled                      1
    HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server DisabledByDefault            0
    HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client Enabled                      1
    HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client DisabledByDefault            0

{{< /highlight >}}

## Download Microsoft Entra Connect

Microsoft Entra Connect is free for everyone and can be downloaded through Microsoft Download Center

1. Open the following URL
[**Microsoft Entra Connect**](https://www.microsoft.com/en-us/download/details.aspx?id=47594)
2. Click on **Download** and installation file will be stored on the default downloads location **C:\Users\Username\Downloads**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/download-microsoft-entra-01.png)

## Install and configure Microsoft Entra Connect

1. Right-click on **AzureADConnect.msi** and click on **install**
2. Select **I agree to the license terms and privacy notice** and click on **Continue**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-01.png)
3. On **Express Settings**, click on **Customize**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-02.png)
4. On **Install required components**, click on **Install** to start the installation of Microsoft Entra synchronization service.
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-03.png)
5. The installation will take some time to complete (around 2 minutes)
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-04.png)
6. On **User sign-in**, select **Password Hash Synchronization** and click on **Next**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-05.png)
7. On **Connect to Microsoft Entra ID**, enter the username of the account that has the Administrative role **Global Administrator** and click on **Next**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-06.png)
8. On Sign in to your account, enter the login credentials and click on **Sign-in**.
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-07.png)
9. On **Connect your directories**, select the Directory type **Active Directory** and the **forest** and click on **Add Directory**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-08.png)
10. Within AD forest account, select the option **Create new AD account** and enter the login credentials of the account that is member of the security group **Enterprise Administrator** within your domain and click on **OK**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-09.png)
12. On **Microsoft Entra sign-in configuration**, select **Continue without matching all UPN suffixs to verified domains** and click on **Next**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-10.png)
13. On **Domain and OU filtering**, we can select the basics by selecting for example the OU that contains the Domain Users and click on **Next**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-11.png)
14. On **Uniquely identify your users**, select **Users are represented only once accross all directories** and **Let Azure manage the source anchor** and click on **Next**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-12.png)
15. On **Filter users and devices**, select **Synchronize all users and devices** and click on **Next**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-13.png)
16. On **Optional features**, click on **Next**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-14.png)
17. On **Ready to configure**, select **Start the synchronization process when configuration completes** and click on **Install**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-15.png)
18. When the configuration of Microsoft Entra Connect has been complted, click on **Exit**
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/install-microsoft-entra-16.png)

## Controlling the Microsoft Entra Connect sync
When the installation of Microsoft Entra Connect has been completed, we need to preform the first synchronisation so we could check if the correct information is issued to Microsoft Entra.

1. Within the start menu, we can see that the following applications are available
* **Synchronization Rules Editor** (You can use the synchronization rule editor to edit or create a new synchronization rule)
* **Synchronization Service** (The Synchronization Service Manager UI is used to configure more advanced aspects of the synchronization engine and to view the operational aspects of the service.)
* **Azure AD Connect** (Microsoft Entra Connect is an on-premises Microsoft application designed to meet and accomplish your hybrid identity goals.)
{{% notice note "Unable to connect to the Synchronization Service" %}}When you are starting the **Synchronization Service** for the first time after completing the installation, we will recieve the following error.
For this to be solved, is to simply **Sign-out** and **Sign-in** to make sure your account is updated with it's group membership.
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/check-microsoft-entra-02.png)
{{% /notice %}}
2. As we can see, the synchronization has been preformed with success according to the logs of **benningshofit.onmicrosoft.com - AAD (export)**.
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/check-microsoft-entra-03.png)
3. As confirmation, we can also check whether the synchronization and the user has been created within [**Microsoft Entra**](https://entra.microsoft.com/#view/Microsoft_AAD_UsersAndTenants/UserManagementMenuBlade/~/AllUsers/menuId/).
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/check-microsoft-entra-04.png)
4. Finally, we can see that the setup has created a new service account especially for the On-Premises Directory Synchronization with the User Principal Name **On-Premises Directory Synchronization Service Account**.
![](/images/00007-how-to-install-and-configure-microsoft-entra-connect/check-microsoft-entra-05.png)

## Conclusion ##
Within this blog, we have preformed the basic steps for the preperation, installation and final check of Microsoft Entra Connect. With this service it is possible to preform the synchronization of local resources (Users, Computer, Security Groups, etc..) to the cloud environment of Microsoft Azure.