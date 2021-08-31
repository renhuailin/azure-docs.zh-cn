---
title: Azure 德国映像 | Microsoft Docs
description: 本文概述了 Azure 德国市场中包含的映像
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: dd43ef428426037696fb2fcfdf6bac58c9b27dc4
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "117029100"
---
# <a name="azure-germany-images"></a>Azure 德国映像

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="overview"></a>概述
在 Azure 德国中部署新的虚拟服务器时，客户可以选择从 Microsoft 部署预创建的映像或上传自己的 VHD。 这种灵活性意味着，如果需要，可以部署自己的标准化映像。

## <a name="variations"></a>变体
Azure 德国目前尚不支持来自合作伙伴的市场映像。

下面是 Azure 德国市场中的可用映像列表。 请注意，每个映像可能具有不同的版本，但此处未列出。 一些预生成的映像包含特定软件的即用即付授权。 请阅读 [Azure 定价](https://azure.microsoft.com/pricing/)页面获得更多指南，并与 Microsoft 帐户团队或分销商协作。

## <a name="images-for-deployments-with-azure-resource-manager"></a>用于部署 Azure 资源管理器的映像

|发布者|产品/服务|SKU|版本|
| --- | --- | --- | --- |
| Canonical | UbuntuServer | 12.04.5-LTS | 12.04.201603150 12.04.201605160 12.04.201608290 12.04.201610201 12.04.201701100 |
| Canonical | UbuntuServer | 14.04.4-LTS | 14.04.201604060 14.04.201605160 14.04.201608300 |
| Canonical | UbuntuServer | 14.04.5-LTS | 14.04.201610200 14.04.201701100 |
| Canonical | UbuntuServer | 16.04-LTS | 16.04.201701130 |
| Canonical | UbuntuServer | 16.04.0-LTS | 16.04.201604203 16.04.201605161 16.04.201609071 16.04.201610200 |
| Canonical | UbuntuServer | 16.10 | 16.10.201701030 |
| cloudera | cloudera-centos-os | 6_7 | 1.0.1 |
| CoreOS | Container-Linux | Alpha |  |
| CoreOS | Container-Linux | Beta |  |
| CoreOS | Container-Linux | Stable |  |
| CoreOS | CoreOS | Alpha | 1068.0.0 1081.2.0 1097.0.0 1122.0.0 1151.0.0 1153.0.0 1164.0.0 1164.1.0 1180.0.0 1185.0.0 1192.0.0 1192.1.0 1192.2.0 1207.0.0 1214.0.0 1221.0.0 1235.0.0 1248.0.0 1248.1.0 1262.0.0 1284.1.0 1284.2.0 1298.1.0 1325.1.0 1339.0.0 1353.1.0 |
| CoreOS | CoreOS | Beta | 1010.4.0 1068.3.0 1081.3.0 1122.1.0 1153.3.0 1153.4.0 1185.1.0 1185.2.0 1192.2.0 1235.1.0 1235.2.0 1248.3.0 1248.4.0 1298.4.0 1325.2.0 1353.2.0 |
| CoreOS | CoreOS | Stable | 1010.5.0 1010.6.0 1068.10.0 1068.6.0 1068.8.0 1068.9.0 1122.2.0 1122.3.0 1185.3.0 1185.5.0 1235.12.0 1235.5.0 1235.6.0 1235.8.0 1298.5.0 1298.6.0 |
| credativ | Debian | 7 | 7.0.201604200 7.0.201606240 7.0.201606280 7.0.201609120 7.0.201611020 7.0.201701180 |
| credativ | Debian | 8 | 8.0.201604200 8.0.201606240 8.0.201606280 8.0.201609120 8.0.201611020 8.0.201701180 8.0.201703150 |
| credativ | Debian | 8-backports | 8.0.201702060 |
| credativ | Debian | 9-beta | 9.0.201702080 9.0.201703150 |
| GE-SRS-Prod-GalleryImages | Process-Server | Windows-2012-R2-Datacenter | 201703.01.00 |
| MicrosoftOSTC | FreeBSD | 10.3 | 10.3.20170112 |
| MicrosoftOSTC | FreeBSD | 11.0 | 11.0.20161223 11.0.20170111 |
| MicrosoftSharePoint | SharePoint2016 | SharePoint_Server_2016_Trial | 16.0.4351 |
| MicrosoftSQLServer | sql2014sp1-ws2012r2 | 企业 | 12.0.4100 |
| MicrosoftSQLServer | SQL2014SP2-WS2012R2 | 企业 | 12.0.50000 12.0.50001 |
| MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Express | 12.0.50000 |
| MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Standard | 12.0.50000 |
| MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Web | 12.0.50000 |
| MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | 企业 | 12.0.50000 |
| MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Standard | 12.0.50000 |
| MicrosoftSQLServer | SQL2016-WS2012R2 | 企业 | 13.0.31640 |
| MicrosoftSQLServer | SQL2016-WS2012R2 | Express | 13.0.31641 |
| MicrosoftSQLServer | SQL2016-WS2012R2 | SQLDEV | 13.0.31640 |
| MicrosoftSQLServer | SQL2016-WS2012R2 | Standard | 13.0.31640 |
| MicrosoftSQLServer | SQL2016-WS2012R2 | Web | 13.0.31640 |
| MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | 企业 | 13.0.21640 |
| MicrosoftSQLServer | SQL2016-WS2016 | 企业 | 13.0.21640 |
| MicrosoftSQLServer | SQL2016-WS2016 | SQLDEV | 13.0.21640 |
| MicrosoftSQLServer | SQL2016-WS2016 | Standard | 13.0.21640 |
| MicrosoftSQLServer | SQL2016-WS2016 | Web | 13.0.21640 |
| MicrosoftSQLServer | SQL2016-WS2016-BYOL | 企业 | 13.0.21640 |
| MicrosoftSQLServer | SQL2016-WS2016-BYOL | Standard | 13.0.21640 |
| MicrosoftSQLServer | SQL2016SP1-WS2016 | 企业 | 13.0.400110 |
| MicrosoftSQLServer | SQL2016SP1-WS2016 | Express | 13.0.400111 |
| MicrosoftSQLServer | SQL2016SP1-WS2016 | SQLDEV | 13.0.400110 |
| MicrosoftSQLServer | SQL2016SP1-WS2016 | Standard | 13.0.400110 |
| MicrosoftSQLServer | SQL2016SP1-WS2016 | Web | 13.0.400110 |
| MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | 企业 | 13.0.400110 |
| MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Standard | 13.0.400110 |
| MicrosoftVisualStudio | VisualStudio | VS-2015-Comm-AzureSDK-29-WS2012R2 | 2017.02.16 |
| MicrosoftVisualStudio | VisualStudio | VS-2015-Ent-VSU3-AzureSDK-29-WS2012R2 | 2017.02.16 |
| MicrosoftVisualStudio | VisualStudio | VS-2017-Ent-WS2016 | 2017.03.06 |
| MicrosoftVisualStudio | VisualStudio | VS-2017-RC3-Comm-WS2016 | 2017.02.14 |
| MicrosoftVisualStudio | VisualStudio | VS-2017-RC3-Ent-WS2016 | 2017.02.14 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | 2.0.20160125 2.0.20160229 2.0.20160430 2.0.20160617 2.0.20160721 2.0.20160812 2.0.20161214 2.0.20170110 2.0.20170316 2.127.20170406 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | 3.0.20160125 3.0.20160229 3.0.20160430 3.0.20160617 3.0.20160721 3.0.20160812 3.0.20161214 3.0.20170111 3.0.20170316 3.127.20170406 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | 4.0.20160125 4.0.20160229 4.0.20160430 4.0.20160617 4.0.20160721 4.0.20160812 4.0.20161012 4.0.20161214 4.0.20170111 4.0.20170316 4.127.20170406 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | 2016.0.20161010 2016.0.20161213 2016.0.20170314 2016.127.20170406 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-Server-Core | 2016.0.20170314 2016.127.20170406 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-with-Containers | 2016.0.20161012 2016.0.20161025 2016.0.20161213 2016.0.20170314 2016.127.20170406 |
| MicrosoftWindowsServer | WindowsServer | 2016-Nano-Server | 2016.0.20161012 2016.0.20161109 |
| MicrosoftWindowsServer | WindowsServer | 2016-Nano-Server-Technical-Preview |  |
| MicrosoftWindowsServer | WindowsServer | 2016-Technical-Preview-with-Containers |  |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview |  |
| OpenLogic | CentOS | 6.5 | 6.5.20170207 |
| OpenLogic | CentOS | 6.7 | 6.7.20160310 |
| OpenLogic | CentOS | 6.8 | 6.8.20160620 6.8.20161026 6.8.20170105 |
| OpenLogic | CentOS | 7.2 | 7.2.20160325 7.2.20160620 7.2.20161026 7.2.20161116 7.2.20170105 |
| OpenLogic | CentOS | 7.3 | 7.3.20161221 |
| RedHat | RHEL | 6.8 | 6.8.20161028 6.8.20161214 6.8.20170224 6.8.2017032020 |
| RedHat | RHEL | 6.9 | 6.9.2017032807 |
| RedHat | RHEL | 7.2 | 7.2.20161026 7.2.20170203 7.2.20170224 7.2.2017032020 |
| RedHat | RHEL | 7.3 | 7.3.20161104 7.3.20170202 7.3.20170224 7.3.2017032020 |
| RedHat | RHEL-SAP-APPS | 6.8 | 6.8.201703130 |
| RedHat | RHEL-SAP-APPS | 7.3 | 7.3.201703130 |
| RedHat | RHEL-SAP-HANA | 6.7 | 6.7.20170310 |
| RedHat | RHEL-SAP-HANA | 7.2 | 7.2.20170310 |
| SUSE | 基础结构 | SMT |  |
| SUSE | openSUSE-Leap | 42.1 | 2016.04.15 2016.11.21 |
| SUSE | openSUSE-Leap | 42.2 | 2017.01.24 2017.03.20 |
| SUSE | SLES | 11-SP4 | 2016.03.01 2016.08.12 2016.10.21 2016.12.21 2017.03.20 |
| SUSE | SLES | 12-SP1 | 2016.03.01 2016.08.11 2016.10.21 |
| SUSE | SLES | 12-SP2 | 2016.11.03 2017.03.20 |
| SUSE | SLES-BYOS | 11-SP4 | 2016.09.16 2017.03.20 |
| SUSE | SLES-BYOS | 12-SP2 | 2016.11.03 2017.03.20 |
| SUSE | SLES-SAP-BYOS | 12-SP1 | 2016.12.16 2017.03.20 |
| SUSE | SLES-SAP-BYOS | 12-SP2 | 2016.11.04 2017.03.20 |
| SUSE | SLES-SAPCAL | 11-SP4 |  |
| SUSE | SUSE-Manager-Proxy-BYOS | 3.0 | 2017.03.20 |
| SUSE | SUSE-Manager-Server-BYOS | 3.0 | 2017.03.20 |


## <a name="images-for-deployments-with-azure-service-manager-classic"></a>用于部署 Azure Service Manager（“经典”）的映像
| 发布服务器 | 映像名称 | OS |
|  ---  |  ---  |  ---  |
|  Credativ  |  Debian 7 "Wheezy"  |  Linux  |
|  Credativ  |  Debian 8 "Jessie"  |  Linux  |
|  Credativ  |  带后端口的 Debian 8“Jessie”  |  Linux  |
|  Credativ  |  Debian 9“Stretch”  |  Linux  |
|  Visual Studio  |  Windows Server 2012 R2 上的 Visual Studio Community 2015 Update 3 with Azure SDK 2.9  |  Windows  |
|  Visual Studio  |  Windows Server 2012 R2 上的 Visual Studio Enterprise 2015 Update 3 with Azure SDK 2.9  |  Windows  |
|  Visual Studio  |  Windows Server 2016 (x64) 上的 Visual Studio Enterprise 2017  |  Windows  |
|  Visual Studio  |  Windows Server 2016 (x64) 上的 Visual Studio Community 2017 RC  |  Windows  |
|  Visual Studio  |  Windows Server 2016 (x64) 上的 Visual Studio Enterprise 2017 RC  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2008 R2 SP1，2016 年 1 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2008 R2 SP1，2016 年 2 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2008 R2 SP1，2016 年 4 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2008 R2 SP1，2016 年 6 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2008 R2 SP1，2016 年 7 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2008 R2 SP1，2016 年 8 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2008 R2 SP1，2016 年 12 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2008 R2 SP1, March 2017  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2008 R2 SP1，2017 年 4 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 Datacenter，2016 年 1 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 Datacenter，2016 年 2 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 Datacenter，2016 年 4 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 Datacenter，2016 年 6 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 Datacenter，2016 年 7 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 Datacenter，2016 年 8 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 Datacenter，2016 年 12 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 Datacenter，2017 年 3 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 Datacenter，2017 年 4 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 R2 Datacenter，2016 年 1 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 R2 Datacenter，2016 年 2 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 R2 Datacenter，2016 年 4 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 R2 Datacenter，2016 年 6 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 R2 Datacenter，2016 年 7 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 R2 Datacenter，2016 年 8 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 R2 Datacenter，2016 年 10 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 R2 Datacenter，2016 年 12 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 R2 Datacenter，2017 年 3 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2012 R2 Datacenter，2017 年 4 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2016 Datacenter，2016 年 10 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2016 Datacenter，2016 年 12 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2016 Datacenter，2017 年 3 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2016 Datacenter，2017 年 4 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2016 Datacenter - Server Core，2017 年 3 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2016 Datacenter - Server Core，2017 年 4 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2016 - Nano Server，2016 年 10 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2016 - Nano Server，2016 年 12 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2016 Datacenter with Containers，2016 年 10 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2016 Datacenter with Containers，2016 年 12 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2016 Datacenter with Containers，2017 年 3 月  |  Windows  |
|  Microsoft Windows Server 产品组  |  Windows Server 2016 Datacenter with Containers，2017 年 4 月  |  Windows  |
|  SUSE  |  SUSE Manager 3.0 Proxy（自带订阅）  |  Linux  |
|  SUSE  |  SUSE Manager 3.0 Server（自带订阅）  |  Linux  |
|  SUSE  |  openSUSE Leap 42.2  |  Linux  |
|  SUSE  |  openSUSE Leap 42.1  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server 11 SP4（自带订阅）  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server 11 SP4  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server 12 SP1  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server 12 SP2（自带订阅）  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server 12 SP2  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server for SAP Applications 12 SP1（自带订阅）  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server for SAP Applications 12 SP1（高级映像）（自带订阅）  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server for SAP Applications 12 SP2（自带订阅）  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server for SAP Applications 12 SP2（高级映像）（自带订阅）  |  Linux  |
|  OpenLogic  |  OpenLogic 6.5  |  Linux  |
|  OpenLogic  |  OpenLogic 6.7  |  Linux  |
|  OpenLogic  |  OpenLogic 6.8  |  Linux  |
|  OpenLogic  |  OpenLogic 7.2  |  Linux  |
|  OpenLogic  |  OpenLogic 7.3  |  Linux  |
|  适用于 FreeBSD 的 Microsoft 开源技术中心  |  FreeBSD 10.3  |  Linux  |
|  适用于 FreeBSD 的 Microsoft 开源技术中心  |  FreeBSD 11.0  |  Linux  |
|  Microsoft SharePoint Server 产品组  |  SharePoint Server 2016 试用版  |  Windows  |
|  RedHat  |  Red Hat Enterprise Linux 6.7  |  Linux  |
|  RedHat  |  Red Hat Enterprise Linux 6.8  |  Linux  |
|  RedHat  |  Red Hat Enterprise Linux 6.9  |  Linux  |
|  RedHat  |  Red Hat Enterprise Linux 7.2  |  Linux  |
|  RedHat  |  Red Hat Enterprise Linux 7.3  |  Linux  |
|  coreos  |  CoreOS Alpha  |  Linux  |
|  coreos  |  CoreOS Beta  |  Linux  |
|  coreos  |  CoreOS Stable  |  Linux  |
|  Cloudera, Inc.  |  Cloudera CentOS 6.7  |  Linux  |
|  Canonical  |  Ubuntu Server 12.04.5-LTS  |  Linux  |
|  Canonical  |  Ubuntu Server 14.04.4-LTS  |  Linux  |
|  Canonical  |  Ubuntu Server 14.04.5-LTS  |  Linux  |
|  Canonical  |  Ubuntu Server 16.04 LTS  |  Linux  |
|  Canonical  |  Ubuntu Server 16.10  |  Linux  |
|  Microsoft SQL Server 产品组  |  SQL Server 2014 SP2 Enterprise Windows Server 2012 R2  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2012 R2 上的 SQL Server 2014 SP2 Express  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2012 R2 上的 SQL Server 2014 SP2 Standard  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2012 R2 上的 SQL Server 2014 SP2 Web  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2016 上的 SQL Server 2016 SP1 Developer  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2016 上的 SQL Server 2016 SP1 Enterprise  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2016 上的 SQL Server 2016 SP1 Express  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2016 上的 SQL Server 2016 SP1 Standard  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2016 上的 SQL Server 2016 SP1 Web  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2012 R2 上的 SQL Server 2016 Developer  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2016 上的 SQL Server 2016 RTM Developer  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2012 R2 上的 SQL Server 2016 RTM Enterprise  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2016 上的 SQL Server 2016 RTM Enterprise  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2012 R2 上的 SQL Server 2016 Express  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2012 R2 上的 SQL Server 2016 Standard  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2016 上的 SQL Server 2016 Standard  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2012 R2 上的 SQL Server 2016 Web  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2016 上的 SQL Server 2016 Web  |  Windows  |
|  Microsoft SQL Server 产品组  |  Windows Server 2012 R2 上的 SQL Server 2014 SP1 Enterprise  |  Windows  |
|  GE-SRS-Prod-GalleryImages  |  Microsoft Azure Site Recovery 进程服务器 V2  |  Windows  |



<!--Working with quickstart templates missing here. needs modification of quickstart repo -->



## <a name="next-steps"></a>后续步骤
若要在使用 Azure 德国时使用终结点发现任何编程差异，请参阅 [Azure 德国开发人员指南](./germany-developer-guide.md)。

有关从市场部署或创建自己的 VHD 的详细信息，请参阅以下资源：  

* [部署 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Windows 虚拟机 FAQ](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [创建 Linux VM 自定义映像](../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




