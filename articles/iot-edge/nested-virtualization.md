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
ms.openlocfilehash: e583808b0be0ff4105abc438ace1b52b9d3317eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726505"
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

除非执行脚本来打开默认交换机，否则 Azure IoT Edge for Linux on Windows 在运行 Server SKU 的 Azure VM 上不兼容。 若要详细了解如何打开默认交换机，请参阅[在 Windows 创建适用于 Linux 的虚拟交换机](how-to-create-virtual-switch.md)。

> [!NOTE]
>
> 任何要托管 EFLOW 的 Azure VM 都必须是[支持嵌套式虚拟化](../virtual-machines/acu.md)的 VM
