---
title: Azure Service Fabric 版本
description: 了解主动支持的 Azure Service Fabric 和平台版本中的群集版本
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: 4eb08bb2ce94612ee81b9cdf19a1b3cd2e2d6b00
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729731"
---
# <a name="service-fabric-supported-versions"></a>受支持的 Service Fabric 版本
本文中的表概述了主动支持的 Service Fabric 和平台版本。

## <a name="windows"></a>Windows

| Service Fabric 运行时 |可直接从此版本升级|可降级到|兼容的 SDK 或 NuGet 包版本|支持的 .NET 运行时** |OS 版本。 |结束支持 |
| --- | --- | --- | --- | --- | --- | --- |
| 8.1 CU1 | 7.2 CU7 | 8.0 | 版本 5.1 或更低版本 | .NET 5.0 (GA)、>= .NET Core 2.1、 <br>全部 >= .NET Framework 4.5 | [请参阅受支持的 OS 版本](#supported-windows-versions-and-support-end-date) | 当前版本 |
| 8.1 RTO | 7.2 CU7 | 8.0 | 版本 5.1 或更低版本 | .NET 5.0 (GA)、>= .NET Core 2.1、 <br>全部 >= .NET Framework 4.5 | [请参阅受支持的 OS 版本](#supported-windows-versions-and-support-end-date) | 当前版本 |
| 8.0 CU3 | 7.1 CU10 | 7.2 | 版本 5.0 或更低版本 | .NET 5.0 (GA)、>= .NET Core 2.1、 <br>全部 >= .NET Framework 4.5 | [请参阅受支持的 OS 版本](#supported-windows-versions-and-support-end-date) | 2022 年 2 月 28 日 |
| 8.0 CU2 | 7.1 CU10 | 7.2 | 版本 5.0 或更低版本 | .NET 5.0 (GA)、>= .NET Core 2.1、 <br>全部 >= .NET Framework 4.5 | [请参阅受支持的 OS 版本](#supported-windows-versions-and-support-end-date) | 2022 年 2 月 28 日 |
| 8.0 CU1 | 7.1 CU10 | 7.2 | 版本 5.0 或更低版本 | .NET 5.0 (GA)、>= .NET Core 2.1、 <br>全部 >= .NET Framework 4.5 | [请参阅受支持的 OS 版本](#supported-windows-versions-and-support-end-date) | 2022 年 2 月 28 日 |
| 8.0 RTO | 7.1 CU10 | 7.2 | 版本 5.0 或更低版本 | .NET 5.0 (GA)、>= .NET Core 2.1、 <br>全部 >= .NET Framework 4.5 | [请参阅受支持的 OS 版本](#supported-windows-versions-and-support-end-date) | 2022 年 2 月 28 日 |
| 7.2 CU7 | 7.0 CU9 | 7.1 | 版本 4.2 或更低版本 | .NET 5.0（预览版支持）、>= .NET Core 2.1、<br>全部 >= .NET Framework 4.5 | [请参阅受支持的 OS 版本](#supported-windows-versions-and-support-end-date) | 2021 年 11 月 30 日 |
| 7.2 CU6 | 7.0 CU4 |7.1 | 版本 4.2 或更低版本 | .NET 5.0（预览版支持）、>= .NET Core 2.1、<br>全部 >= .NET Framework 4.5 | [请参阅受支持的 OS 版本](#supported-windows-versions-and-support-end-date)| 2021 年 11 月 30 日 |
| 7.2 RTO-CU5 | 7.0 CU4 | 7.1 |版本 4.2 或更低版本 | >= .NET Core 2.1、<br>全部 >= .NET Framework 4.5 | [请参阅受支持的 OS 版本](#supported-windows-versions-and-support-end-date)| 2021 年 11 月 30 日 |
| 7.1 |7.0 CU3 |不适用 | 版本 4.1 或更低版本 | >= .NET Core 2.1、<br>全部 >= .NET Framework 4.5 | [请参阅受支持的 OS 版本](#supported-windows-versions-and-support-end-date) | 2021 年 7 月 31 日 |

** Service Fabric 不提供 .NET Core 运行时。 由服务作者负责确保其<a href="/dotnet/core/deploying/">可用</a>。

## <a name="supported-windows-versions-and-support-end-date"></a>受支持的 Windows 版本和支持结束日期
当对 OS 版本的支持到达其生命周期终止日期时，对特定 OS 上 Service Fabric 的支持随之结束。


### <a name="windows-server"></a>Windows Server

| OS 版本 | Service Fabric 支持结束日期 | OS 生命周期链接 |
|---|---|---|
|Windows Server 2019|2029/1/9|<a href="/lifecycle/products/windows-server-2019">Windows Server 2019 - Microsoft 生命周期</a>|
|Windows Server 2016 |2027/1/12|<a href="/lifecycle/products/windows-server-2016">Windows Server 2016 - Microsoft 生命周期</a>|
|Windows Server 2012 R2 |2023/10/10|<a href="/lifecycle/products/windows-server-2012-r2">Windows Server 2012 R2 - Microsoft 生命周期</a>|
|版本 20H2 |2022/5/10|<a href="/lifecycle/products/windows-server">Windows Server - Microsoft 生命周期</a>|
|版本 2004 |2021/12/14|<a href="/lifecycle/products/windows-server">Windows Server - Microsoft 生命周期</a>|
|版本 1909 |2021/5/11|<a href="/lifecycle/products/windows-server">Windows Server - Microsoft 生命周期</a>|

<br>

### <a name="windows-10"></a>Windows 10

| OS 版本 | Service Fabric 支持结束日期 | OS 生命周期链接 |
| --- | --- | --- |
| Windows 10 2019 LTSC | 2029/1/9 | <a href="/lifecycle/products/windows-10-ltsc-2019">Windows 10 2019 LTSC - Microsoft 生命周期</a> |
| 版本 20H2 | 2023/5/9 | <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 企业版和教育版 - Microsoft 生命周期</a> |
| 版本 2004 | 2021/12/14| <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 企业版和教育版 - Microsoft 生命周期</a> |
| 版本 1909 | 2022/5/10 | <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 企业版和教育版 - Microsoft 生命周期</a> |
| 版本 1809 | 2021/5/11 | <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 企业版和教育版 - Microsoft 生命周期</a> |
| 版本 1803 | 2021/5/11 | <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 企业版和教育版 - Microsoft 生命周期</a> |

## <a name="linux"></a>Linux

| Service Fabric 运行时 | 可直接从此版本升级 |可降级到 |兼容的 SDK 或 NuGet 包版本 | 支持的 .NET 运行时** | OS 版本 | 结束支持 |
| --- | --- | --- | --- | --- | --- | --- |
| 8.1 CU1 | 7.2 CU7 | 8.0 | 版本 5.1 或更低版本 | >= .NET Core 2.1 | [请参阅受支持的 OS 版本](#supported-linux-versions-and-support-end-date) | 当前版本 |
| 8.1 RTO | 7.2 CU7 | 8.0 | 版本 5.1 或更低版本 | >= .NET Core 2.1 | [请参阅受支持的 OS 版本](#supported-linux-versions-and-support-end-date) | 当前版本 |
| 8.0 CU3 | 7.1 CU8 | 7.2 | 版本 5.0 或更低版本 | >= .NET Core 2.1 | [请参阅受支持的 OS 版本](#supported-linux-versions-and-support-end-date) | 2022 年 2 月 28 日 |
| 8.0 CU1 | 7.1 CU8 | 7.2 | 版本 5.0 或更低版本 | >= .NET Core 2.1 | [请参阅受支持的 OS 版本](#supported-linux-versions-and-support-end-date) | 2022 年 2 月 28 日 |
| 8.0 RTO | 7.1 CU8 | 7.2 | 版本 5.0 或更低版本 | >= .NET Core 2.1 | [请参阅受支持的 OS 版本](#supported-linux-versions-and-support-end-date) | 2022 年 2 月 28 日 |
| 7.2 CU7 | 7.0 CU9 | 7.1 | 低于或等于版本 4.2 | >= .NET Core 2.1 | [请参阅受支持的 OS 版本](#supported-linux-versions-and-support-end-date) | 2021 年 11 月 30 日 |
| 7.2 RTO-CU6 | 7.0 CU4 | 7.1 | 低于或等于版本 4.2 | >= .NET Core 2.1 | [请参阅受支持的 OS 版本](#supported-linux-versions-and-support-end-date) | 2021 年 11 月 30 日 |
| 7.1 | 7.0 CU3 | 不适用 | 低于或等于版本 4.1 | >= .NET Core 2.1 | [请参阅受支持的 OS 版本](#supported-linux-versions-and-support-end-date) | 2021 年 7 月 31 日 |

** Service Fabric 不提供 .NET Core 运行时，由服务作者负责确保其<a href="/dotnet/core/deploying/">可用</a>

## <a name="supported-linux-versions-and-support-end-date"></a>受支持的 Linux 版本和支持结束日期
当对 OS 版本的支持到达其生命周期终止日期时，对特定 OS 上 Service Fabric 的支持随之结束。

#### <a name="ubuntu"></a>Ubuntu
| OS 版本 | Service Fabric 支持结束日期| OS 生命周期链接 |
| --- | --- | --- |
| Ubuntu 18.04 | 2028 年 4 月 | <a href="https://wiki.ubuntu.com/Releases">Ubuntu 生命周期</a>|
| Ubuntu 16.04 | 2024 年 4 月 | <a href="https://wiki.ubuntu.com/Releases">Ubuntu 生命周期</a>|

## <a name="service-fabric-version-name-and-number-reference"></a>Service Fabric 版本名称和版本号参考
下表列出了 Service Fabric 的版本名称以及相应的版本号。

| 版本名称 | Windows 版本号 | Linux 版本号 |
| --- | --- | --- |
| 8.1 CU1 | 8.1.321.9590 | 8.1.323.1 |
| 8.1 RTO | 8.1.316.9590 | 8.1.320.1 |
| 8.0 CU3 | 8.0.536.9590 | 8.0.527.1 |
| 8.0 CU2 | 8.0.521.9590 | NA |
| 8.0 CU1 | 8.0.516.9590 | 8.0.515.1 | 
| 8.0 RTO | 8.0.514.9590 | 8.0.513.1 | 
| 7.2 CU7 | 7.2.477.9590 | 7.2.476.1 |
| 7.2 CU6 | 7.2.457.9590 | 7.2.456.1 |
| 7.2 CU7 | 7.2.477.9590 | 7.2.476.1 |
| 7.2 CU5 | 7.2.452.9590 | 7.2.454.1 |
| 7.2 CU4 | 7.2.445.9590 | 7.2.447.1 |
| 7.2 CU3 | 7.2.433.9590 | NA |
| 7.2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7.2 RTO | 7.2.413.9590 | NA |
| 7.1 CU10 | 7.1.510.9590 | NA |
| 7.1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7.1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7.1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7.1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7.1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7.1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7.1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7.0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7.0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7.0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 6.5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 6.5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6.5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.4 CU8 | 6.4.670.9590 | 不适用|
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU6 | 6.4.658.9590 | 不适用|
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU2 | 6.4.622.9590 | 不适用|
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.1 CU3 | 6.1.472.9494 | 不适用|
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 5.7 CU4 | 5.7.221.9494 | 不适用|
| 5.7 RTO | 5.7.198.9494 | 不适用|
| 5.6 CU3 | 5.6.220.9494 | 不适用|
| 5.6 CU2 | 5.6.210.9494 | 不适用|
| 5.6 RTO | 5.6.204.9494 | 不适用|
| 5.5 CU4 | 5.5.232.0 | 不适用|
| 5.5 CU3 | 5.5.227.0 | 不适用|
| 5.5 CU2 | 5.5.219.0 | 不适用|
| 5.5 CU1 | 5.5.216.0    | 不适用|
| 5.4 CU2 | 5.4.164.9494 | 不适用|
| 5.3 CU3 | 5.3.311.9590 | 不适用|
| 5.3 CU2 | 5.3.301.9590 | 不适用|
| 5.3 CU1 | 5.3.204.9494 | 不适用|
| 5.3 RTO | 5.3.121.9494 | 不适用|
