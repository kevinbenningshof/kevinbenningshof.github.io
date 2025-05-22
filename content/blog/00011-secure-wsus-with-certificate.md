---
title: "Secure WSUS with PKI certificate"
slug: "secure-wsus-with-pki-certificate"
date: "2025-05-18T19:00:00"
author: "Kevin Benningshof"
summary: "Using SSL with WSUS is crucial for security and ensuring a reliable update process. By using SSL, WSUS encrypts the metadata passed between clients and the WSUS server, protecting it from interception. This prevents a man-in-the-middle attack, where an attacker could intercept and potentially alter update information. Additionally, SSL helps authenticate the WSUS server to clients, confirming its legitimacy. "
tags: [
    "Microsoft",
    "windows Server",
    "Windows Server Update Services",
    "PKI",
    "TLS/SSL"
]
categories: [ "blog" ]
thumbnail: "/images/blog/00011/windows-server-2022.png"
toc: true
draft: false
featured: true
---

# Introduction
Using SSL with WSUS is crucial for security and ensuring a reliable update process. By using SSL, WSUS encrypts the metadata passed between clients and the WSUS server, protecting it from interception. This prevents a man-in-the-middle attack, where an attacker could intercept and potentially alter update information. Additionally, SSL helps authenticate the WSUS server to clients, confirming its legitimacy. 

## 1. Installation of Windows Server Update Services

1. Open **Internet Information Services (IIS) Manager** through the start menu.
2. Within **IIS**, select the server and then double click on **Server Certificates** within **IIS**
![](/images/00011-secure-wsus-with-certificate/request-ssl-cert-01.png)
3. Within **Server Ceritificates**, click on Create **Domain Certificate**.
![](/images/00011-secure-wsus-with-certificate/request-ssl-cert-02.png)
4. Enter the information that is required and click on **Next**.
![](/images/00011-secure-wsus-with-certificate/request-ssl-cert-03.png)
5. Click on **Select..**
![](/images/00011-secure-wsus-with-certificate/request-ssl-cert-04.png)
6. Select the **Certificate Authority**, that needs to create the certificate and click on **OK**
![](/images/00011-secure-wsus-with-certificate/request-ssl-cert-05.png)
7. Enter the **friendly name** of the certificate and click on **Finish**
![](/images/00011-secure-wsus-with-certificate/request-ssl-cert-06.png)
8. As we can see, the certificate has been requested and issued to the server by the certificate authority.
![](/images/00011-secure-wsus-with-certificate/request-ssl-cert-07.png)

## 2. Configure SSL certificate within WSUS

1. Within **Internet Information Services (IIS) Manager**, navigate to **Connections** > **hostname** > **Sites** > **WSUS Administration**
2. Within **WSUS Administration**, navigate to **Actions** and click on **Bindings..**
![](/images/00011-secure-wsus-with-certificate/assign-ssl-cert-01.png)
3. Select **HTTPS** and click on **Edit...**
![](/images/00011-secure-wsus-with-certificate/assign-ssl-cert-02.png)
4. Within **SSL certificate**, change it from **Not selected** to **WSUS SSL** and click on **OK** to apply the settings.
![](/images/00011-secure-wsus-with-certificate/assign-ssl-cert-03.png)
5. Now expand the WSUS Administration option and preform the following steps for **ApiRemoting30**, **ClientWebService**, **DssAuthWebService**, **ServerSyncWebServices** and **SimpleAuthWebService**.
6. Select the **Application** and then double click on **SSL Settings**.
![](/images/00011-secure-wsus-with-certificate/assign-ssl-cert-04.png)
7. Within **SSL Settings**, enable **Require SSL** and click on **Apply** and repeat this within the other applications.
![](/images/00011-secure-wsus-with-certificate/assign-ssl-cert-06.png)
8. Open the **command prompt** or **PowerShell** and navigate to the directory **C:\Program Files\Update Services\Tools**
{{< highlight html >}}
    PS C:\Windows\System32> cd 'C:\Program Files\Update Services\Tools'
    PS C:\Program Files\Update Services\Tools> .\wsusutil.exe configuressl servername.domainname.edu
{{< /highlight >}}
9. When this has been configured, you will see the following output
{{< highlight html >}}
    URL: https://servername.domainname.edu:8531
{{< /highlight >}}
10. As confirmation, open **Windows Server Update Services** and select the server. Within **Overview**, we can see that the port has been changed from **8530** to **8531**
![](/images/00011-secure-wsus-with-certificate/assign-ssl-cert-20.png)

## 2. Edit the Group Policy
1. Open **Group Policy Management**, **right-click** on the **Group Policy Object** that is configured for WSUS and click on **Edit...**
![](/images/00011-secure-wsus-with-certificate/edit-existing-gpo-01.png)
2. Navigate to **Computer Configuration** > **Policies** > **Administrative Templates** > **Windows Components** > **Windows Update** and open the policy named **Specify intranet Microsoft update service location**
![](/images/00011-secure-wsus-with-certificate/edit-existing-gpo-02.png)
3. Now change the following information **Set the intranet update service for detection updates** and **Set the intranet statics server** 
> * Set the intranet update service for detection updates : **https://msvs-wsus-22-01.benningshof-it.edu:8531**
> * Set the intranet statics server : **https://msvs-wsus-22-01.benningshof-it.edu:8531**

![](/images/00011-secure-wsus-with-certificate/edit-existing-gpo-03.png)


## 4. Check the updated group policy
1. to ensure the group policy is applied, open the **command prompt** or **PowerShell** and run **gpupdate /force**
![](/images/00011-secure-wsus-with-certificate/check-applied-gpo-01.png)
2. To check which settings have change through Group Policy Management, enter the command **rsop.msc** which will create 
![](/images/00011-secure-wsus-with-certificate/check-applied-gpo-02.png)
3. Navigate to **Computer Configuration** > **Policies** > **Administrative Templates** > **Windows Components** > **Windows Update** and open the policy named **Specify intranet Microsoft update service location** to verify if the settings have been applied.
![](/images/00011-secure-wsus-with-certificate/check-applied-gpo-03.png)

## Conclusion ##
Within this blog, we have learned which steps are needed to request, create and apply the certificate for Windows Server Update Services and to ensure that the servers communicate through SSL with eachother with critical information. This ensures that Windows Server Update Services is a bit more secure than the default configuration.