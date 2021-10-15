---
title: 支持的操作系统、容器引擎 - Azure IoT Edge
description: 了解可以运行 Azure IoT Edge 守护程序和运行时的操作系统以及生产设备支持的容器引擎
author: kgremban
ms.author: kgremban
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fd970e80f2b091d17c40b15d6c55699721fb1025
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129349665"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 支持的系统

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

本文详述 IoT Edge（不管是正式版还是预览版）支持的系统和组件。

## <a name="get-support"></a>获取支持

如果在使用 Azure IoT Edge 服务时遇到问题，可以通过多种方式来寻求支持。 请尝试通过以下某个渠道来寻求支持：

**报告 bug** – 涉及 Azure IoT Edge 产品的大多数开发都是在 IoT Edge 开放源代码项目中进行的。 可以在项目的[问题页面](https://github.com/azure/iotedge/issues)上报告 bug。 与 Azure IoT Edge for Linux on Windows 相关的 bug 可以在 [iotedge-eflow 问题页](https://github.com/azure/iotedge-eflow/issues)上报告。 修复很快就会从项目实施到产品更新中。

**Microsoft 客户支持团队** – 拥有 [支持计划](https://azure.microsoft.com/support/plans/)的用户可以通过直接从 [Azure 门户](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)创建支持票证来与 Microsoft 客户支持团队进行沟通。

**功能请求** – Azure IoT Edge 产品通过产品的 [User Voice 页面](https://feedback.azure.com/forums/907045-azure-iot-edge)跟踪功能请求。

## <a name="container-engines"></a>容器引擎

Azure IoT Edge 模块是以容器形式实现的，因此 IoT Edge 需要容器引擎来启动这些模块。 Microsoft 提供了容器引擎 moby-engine 来满足此要求。 该容器引擎基于 Moby 开源项目。 Docker CE 和 Docker EE 是其他常用的容器引擎。 它们也基于 Moby 开放源代码项目并且与 Azure IoT Edge 兼容。 Microsoft 对使用那些容器引擎的系统提供尽力而为的支持；但是，Microsoft 不能为其中的问题提供修复。 因此，Microsoft 建议在生产系统上使用 moby-engine。

<br>
<center>

![作为容器运行时的 Moby 引擎](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>操作系统

Azure IoT Edge 在大多数可以运行容器的操作系统上运行；但是，并非所有这些系统都受到同等支持。 操作系统分组为各个层级，这些层级表示用户可以预期的支持级别。

* 支持第 1 层系统。 对于第 1 层系统，Microsoft：
  * 将此操作系统包括在自动化测试中
  * 为它们提供安装程序包
* 第 2 层系统与 Azure IoT Edge 兼容并且可以相对容易地使用。 对于第 2 层系统：
  * Microsoft 已在平台上进行了非正式测试，或者知道合作伙伴已在平台上成功运行 Azure IoT Edge
  * 适用于其他平台的安装程序包在这些平台上可能会正常工作

主机 OS 系列必须始终与模块容器内部使用的来宾 OS 系列相匹配。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
换言之，在 Linux 上只能使用 Linux 容器，在 Windows 上只能使用 Windows 容器。 使用 Windows 容器时，仅支持进程隔离的容器，而不支持 Hyper-V 隔离的容器。  

IoT Edge for Linux on Windows 使用 Windows 主机上运行的 Linux 虚拟机中的 IoT Edge。 通过这种方式，可以在 Windows 设备上运行 Linux 模块。
:::moniker-end
<!-- end 1.1 -->

### <a name="tier-1"></a>第 1 层

下表中列出的系统（不管是公开发布版还是公共预览版）受 Microsoft 的支持，并在每个新版本中进行了测试。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Azure IoT Edge 支持构建为 Linux 或 Windows 容器的模块。 可以将 Linux 容器部署到 Linux 设备，或使用 IoT Edge for Linux on Windows 将 Linux 容器部署到 Windows 设备。 Windows 容器只能部署到 Windows 设备。
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Azure IoT Edge 版本 1.2 仅支持构建为 Linux 容器的模块。

目前不支持在 Windows 设备上运行 IoT Edge 版本 1.2。 [Windows 上的 IoT Edge for Linux](iot-edge-for-linux-on-windows.md) 是在 Windows 设备上运行 IoT Edge 的建议方法，但目前仅运行 IoT Edge 1.1。 有关详细信息，请参阅本文的 [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) 版本。

:::moniker-end
<!-- end 1.2 -->

#### <a name="linux-containers"></a>Linux 容器

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
构建为 Linux 容器的模块可以部署到 Linux 或 Windows 设备。 对于 Linux 设备，IoT Edge 运行时会直接安装在主机设备上。 对于 Windows 设备，使用 IoT Edge 运行时预生成的 Linux 虚拟机会在主机设备上运行。

建议使用 [IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) 在 Windows 设备上运行 IoT Edge。

| 操作系统 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/support/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/support/green-check.png) |  | 公共预览版 |
| Windows 10 专业版 | ![Windows 10 Pro + AMD64](./media/support/green-check.png) |  |  |
| Windows 10 企业版 | ![Windows 10 Enterprise + AMD64](./media/support/green-check.png) |  |  |
| Windows 10 IoT 企业版 | ![Windows 10 IoT Enterprise + AMD64](./media/support/green-check.png) |  |  |
| Windows Server 2019 | ![Windows Server 2019 + AMD64](./media/support/green-check.png) |  |  |

所有 Windows 操作系统都必须是 1809 版（内部版本 17763）或更高版本。
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

| 操作系统 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/support/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/support/green-check.png) |  | 公共预览版 |

:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>Ubuntu Server 16.04 支持将在 IoT Edge 版本 1.1 发布时结束。

#### <a name="windows-containers"></a>Windows 容器

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
>[!IMPORTANT]
>IoT Edge 1.1 LTS 是最后一个支持 Windows 容器的发布通道。 从版本 1.2 开始，将不再支持 Windows 容器。 请考虑使用或移动至 [IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) 以在 Windows 设备上运行 IoT Edge。

构建为 Windows 容器的模块只能部署到 Windows 设备。

| 操作系统 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT 企业版 | ![check1](./media/support/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/support/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/support/green-check.png) |  |  |

所有 Windows 操作系统都必须是 1809 版（内部版本 17763）。 Windows 的这一特定内部版本是 Windows 上的 IoT Edge 所必需的，因为 Windows 容器的版本必须与主机 Windows 设备的版本完全匹配。 Windows 容器当前只使用内部版本 17763。

>[!NOTE]
>Windows 10 IoT 核心版支持将在 IoT Edge 版本 1.1 发布时结束。
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge 1.1 LTS 是最后一个支持 Windows 容器的发布通道。 从版本 1.2 开始，将不再支持 Windows 容器。

有关 Windows 容器支持的操作系统的信息，请参阅本文的 [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) 版本。

:::moniker-end
<!-- end 1.2 -->

### <a name="tier-2"></a>第 2 层

下表中列出的系统被视为兼容 Azure IoT Edge，但未由 Microsoft 进行积极测试或维护。

| 操作系统 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS-7](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7) | ![CentOS + AMD64](./media/support/green-check.png) | ![CentOS + ARM32v7](./media/support/green-check.png) | ![CentOS + ARM64](./media/support/green-check.png) |
| [Ubuntu 20.04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/support/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/support/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/support/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/support/green-check.png) | ![Debian 9 + ARM32v7](./media/support/green-check.png) | ![Debian 9 + ARM64](./media/support/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/support/green-check.png) | ![Debian 10 + ARM32v7](./media/support/green-check.png) | ![Debian 10 + ARM64](./media/support/green-check.png) |
| [Debian 11](https://www.debian.org/releases/bullseye/) | ![Debian 11 + AMD64](./media/support/green-check.png) | ![Debian 11 + ARM32v7](./media/support/green-check.png) | ![Debian 11 + ARM64](./media/support/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/support/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/support/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/support/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/support/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/support/green-check.png) |
| [RHEL 7](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7) | ![RHEL 7 + AMD64](./media/support/green-check.png) | ![RHEL 7 + ARM32v7](./media/support/green-check.png) | ![RHEL 7 + ARM64](./media/support/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/support/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/support/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/support/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/support/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/support/green-check.png) | ![Yocto + ARM32v7](./media/support/green-check.png) | ![Yocto + ARM64](./media/support/green-check.png) |
| Raspberry Pi OS Buster |  | ![Raspberry Pi OS Buster + ARM32v7](./media/support/green-check.png) | ![Raspberry Pi OS Buster + ARM64](./media/support/green-check.png) |

<sup>1</sup> [安装或卸载 Azure IoT Edge for Linux](how-to-install-iot-edge.md) 中的 Ubuntu Server 18.04 安装步骤应在 Ubuntu 20.04 上无任何更改的情况下有效。

## <a name="releases"></a>发行版本

IoT Edge 发行资产和发行说明在 [azure-iotedge 版本](https://github.com/Azure/azure-iotedge/releases)页上提供。 本部分介绍这些发行说明中的信息，让你更轻松地直观显示每个版本的组件。

下表列出了每个版本（以 1.2.0 开始）中包含的组件。 可以单独安装或更新此表中列出的组件，并且它们与旧版本中的组件向后兼容。

| 发布 | aziot-edge | edgeHub<br>edgeAgent | aziot-identity-service |
| ------- | ---------- | -------------------- | ---------------------- |
| **1.2** | 1.2.0<br>1.2.1<br>1.2.2<br>1.2.3 | 1.2.0<br>1.2.1<br>1.2.2<br> 1.2.3  | 1.2.0<br>1.2.1<br>1.2.2<br><br> |

下表列出了每个版本（最高为 1.1 LTS 版本）中包含的组件。 可以单独安装或更新此表中列出的组件，并且它们与旧版本中的组件向后兼容。

IoT Edge 1.1 是首个长期支持 (LTS) 发布通道。 此版本未引入任何新功能，但会收到对回归进行的安全更新和修复。 IoT Edge 1.1 LTS 使用 .NET Core 3.1，且有效支持期至 2022 年 12 月 3 日，以匹配 [.NET Core 和 .NET 5 发布生命周期](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。

| 发布 | iotedge | edgeHub<br>edgeAgent | libiothsm | moby |
|--|--|--|--|--|
| 1\.1 LTS | 1.1.0<br>1.1.1<br><br>1.1.3<br>1.1.4<br>1.1.5<br>1.1.6 | 1.1.0<br>1.1.1<br>1.1.2<br>1.1.3<br>1.1.4<br>1.1.5<br><br> | 1.1.0<br>1.1.1<br><br>1.1.3<br>1.1.4<br>1.1.5<br>1.1.6 |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4（ARMv7hl，CentOS）<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

>[!IMPORTANT]
>随着长期支持渠道的发布，我们建议所有当前运行 1.0.x 的客户将其设备升级到 1.1.x，以获得持续的支持。

IoT Edge 使用 Microsoft.Azure.Devices.Client SDK。 有关详细信息，请参阅 [Azure IoT C# SDK GitHub 存储库](https://github.com/Azure/azure-iot-sdk-csharp)或 [Azure SDK for .NET 参考内容](/dotnet/api/overview/azure/iot/client)。 以下列表显示了用于测试每个版本的客户端 SDK 版本：

| IoT Edge 版本 | Microsoft.Azure.Devices.Client SDK 版本 |
|------------------|--------------------------------------------|
| 1.2.0            | 1.33.4-NestedEdge
| 1.1 (LTS)        | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>虚拟机

Azure IoT Edge 可在虚拟机中运行。 当客户想要利用边缘智能增强现有的基础结构时，经常会使用虚拟机作为 IoT Edge 设备。 主机 VM OS 系列必须与模块容器内部使用的来宾 OS 系列相匹配。 直接在设备上运行 Azure IoT Edge 时，同样要满足此要求。 Azure IoT Edge不区分底层虚拟化技术，将在以 Hyper-V 和 vSphere 等平台为后盾的 VM 中工作。

<br>

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

<center>

![VM 中的 Azure IoT Edge](./media/support/edge-on-vm-with-windows.png)

</center>

::: moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

<center>

![VM 中的 Azure IoT Edge](./media/support/edge-on-vm.png)

</center>

:::moniker-end

## <a name="minimum-system-requirements"></a>最低系统要求

在 Raspberry Pi3 等小型设备以及服务器级硬件上，Azure IoT Edge 都能非常顺利地运行。 哪种硬件适合方案取决于想要运行的工作负荷。 在设备方面做出最终决策可能比较复杂；但是，可以在传统的便携式计算机或台式机上轻松开始设计解决方案的原型。

在设计原型期间获得的经验可以帮助你在设备方面做出最终的选择。 应考虑的问题包括：

* 工作负荷中有多少个模块？
* 模块的容器共享多少层？
* 你的模块采用什么语言编写？
* 模块将处理多少数据？
* 模块是否需要使用任何专用硬件来加速其工作负荷？
* 所需的解决方案性能特点有哪些？
* 硬件预算是多少？
