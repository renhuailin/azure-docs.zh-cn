---
title: 重置并重新激活 Azure Stack Edge Pro 设备 |Microsoft Docs
description: 了解如何从中擦除数据，然后重新激活 Azure Stack Edge Pro 设备。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2020
ms.author: alkohli
ms.openlocfilehash: 4026bac9818b14c33c05d99caff4052adad196c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744509"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-pro-device"></a>重置并重新激活 Azure Stack Edge Pro 设备

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何重置、重新配置和重新激活 Azure Stack Edge Pro 设备（如果你遇到设备问题，或者出于其他某种原因需要重新启动）。

将设备重置为 "删除数据" 后，需要将设备作为新资源重新激活。 重置设备会删除设备配置，因此需要通过本地 web UI 重新配置设备。

在本文中，学习如何：

> [!div class="checklist"]
>
> * 擦除设备上数据磁盘中的数据
> * 通过创建新订单重新配置设备，重新配置设备并激活设备

## <a name="reset-data-from-the-device"></a>从设备重置数据

若要擦除设备上数据磁盘中的数据，需要重置设备。 

重置之前，请根据需要创建设备上本地数据的副本。 可将设备中的数据复制到 Azure 存储容器。

>[!IMPORTANT]
> 重置设备将擦除设备中的所有本地数据和工作负荷，并且无法逆转。 仅当要在设备上启动不用重新时重置设备。

可以在本地 web UI 或 PowerShell 中重置设备。 有关 PowerShell 说明，请参阅 [重置设备](./azure-stack-edge-connect-powershell-interface.md#reset-your-device)。

[!INCLUDE] [重置设备中的数据](../../includes/azure-stack-edge-device-reset.md)

## <a name="reactivate-device"></a>重新激活设备

重置设备后，需要将设备作为新资源重新激活。 放置新订单后，需要重新配置并重新激活新资源。

若要重新激活现有设备，请执行以下步骤：

1. 按照 [创建新资源](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#create-a-new-resource)中的步骤，为现有设备创建新订单。 在 " **发货地址** " 选项卡上，选择 " **我已有设备**"。

   ![不在送货地址中指定新设备](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [获取激活密钥](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#get-the-activation-key)。

1. [连接到设备](azure-stack-edge-gpu-deploy-connect.md)。

1. [为设备配置网络](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。

1. [配置设备设置](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)。

1. [配置证书](azure-stack-edge-gpu-deploy-configure-certificates.md)。

1. [激活设备](databox-online/azure-stack-edge-gpu-deploy-activate.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何 [连接到 Azure Stack Edge Pro 设备](azure-stack-edge-gpu-deploy-connect.md)。
