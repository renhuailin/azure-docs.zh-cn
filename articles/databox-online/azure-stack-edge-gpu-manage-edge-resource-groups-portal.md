---
title: 管理 Azure Stack Edge Pro GPU 设备上的 Edge 资源组
description: 了解如何通过 Azure 门户管理 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 设备上的 Edge 资源组。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: c77fb42579af8d1e4bad7e55746ad36d16c983b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778615"
---
# <a name="manage-edge-resource-groups-on-azure-stack-edge-pro-gpu-devices"></a>管理 Azure Stack Edge Pro GPU 设备上的 Edge 资源组

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Edge 资源组包含创建和部署虚拟机期间通过本地 Azure 资源管理器在设备上创建的资源。 这些本地资源可能包括虚拟机、VM 映像、磁盘、网络接口和其他资源类型（例如 Edge 存储帐户）。

本文介绍如何查看和删除 Azure Stack Edge Pro GPU 设备上的 Edge 资源组。

## <a name="view-edge-resource-groups"></a>查看 Edge 资源组

按照以下步骤查看当前订阅的 Edge 资源组。

1. 转到设备上的“虚拟机”，然后转到“资源”窗格 。 选择“Edge 资源组”。

    ![Azure Stack Edge 设备上虚拟机的“资源”视图的屏幕截图。 “Edge 资源组”选项卡将突出显示。](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-01.png)

    > [!NOTE]
    > 在设备上设置 Azure 资源管理器环境后，可以在 Azure Powershell 中使用 [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-6.1.0&preserve-view=true) 获取相同的列表。 有关详细信息，请参阅[连接到 Azure 资源管理器](azure-stack-edge-gpu-connect-resource-manager.md)。


## <a name="delete-an-edge-resource-group"></a>删除 Edge 资源组

按照以下步骤删除不再使用的 Edge 资源组。

> [!NOTE]
> - 要删除的资源组必须是空的。 
> - 不能删除 ASERG 资源组。 该资源组用于存储在设备上启用计算时自动创建的 ASEVNET 虚拟网络。

1. 转到设备上的“虚拟机”，然后转到“资源”窗格 。 选择“Edge 资源组”。

    ![该屏幕截图显示 Azure Stack Edge 设备上虚拟机的“资源”视图。 “Edge 资源组”选项卡将突出显示。](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-01.png)

1. 选择要删除的资源组。 在该资源组的最右侧，选择删除图标（垃圾桶）。

   仅当资源组不包含任何资源时，才会显示删除图标。

    ![Azure Stack Edge 设备上 VM 的“资源”视图中“Edge 资源组”选项卡的屏幕截图。 资源组旁边的垃圾桶图标指示可将其删除。 该图标已突出显示。](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-02.png)

1. 你将看到一条消息，要求确认删除该 Edge 资源组。 该操作不可逆。 选择 **“是”** 。

    ![VM 的“资源”视图中“Edge 资源组”选项卡的屏幕截图。 突出显示的垃圾桶图标指示可以删除对应的资源组。](./media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-03.png)

    删除操作完成后，将从列表中删除该资源组。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理 Azure Stack Edge Pro GPU 设备，请参阅[使用本地 Web UI 管理 Azure Stack Edge Pro GPU](azure-stack-edge-manage-access-power-connectivity-mode.md)。

- [在设备上设置 Azure 资源管理器环境](azure-stack-edge-gpu-connect-resource-manager.md)。