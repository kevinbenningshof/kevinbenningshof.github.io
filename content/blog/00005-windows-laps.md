---
title: "Installation and configuration of LAPS (Local Administrator Password Solution)"
slug: "installation-and-configuration-of-laps"
date: "2025-03-02T21:00:00"
author: "Kevin Benningshof"
summary: "Within this blog, we are going to take a look how it's possible to install, configure and deploy Windows LAPS"
tags: [
    "Microsoft",
    "windows Server",
    "LAPS"
]
categories: [ "blog" ]
thumbnail: "/images/blog/00005/windows-server-2022.png"
toc: true
draft: false
featured: true
---

## Introduction ##
Windows LAPS is one of the most effective ways to protect administrator password and prevent unauthorized users getting access to systems and-/or data that they may not have access to. Within Microsoft Local Administrator Password Solution, it will make it possible to manage these passwords and make sure this will be randomized within a set period of time.

Within this blog, we will cover the basics of Microsoft LAPS and ensure that it will.

## What are the benefits of using Windows LAPS ##

Use Windows LAPS to regularly rotate and manage local administrator account passwords and get these benefits:

* Protection against pass-the-hash and lateral-traversal attacks
* Improved security for remote help desk scenarios
* Ability to sign in to and recover devices that are otherwise inaccessible
* A fine-grained security model (access control lists and optional password encryption) for securing passwords that are stored in Windows Server Active Directory
* Support for the Entra role-based access control model for securing passwords that are stored in Microsoft Entra ID

## Windows LAPS supported operating system ##

Windows LAPS is not available for every version of Windows Operating Systems. Currently the following operating systems are supported.

