---
title: "How to Install Windows Server"
slug: "how-to-install-windows-server"
date: "2025-02-08T21:00:00"
author: "Kevin Benningshof"
summary: "Microsoft Windows Server is a popular operating system running in almost every company next to Linux. What if you want to install Windows Server within your home lab or within a organization? Most of the time they will use some kinds of template to make sure this can be deployed within just a few minutes. Within this tutorial, i will learn you the steps that are needed to create this Virtual Machine and install Windows Server 2022 on it running on Proxmox."
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

Microsoft Windows Server is used world wide to deliver the services that are running in the small, medium and big companies. But it is also possible to use it within, for example your personal homelab seperate to the production environment.

Within this post, we will make sure that Microsoft Windows Server 2022 is installed within Proxmox and that the required drivers for the communication between Windows and Proxmox is working correctly. Keep in notice, that this is within my personal environment and can be different in other environments

## Download Windows Server ##

From Microsoft it's possible to get one of the ISO's that is needed to perform the installation. Keep in mind, that the ones used for this post is the Evaluation version of Windows Server 2022

Click on one of the following links to start the proces 

* [Download Windows Server 2016](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016)
* [Download Windows Server 2019](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019)
* [Download Windows Server 2022](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2022)
* [Download Windows Server 2025](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2025)

## Download the ISO to Proxmox ##

* Within Proxmox, navigate to the location that is marked as the storage for **ISO Images**.
* When on the location, click on the button **Download from URL** and supply it with the required information

![](/images/00001-how-to-install-windows-server/how-to-download-windows-server-01.png)

* During the download procedure, you can see more information regarding the speed and percentage.

![](/images/00001-how-to-install-windows-server/how-to-download-windows-server-02.png)

## How to Windows Server ISO

* Within Proxmox, navigate to **Datacenter** > **Hostname** > **VMID (VMname)** > **Hardware**
* Select the **CD/DVD Drive** and click on **Edit**
* Select **Use CD/DVD disc image file (iso)** and select the **Storage** and the **ISO Image**
* As final step, click on **OK** to mount the **ISO**

![](/images/00001-how-to-install-windows-server/how-to-mount-windows-server-01.png)

## How to install Windows Server 2022 ##

1. When you have started the virtual machine, it will ask **Press any key to boot from the CD or DVD** to start the setup.
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-01.png)

2. Windows is now loading the files.
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-02.png)

3. Now select the **language to install**, **Time and currency format** / **Keyboard and imput method** and click on **Next**
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-03.png)

4. Click on **Install now**
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-04.png)

5. Select the version of the operating that you want to install (In my opinion, **Windows Server 2022 Standard Evaluation (Desktop Experience)** will be the best choice) and click on **Next**
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-05.png)

6. Check the **I Accept** button and click on **Next**
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-06.png)

7. Click on **Custom: Install Microsoft Server Operating System only (advanced)**
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-07.png)

8. Click on **Load driver**
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-08.png)

9. Within **Load driver**, click on **Browse**
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-09.png)

10. Navigate to the locations, where the drivers are located.
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-10.png)

11. Select **Red Hat VirtIO SCSI** and click on **Next**
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-11.png)

12. The drivers are currently being loaded
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-12.png)

13. When the import has completed, you will see the drives that is assigned within **Proxmox** to the **Virtual Machine**, now select the drive and click on **Next** to start the installation
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-13.png)

15. The installation of Windows will start and will provide you with the status.

- Copying Windows files
- Getting files ready for installation
- Installing features
- Installing updates
- Finishing up

![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-14.png)

16. When the installation is finished, you will need to enter a new password for the **Built-in Administrator** and click on **Finish**
![](/images/00001-how-to-install-windows-server/how-to-install-windows-server-15.png)

## Conclusion ##

Within this post, you have seen the steps that are needed to install **Windows Server** on the **Virtual Machine** within **Proxmox**.

