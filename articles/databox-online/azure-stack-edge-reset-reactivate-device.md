---
title: Azure Stack Edge 设备重置和重新激活
description: 了解如何从 Azure Stack Edge 设备中擦除数据，然后重新激活该设备。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/05/2021
ms.author: alkohli
ms.openlocfilehash: 4afe7235cb4122e4c4fe7e26fff4b34504663892
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460592"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-device"></a>重置并重新激活 Azure Stack Edge 设备

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何重置、重新配置和重新激活 Azure Stack Edge 设备（如果该设备出现问题，或者出于其他某种原因需要重新启动）。

重置设备以删除数据后，需要将设备作为新资源重新激活。 重置设备会删除设备配置，因此需要通过本地 Web UI 重新配置设备。

例如，可能需要将现有的 Azure Stack Edge 资源移动到新的订阅。 为此，需要：

1. 按照[重置设备](#reset-device)中的步骤重置设备上的数据。
2. 创建将新订阅与现有设备一同使用的新资源，然后激活该设备。 按照[重新激活设备](#reactivate-device)中的步骤操作。

## <a name="reset-device"></a>重置设备

若要擦除设备上数据磁盘中的数据，需要重置设备。

重置之前，请根据需要创建设备上本地数据的副本。 可将设备中的数据复制到 Azure 存储容器。

>[!IMPORTANT]
> 重置设备将擦除设备中的所有本地数据和工作负载，并且无法撤销。 仅当要重新启动设备时，才重置设备。

你可以在本地 Web UI 或 PowerShell 中重置设备。 有关 PowerShell 说明，请参阅[重置设备](./azure-stack-edge-connect-powershell-interface.md#reset-your-device)。

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

## <a name="reactivate-device"></a>重新激活设备

重置设备后，需要将设备作为新资源重新激活。 下新订单后，需要重新配置并重新激活新资源。

若要重新激活现有设备，请执行以下步骤：

1. 按照[创建新资源](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)中的步骤，为现有设备创建新订单。 在“送货地址”选项卡上，选择“我已有设备” 。

   ![在送货地址中不指定新设备](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [获取激活密钥](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#get-the-activation-key)。

1. [连接到设备](azure-stack-edge-gpu-deploy-connect.md)。

1. [为设备配置网络](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。

1. [配置设备设置](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)。

1. [配置证书](azure-stack-edge-gpu-deploy-configure-certificates.md)。

1. [激活设备](azure-stack-edge-gpu-deploy-activate.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何[连接到 Azure Stack Edge 设备](azure-stack-edge-gpu-deploy-connect.md)。
