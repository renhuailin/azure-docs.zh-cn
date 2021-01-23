---
title: 替换 Azure Stack Edge Pro 设备 |Microsoft Docs
description: 描述如何获取 Azure Stack Edge Pro 设备的替换。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 91cb446da31f353162f90778d855056a9697d455
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726560"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>替换 Azure Stack Edge Pro 设备

本文介绍如何替换 Azure Stack Edge Pro 设备。 如果现有设备有硬件故障或需要升级，则需要替换设备。 


在本文中，学习如何：

> [!div class="checklist"]
>
> * 为硬件问题打开支持票证
> * 在 Azure 门户中为更换设备创建新订单
> * 安装，激活更换设备
> * 返回原始设备

## <a name="open-a-support-ticket"></a>打开支持票证

如果现有设备有硬件故障，请按照以下步骤操作以打开支持票证：

1. 向 Microsoft 支持部门打开支持票证，表示你希望退回设备。 选择 **Azure Stack Edge Pro 硬件** 问题类型，并选择 " **硬件问题** " 子类型。  

    ![创建支持票证](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Microsoft 支持部门工程师将与你联系，以确定 (FRU) 的现场更换单元是否可以解决该问题并可用于此实例。 如果 FRU 不可用或设备需要硬件升级，则支持人员会指导你设置新订单并返回旧设备。

## <a name="create-a-new-order"></a>创建新订单

按照 [创建新资源](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)中的步骤，创建用于激活替代设备的新资源。

> [!NOTE]
> 不支持对现有资源激活替代设备。 新资源被视为新的订单。 你将在设备寄送到你后开始支付14天的费用。

## <a name="install-and-activate-the-replacement-device"></a>安装并激活更换设备

请按照以下步骤安装和激活更换设备：

1. [安装设备](azure-stack-edge-deploy-install.md)。
2. 针对之前创建的新资源[激活设备](azure-stack-edge-deploy-connect-setup-activate.md)。

## <a name="return-your-existing-device"></a>返回现有设备

按照所有步骤操作，退回原始设备：

1. [清除设备上的数据](azure-stack-edge-return-device.md#erase-data-from-the-device)。
2. 启动原始设备的[设备返回](azure-stack-edge-return-device.md#initiate-device-return)。
3. [安排取件](azure-stack-edge-return-device.md#schedule-a-pickup)。
4. 在 Microsoft 接收到设备后，可以删除与返回的设备关联 [的资源](azure-stack-edge-return-device.md#delete-the-resource) 。


## <a name="next-steps"></a>后续步骤

- 了解如何 [返回 Azure Stack Edge Pro 设备](azure-stack-edge-return-device.md)。
