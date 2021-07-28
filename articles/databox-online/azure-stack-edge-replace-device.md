---
title: Azure Stack Edge 设备更换
description: 说明如何获取 Azure Stack Edge 设备更换品。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c3c029826ccd4430f935b63b74bc958ad8de4c1e
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460626"
---
# <a name="replace-your-azure-stack-edge-device"></a>更换 Azure Stack Edge 设备

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文说明如何更换 Azure Stack Edge 设备。 如果现有设备有硬件故障或需要升级，则需要替换设备。 


在本文中，学习如何：

> [!div class="checklist"]
>
> * 针对硬件问题开具支持票证
> * 在 Azure 门户中为更换的设备创建新订单
> * 安装更换的设备并将其激活
> * 退回原始设备

## <a name="open-a-support-ticket"></a>打开支持票证

如果现有设备发生硬件故障，请按照以下步骤开具支持票证：

1. 向 Microsoft 支持部门打开支持票证，表示你希望退回设备。 选择“Azure Stack Edge 硬件”问题类型，并选择“硬件问题”子类型 。  

    ![创建支持票证](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Microsoft 支持部门工程师将与你联系，以确定现场更换单元 (FRU) 是否可以解决该问题并且可用于此实例。 如果 FRU 不可用或设备需要硬件升级，则支持人员将指导你下新订单并退回旧设备。

## <a name="create-a-new-order"></a>创建新订单

按照[创建新资源](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)中的步骤操作，创建用于激活更换设备的新资源。

> [!NOTE]
> 不支持针对现有资源激活更换的设备。 新资源被视为新订单。 自设备发货后开始计费，计费周期为 14 天。

## <a name="install-and-activate-the-replacement-device"></a>安装更换的设备并将其激活

请按照以下步骤安装和激活更换的设备：

1. [安装设备](azure-stack-edge-deploy-install.md)。
2. 针对之前创建的新资源[激活设备](azure-stack-edge-deploy-connect-setup-activate.md)。

## <a name="return-your-existing-device"></a>退回你的现有设备

按照所有步骤操作，退回原始设备：

1. [清除设备上的数据](azure-stack-edge-return-device.md#erase-data-from-the-device)。
2. 开始原始设备的[设备退回](azure-stack-edge-return-device.md#initiate-device-return)。
3. [安排取件](azure-stack-edge-return-device.md#schedule-a-pickup)。
4. 在 Microsoft 收到设备后，你就可以[删除与退回的设备关联的资源](azure-stack-edge-return-device.md#delete-the-resource)。


## <a name="next-steps"></a>后续步骤

- 了解如何[退回 Azure Stack Edge 设备](azure-stack-edge-return-device.md)。
