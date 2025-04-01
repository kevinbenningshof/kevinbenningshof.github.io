---
title: "Enforce synchronization of Microsoft Entra Connect"
slug: "enforce-sync-of-entra-connect"
date: "2025-03-30T21:00:00"
author: "Kevin Benningshof"
summary: "Changes have been applied to several AD objects within Active Directory and this needs to be applied directory. Within this we will preform the check, apply the change and enforce the sync."
tags: [
    "Microsoft",
    "windows Server",
    "Entra"
]
categories: [ "Blog" ]
thumbnail: "/images/blog/00008/microsoft-entra-connect.png"
toc: true
draft: false
featured: true
---

# Introduction
It can be that certain information needs to be changed within the on-premises object in the domain. By default Microsoft Entra Connect will preform the synchronization with Entra in a cycle of 30 minutes. This can be seen within Synchronization Service Manager when the last sync has been preformed. Within this post, we can enforce the synchronization within the 30 minute time window to apply the changes.

## Check the ADSync module
First, we will need to check if the module is present so we can execute the commands for the ADSync PowerShell module with **Get-Module**
![](/images/00008-enforce-sync-of-entra-connect/check-adsync-module-01.png)

By default, when Microsoft Entra Connect has been installed the PowerShell module should be present. Which is not the case in my environment.
To import the PowerShell module, we can preform the following command which will import the module.

{{< highlight html >}}
    PS C:\Windows\system32> Import-Module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync" -Verbose
{{< /highlight >}}
![](/images/00008-enforce-sync-of-entra-connect/check-adsync-module-02.png)

## Check for the last sync status
To preform the check of the scheduler, we can check when the last sync has been preformed through Microsoft Entra Connect. For this we can preform the **Get-ADSyncScheduler** command.
{{< highlight html >}}
    PS C:\Windows\system32> Get-ADSyncScheduler

    AllowedSyncCycleInterval            : 00:30:00
    CurrentlyEffectiveSyncCycleInterval : 00:30:00
    CustomizedSyncCycleInterval         :
    NextSyncCyclePolicyType             : Delta
    NextSyncCycleStartTimeInUTC         : 24-3-2025 22:03:04
    PurgeRunHistoryInterval             : 7.00:00:00
    SyncCycleEnabled                    : True
    MaintenanceEnabled                  : True
    StagingModeEnabled                  : False
    SchedulerSuspended                  : False
    SyncCycleInProgress                 : False
{{< /highlight >}}

## Enforcing the synchronization cycle
With the **Start-ADSyncSyncCycle** it is possible to preform two different commands for the Delta or the Full sync through Microsoft Entra Connect

**Start-ADSyncSyncCycle -PolicyType Delta**
* This will preform the synchronization between Microsoft Entra Sync and Azure with the changes that have been preformed after the last sync. For example, when the spell correction has been preformed and it needs to be applied immediately.

**Start-ADSyncSyncCycle -PolicyType Initial**
* This will preform the synchronization between Microsoft Entra Sync and Azure and check every AD Objects and apply the changes. For example, when a major change has been preformed and it needs to be synched with Azure immediately to correct the error on multiple objects.

## Check the Microsoft Entra Connect Sync status
The sync status, can be checked by detail through the application **Synchronization Service Manager** 
When we enforce the synchronization, we will see that this is in progress.
![](/images/00008-enforce-sync-of-entra-connect/enforce-adsync-01.png)

## Change the Sync Cycle Interval
By default the Sync Cycle Interval is set to 30 minutes, this can be changed to for example 15 minutes. 

{{% notice note "Syntax" %}}
* Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss
{{% /notice %}}

With the following command, we will change the sync interval to every 15 minutes.
{{< highlight html >}}
    PS C:\Windows\system32> Set-ADSyncScheduler -CustomizedSyncCycleInterval 00:15:00
{{< /highlight >}}


## Changing the Scheduler status
This command will disable the sync schedule and can be usefull if you need to preform a major change on-prem and want to temporarily disable the synchronization to Azure.

{{< highlight html >}}
    PS C:\Windows\system32> Set-ADSyncScheduler -SyncCycleEnabled $false

    AllowedSyncCycleInterval            : 00:30:00
    CurrentlyEffectiveSyncCycleInterval : 00:30:00
    CustomizedSyncCycleInterval         : 00:15:00
    NextSyncCyclePolicyType             : Delta
    NextSyncCycleStartTimeInUTC         : 24-3-2025 22:03:04
    PurgeRunHistoryInterval             : 7.00:00:00
    SyncCycleEnabled                    : False
    MaintenanceEnabled                  : True
    StagingModeEnabled                  : False
    SchedulerSuspended                  : False
    SyncCycleInProgress                 : False
{{< /highlight >}}

This command will enable the sync schedule.
{{< highlight html >}}
    PS C:\Windows\system32> Set-ADSyncScheduler -SyncCycleEnabled $true

    AllowedSyncCycleInterval            : 00:30:00
    CurrentlyEffectiveSyncCycleInterval : 00:30:00
    CustomizedSyncCycleInterval         : 00:15:00
    NextSyncCyclePolicyType             : Delta
    NextSyncCycleStartTimeInUTC         : 24-3-2025 22:03:04
    PurgeRunHistoryInterval             : 7.00:00:00
    SyncCycleEnabled                    : True
    MaintenanceEnabled                  : True
    StagingModeEnabled                  : False
    SchedulerSuspended                  : False
    SyncCycleInProgress                 : False
{{< /highlight >}}

## Conclusion ##
When changes need to be applied immidiatly, it's possible to preform the specific command to apply the changes in Entra through Microsoft Entra Connect.
These are handy when it comes to the creation accounts of a load of changes in one sync moment.