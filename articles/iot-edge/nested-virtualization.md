---
title: Azure IoT Edge for Linux on Windows 的嵌套式虚拟化 | Microsoft Docs
description: 了解如何探索 Azure IoT Edge for Linux on Windows 中的嵌套式虚拟化。
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 7c50e813655603101da15931a6b3056d431f47b8
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108129524"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Azure IoT Edge for Linux on Windows 的嵌套式虚拟化
有两种形式的嵌套式虚拟化与 Azure IoT Edge for Linux on Windows 兼容。 用户可以选择通过本地 VM 或 Azure VM 进行部署。 本文将为用户阐明哪个选项最适合其方案，并提供配置要求的深入说明。

> [!NOTE]
>
> 确保为嵌套虚拟化启用一个[网络选项](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options)。 否则会导致 EFLOW 安装错误。 

## <a name="deployment-on-local-vm"></a>在本地 VM 上部署
这是适用于任何托管 Azure IoT Edge for Linux on Windows 的 Windows VM 的基线方法。 对于这种情况，需要在开始部署之前启用嵌套式虚拟化。 有关如何配置此方案的详细信息，请参阅[在使用嵌套式虚拟化的虚拟机中运行 Hyper-V](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)。

如果使用的是 Windows Server，请确保[安装 Hyper-V 角色](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)。

## <a name="deployment-on-azure-vms"></a>在 Azure VM 上部署
如果选择在 Azure VM 上部署，请注意，在设计上不会有外部交换机。 除非执行了一个可启动默认交换机的特定脚本，否则，Azure IoT Edge for Linux on Windows 在运行服务器 SKU 的 Azure VM 上也不兼容。 有关如何启动默认交换机的详细信息，请参阅下面的[“Windows Server”部分](#windows-server)。 

> [!NOTE]
>
> 任何要托管 EFLOW 的 Azure VM 都必须是[支持嵌套式虚拟化](../virtual-machines/acu.md)的 VM


## <a name="limited-use-of-external-switch"></a>使用外部交换机受到限制
在 VM 无法通过外部交换机获取 IP 地址的任何方案中，部署功能会自动将内部默认交换机用于部署。 这意味着，Azure IoT Edge for Linux VM 管理只能在目标设备本身上执行（即，只有在 localhost 上才可能通过 WAC PowerShell SSH 扩展连接到 Azure IoT Edge for Linux VM）。

## <a name="windows-server"></a>Windows Server
对于 Windows Server 用户，请注意，Azure IoT Edge for Linux on Windows 不自动支持默认交换机。

* 对本地 VM 产生的结果：请确保 EFLOW VM 可以通过外部交换机获取 IP 地址。

* 对 Azure VM 产生的结果：由于 Azure VM 上没有外部交换机，因此在服务器上设置内部交换机之前无法部署 EFLOW。

默认情况下，服务器 SKU 上没有默认交换机（无论服务器 SKU 是否在 Azure VM 上运行）。 在无法使用外部交换机的 Azure VM 上部署时，需要先手动设置并配置默认交换机，然后再进行 Azure IoT Edge for Linux on Windows 部署。 我们的部署功能之所以涵盖此操作，是因为它需要内部交换机的 IP 配置、NAT 配置，以及安装并配置 DHCP 服务器。 我们的公共预览版部署功能指出，它不会随意配置这些设置，目的是避免对生产部署上的网络配置造成影响。

* 在[如何在 Azure 虚拟机中启用嵌套式虚拟化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)中可以找到有关如何手动设置默认交换机的相关信息
* 在[使用 Windows PowerShell 部署 DHCP](/windows-server/networking/technologies/dhcp/dhcp-deploy-wps) 中可以找到有关如何为此方案设置 DHCP 服务器的文档