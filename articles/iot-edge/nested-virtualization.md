---
title: 用于 Azure IoT Edge for Linux on Windows 的嵌套虚拟化 | Microsoft Docs
description: 了解如何在 Azure IoT Edge for Linux on Windows 中导航嵌套虚拟化。
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0e0021ec3564ca079f9ab02fe5ed3f0cfa5a1560
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608456"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>用于 Azure IoT Edge for Linux on Windows 的嵌套虚拟化
有两种形式的嵌套虚拟化与 Azure IoT Edge for Linux on Windows 兼容。 用户可以选择通过本地 VM 或 Azure VM 进行部署。 本文将使用户清楚了解哪些选项最适合其场景，并深入了解配置要求。

> [!NOTE]
>
> 确保为嵌套虚拟化启用一个[网络选项](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options)。 否则将导致 EFLOW 安装错误。 

## <a name="deployment-on-local-vm"></a>在本地 VM 上部署
对于托管 Azure IoT Edge for Linux on Windows 的任何 Windows VM，这是基线方法。 对于这种情况，需要先启用嵌套虚拟化，然后才能开始部署。 有关如何配置此场景的详细信息，请阅读[使用嵌套虚拟化在虚拟机中运行 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)。

如果使用的是 Windows Server，请确保[安装 Hyper-V 角色](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)。

## <a name="deployment-on-azure-vms"></a>在 Azure VM 上部署
如果选择在 Azure VM 上部署，请注意，根据设计没有外部交换机。 除非执行特定脚本来打开默认交换机，否则在运行 Server SKU 的 Azure VM 上，Azure IoT Edge for Linux on Windows 也不兼容。 有关如何打开默认交换机的详细信息，请参阅下面的 [Windows Server 部分](#windows-server)。 

> [!NOTE]
>
> 任何应托管 EFLOW 的 Azure VM 都必须是[支持嵌套虚拟化](../virtual-machines/acu.md)的 VM


## <a name="limited-use-of-external-switch"></a>外部交换机使用受限
在 VM 无法通过外部交换机获得 IP 地址的场景中，部署功能会自动使用内部默认交换机进行部署。 这意味着，只能在目标设备本身上管理 Azure IoT Edge for Linux VM（例如，只能在 localhost 上通过 WAC PowerShell SSH 扩展连接到 Azure IoT Edge for Linux VM）。

## <a name="windows-server"></a>Windows Server
对于 Windows Server 用户，请注意，Azure IoT Edge for Linux on Windows 不会自动支持默认交换机。

* 本地 VM 的结果：确保 EFLOW VM 可以通过外部交换机获得 IP 地址。

* Azure VM 的结果：由于 Azure VM 上没有外部交换机，因此在服务器上设置内部交换机之前，无法部署 EFLOW。

默认情况下，Server SKU 上没有默认的交换机（无论 Server SKU 是否在 Azure VM 上运行）。 在无法使用外部交换机的 Azure VM 上部署时，需要先手动设置和配置默认交换机，然后才能部署 Azure IoT Edge for Linux on Windows。 我们的部署功能涵盖了这一点，因为它需要为内部交换机配置 IP、配置 NAT 以及安装和配置 DHCP 服务器。 公共预览版中的部署功能指出，它不会摆弄这些设置，也不会损坏生产部署上的网络配置。

* 有关如何手动设置默认开关的相关信息，请参阅[如何在 Azure 虚拟机中启用嵌套虚拟化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* 有关如何为此场景设置 DHCP 服务器的文档，请参阅[使用 Windows PowerShell 部署 DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)