* Windows 11 23H2 (and later Windows Client releases)
* Windows Server 23H2 (and later Windows Server releases)
* Windows 11 22H2 [April 11, 2023—KB5025239 (OS Build 22621.1555)](https://support.microsoft.com/en-us/topic/april-11-2023-kb5025239-os-build-22621-1555-5eaaaf42-bc4d-4881-8d38-97e0082a6982)
* Windows 11 21H2 [April 11, 2023—KB5025224 (OS Build 22000.1817)](https://support.microsoft.com/en-us/topic/april-11-2023-kb5025224-os-build-22000-1817-ebc75372-608d-4a77-a6e0-cb1e15f117fc)
* Windows 10 [April 11, 2023—KB5025221 (OS Builds 19042.2846, 19044.2846, and 19045.2846)](https://support.microsoft.com/en-us/topic/april-11-2023-kb5025221-os-builds-19042-2846-19044-2846-and-19045-2846-b00c3356-baac-4a41-8342-7f97ec83445a)
* Windows Server 2022 [April 11, 2023—KB5025230 (OS Build 20348.1668)](https://support.microsoft.com/en-us/topic/april-11-2023-kb5025230-os-build-20348-1668-28a5446e-6389-4a5b-ae3f-e942a604f2d3)
* Windows Server 2019 [April 11, 2023—KB5025229 (OS Build 17763.4252)](https://support.microsoft.com/en-us/topic/april-11-2023-kb5025229-os-build-17763-4252-e8ead788-2cd3-4c9b-8c77-d677e2d8744f)

All supported editions of the above platforms have been updated with Windows LAPS, including LTSC editions. The introduction of the Windows LAPS feature doesn't modify in any way whatsoever the standard Microsoft product lifecycle policies.

## How to install Windows LAPS ##

Currently, it's not required anymore to download and install the Windows LAPS client on one of the Domain Controllers since the patch releases of April 2023.
Therefor there are no further actions required.

### Extending the Domain Schema ###
1. Open **PowerShell** as **Administrator** on one of the Domain Controllers.

2. Run the following command to verify if **LAPS module** is present.
{{< highlight html >}}
    PS C:\Users\Administrator> Get-Command -Module LAPS
{{< /highlight >}}

3. When the **LAPS module** is present, you will recieve the following response.
{{< highlight html >}}
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Function        Get-LapsAADPassword                                1.0.0.0    LAPS
    Function        Get-LapsDiagnostics                                1.0.0.0    LAPS
    Cmdlet          Find-LapsADExtendedRights                          1.0.0.0    LAPS
    Cmdlet          Get-LapsADPassword                                 1.0.0.0    LAPS
    Cmdlet          Invoke-LapsPolicyProcessing                        1.0.0.0    LAPS
    Cmdlet          Reset-LapsPassword                                 1.0.0.0    LAPS
    Cmdlet          Set-LapsADAuditing                                 1.0.0.0    LAPS
    Cmdlet          Set-LapsADComputerSelfPermission                   1.0.0.0    LAPS
    Cmdlet          Set-LapsADPasswordExpirationTime                   1.0.0.0    LAPS
    Cmdlet          Set-LapsADReadPasswordPermission                   1.0.0.0    LAPS
    Cmdlet          Set-LapsADResetPasswordPermission                  1.0.0.0    LAPS
    Cmdlet          Update-LapsADSchema                                1.0.0.0    LAPS
{{< /highlight >}}
{{% notice note "More information" %}}
* For more information regarding the commands [LAPS Description](https://learn.microsoft.com/en-us/powershell/module/laps/?view=windowsserver2025-ps#laps)
{{% /notice %}}

4. To update the Active Directory Schema you can run the following
{{< highlight html >}}
    PS C:\Users\Administrator> Update-LapsADSchema
{{< /highlight >}}

5. To preform the schema update, you will need to respond with **A** and press **Enter**.
{{< highlight html >}}
    PS C:\Users\Administrator> Update-LapsADSchema

    The 'ms-LAPS-Password' schema attribute needs to be added to the AD schema.
    Do you want to proceed?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"): A
{{< /highlight >}}

### Check if LAPS is active ###

**PowerShell**
1. To verify if the LapsADSchema is configured properly, you can run the following command to recieve the results
{{< highlight html >}}
    PS C:\Users\Administrator> Update-LapsADSchema -Verbose
{{< /highlight >}}

2. Within the results that are generated, it will show the check request to LDAP, the response, the verification that if it exists or not and then the validation. Within the output, it will show which attributes have been succesfully extended within LAPS schema
{{< highlight html >}}
    * msLAPS-PasswordExpirationTime
    * msLAPS-Password
    * msLAPS-EncryptedPassword
    * msLAPS-EncryptedPasswordHistory
    * msLAPS-EncryptedDSRMPassword
    * msLAPS-EncryptedDSRMPasswordHistory.
{{< /highlight >}}

**Active Directory**
1. The verification can also be preformed through **Active Directory User and Computers**.
2. Right-click on the **Object** and click on **Properties**
3. Within the tabs **LAPS** and **Attribute Editor**, we can see it's configured.
![](/images/00005-windows-laps/check-laps-in-active-directory-01.png)

{{% notice note "More information" %}}
* To make sure this is visible, we need to enable **Advanced features** within Active Directory
![](/images/00005-windows-laps/enable-advanced-features-01.png)
{{% /notice %}}

### Configure the LAPS permissions ###

The managed device needs to be granted permission to update its password and make it visible within Active Directory. This action is performed by setting inheritable permissions on the **Organizational Unit (OU)** the device is located in. The **Set-LapsADComputerSelfPermission** is used for this purpose.

Within my environment, there are several OU's present for the seperation of services.
![](/images/00005-windows-laps/laps-permissions-01.png)

1. To set the permissions to the OU and sub OU's, we need to preform the following command.

{{< highlight html >}}
    PS C:\Users\Administrator> Set-LapsADComputerSelfPermission -Identity OU=Servers,OU=benningshof-it,OU=Netherlands,OU=INFRA,DC=benningshof-it,DC=edu
{{< /highlight >}}

## Configuration of Group Policies ##
From this part, we will need to create a Group Policy that will initiate the use of LAPS by the servers and-/or clients. In this case, i've created a new Group Policy with the namt **_BIT-GP-NL_SVR-LAPS**
![](/images/00005-windows-laps/configure-laps-group-policy-01.png)

When the group policy is created through **Group Policy Manager**, navigate to  **Computer Configuration** > **Policies** > **Policy definitions (ADMX files) retrieved from the local computer** > **System** > **LAPS**. Within the directory, you will see that alle the settings aren't enabled. This will be preformed and explained within the following steps.
![](/images/00005-windows-laps/configure-laps-group-policy-02.png)

### Enable password backup for DSRM accounts ###
When you enable this setting the DSRM administrator account password will be managed and backed up to Active Directory. Enabling this setting has no effect unless the managed device is a domain controller and password encryption is also enabled.
![](/images/00005-windows-laps/configure-laps-group-policy-08.png)

### Configure size of encrypted password history ###
This will enable you to define how many backups will be stored within the history. In my case, the last 12 generated passwords will be stored.
![](/images/00005-windows-laps/configure-laps-group-policy-04.png)

### Enable password encryption ###
By default, Windows LAPS stores the passwords within Active Directory. But this will be through an un-encrypted way. With this option, the device containing the managed admin account performs the encryption, so there are no artifacts that a bad actor can use.
![](/images/00005-windows-laps/configure-laps-group-policy-07.png)

### Configure authorized password decryptors ###
If you enable secret encryption for Windows LAPS secrets, you must configure a principal that is allowed to decrypt those LAPS secrets unless you only plan on domain admins performing this task. If you want other groups to be able to decrypt Windows LAPS secrets that are encrypted, and you want your domain admins to also be able to decrypt those secrets, group nesting can come in handy.
![](/images/00005-windows-laps/configure-laps-group-policy-10.png)

### Name of administrator account to manager ###
With this options, it is possible to specify a custom Administrator account name to manage the password for. Example: account **company_admin** instead of Administrator. To make sure this works, you need to create a new account through Group Policies that will function as the replacement.
![](/images/00005-windows-laps/configure-laps-group-policy-11.png)

### Configure password backup directory ###
If enabled, the password will be stored in either Active Directory "local" or within Azure Active Directory "cloud". When this options is not enabled, Tthe local administrator password isn't managed and will not work.
![](/images/00005-windows-laps/configure-laps-group-policy-03.png)

### Do not allow password expiration time longer than required by policy ###
When this is enabled, the password that has been applied will be changed when the expiration time has passed.
![](/images/00005-windows-laps/configure-laps-group-policy-09.png)

### Password Settings ###
This will enable you to manage the length, difficulty of the password and within which time stamp it needs to be renewed.
![](/images/00005-windows-laps/configure-laps-group-policy-06.png)

### Post-authentication actions ###
When the administrator account was in use, the password will automatically reset and the account will be logged out (for example after 8 hours)
![](/images/00005-windows-laps/configure-laps-group-policy-05.png)

## Verify password change ##
After the 90 minute interval, the group policies will be updated automatically to enforce the use of LAPS.
When you want to check if the reset has been executed on the password, preform the following steps.

**Active Directory**
1. Open **Active Directory Users and Computers**.
2. Navigate to the **Organizational Unit** where the devices are active. 
3. **Double click** on the server and navigate to **LAPS**.
4. Within the properties you will see that the following fields are filled with the information.
* Current LAPS password expiration
* Set new LAPS password expiration
* LAPS local admin account name
* LAPS local admin account password
5. To see the actuall password, click on **Hide Password**.
![](/images/00005-windows-laps/verify-laps-password-01.png)


**PowerShell**
1. Open **PowerShell**
2. Enter the following command
{{< highlight html >}}
    PS C:\Users\Administrator> Get-LapsADPassword -identity "msvs-adca-22-01" -AsPlainText

        Computername        : MSVS-ADCA-22-01
        Distinguished Name  : CN=MSVS-ADCA-22-01,OU=Certificate Authority,OU=Servers,OU=benningshof-it,OU=Netherlands,OU=INFRA,DC=benningshof-it,DC=edu
        Account             : Administrator
        Password            : VerySecretPassword
        PasswordUpdateTime  : 10-3-2025 17:00:23
        ExpirationTimeStamp : 11-3-2025 17:00:23
        Source              : EncryptionPassword
        DecryptionStatus    : Success
        AuthorizedDecryptor : CORP\Domain Admins
{{< /highlight >}}

## How to reset Windows LAPS password ##

**Active Directory**
1. Open **Active Directory Users and Computers**.
2. Navigate to the **Organizational Unit** where the devices are active. 
3. **Double click** on the server and navigate to **LAPS**
4. Click on **Expire now**
![](/images/00005-windows-laps/change-laps-password-01.png)
5. Open the command-prompt on the device and preform **GPupdate /force**. When the group policies are updated, you can check if the password has been reset.
![](/images/00005-windows-laps/change-laps-password-02.png)

**PowerShell**

It's also possible to preform the password reset within PowerShell, for this you will need to preform the command on the server itself

{{< highlight html >}}
    PS C:\Users\Administrator> Reset-LapsPassword
{{< /highlight >}}


### Assign permissions to users or groups ###
Some accounts or groups might already be granted Extended Rights permission on the managed device's OU. This permission is problematic because it grants the ability to read confidential attributes (all of the Windows LAPS password attributes are marked as confidential). One way to check to see who is granted these permissions is by using the **Find-LapsADExtendedRights**.

{{< highlight html >}}
    PS C:\Users\Administrator> Find-LapsADExtendedRights -Identity OU=Servers,OU=benningshof-it,OU=Netherlands,OU=INFRA,DC=benningshof-it,DC=edu

    ObjectDN                                                                        ExtendedRightHolders
    --------                                                                        --------------------
    OU=Servers,OU=benningshof-it,OU=Netherlands,OU=INFRA,DC=benningshof-it,DC=edu   {NT AUTHORITY\SYSTEM, LAPS\Domain Admins}
{{< /highlight >}}

In the output, you will see which accounts and groups have access to the password that has been created through LAPS.

{{< highlight html >}}
    PS C:\Users\Administrator> Set-LapsAdResetPasswordPermissions -Identity "OU=Servers,OU=benningshof-it,OU=Netherlands,OU=INFRA,DC=benningshof-it,DC=edu" -AllowedPrincipals "CORP\GG_SEC_IT-Administrators"

    Name        DistinguishedName
    ----        -----------------
    Servers     OU=Servers,OU=benningshof-it,OU=Netherlands,OU=INFRA,DC=benningshof-it,DC=edu    
{{< /highlight >}}

When preforming the check, you can see that the security group that has been added is visible within **ExtendedRightHolders**

{{< highlight html >}}
    PS C:\Users\Administrator> Find-LapsAdExtendedRights -Identity "OU=Servers,OU=benningshof-it,OU=Netherlands,OU=INFRA,DC=benningshof-it,DC=edu"
   
    ObjectDN                                                                        ExtendedRightHolders
    --------                                                                        --------------------
    OU=Servers,OU=benningshof-it,OU=Netherlands,OU=INFRA,DC=benningshof-it,DC=edu   {NT AUTHORITY\SYSTEM, LAPS\Domain Admins, CORP\GG_SEC_IT-Administrators}
{{< /highlight >}}

As shown in the following image, you can see that the Security Group has the authorisations to preform the password reset.
![](/images/00005-windows-laps/give-securitygroup-permissions-01.png)

### Removing permissions to users or groups ###

To remove the authorizations within the Organizational Unit, will need to be preformed through **Active Directory Users and Computers** and cannot be preformed through PowerShell.

1. Open **Active Directory Users and Computers**.
2. Navigate to the **Organizational Unit**, right-click and open **Properties**.
3. Navigate to **Security**, select the **security group** and click on **Remove**.

![](/images/00005-windows-laps/remove-securitygroups-permissions-01.png)

## Conclusion ##

Within this blog, we have preformed the basic steps for the activation of LAPS within Active Directory. We have configure the basic settings, through the Group Policies and made sure that the local administrator account is more secure than with the default configuration. In short, this will contribute in the security of the domain and once configured, it will automatically preform the password changes to keep it secure.