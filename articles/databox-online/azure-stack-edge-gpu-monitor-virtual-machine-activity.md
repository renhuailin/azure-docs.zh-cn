---
title: 监视 Azure Stack Edge Pro GPU 设备上的 VM 活动
description: 了解如何在 Azure 门户中监视 Azure Stack Edge Pro GPU 设备上的 VM 活动。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: a73337ad261d8d2d7aef2c6292c7387d7c4cad9a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778537"
---
# <a name="monitor-vm-activity-on-your-azure-stack-edge-pro-gpu-device"></a>监视 Azure Stack Edge Pro GPU 设备上的 VM 活动

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何在 Azure 门户中查看 Azure Stack Edge Pro GPU 设备上的虚拟机活动日志。

> [!NOTE]
> 可以在虚拟机的“指标”选项卡上放大 VM 活动期间的 CPU 和内存使用情况。 有关详细信息，请参阅[监视 VM 指标](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)。

## <a name="view-activity-logs"></a>查看活动日志

若要查看 Azure Stack Edge Pro GPU 设备上的虚拟机活动日志，请执行以下步骤：

1. 转到该设备，然后转到“虚拟机”。 选择“活动日志”。

    ![显示 Azure Stack Edge 设备上虚拟机的“活动日志”窗格的屏幕截图。 “活动日志”在导航窗格中突出显示。](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-01.png)

    你将看到设备上虚拟机的 VM 来宾日志。

1. 使用列表上方的筛选器来找到需要查看的活动。

    ![显示虚拟机的“活动日志”页上的“时间范围”选项的屏幕截图。 突出显示了所选时间范围。](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-02.png)<!--Reshoot to remove pointer. Lightbox treatment?-->

1. 按操作名称单击向下箭头，以查看关联的活动。

    ![显示 Azure Stack Edge 设备上虚拟机的“活动日志”页的屏幕截图。 展开的操作在列表中突出显示。](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-03.png)<!--Reshoot to remove pointer. May be able to replace drop-down only.-->

在 Azure 中的任何“活动日志”窗格上，可以对活动进行筛选和排序、选择要显示的列、向下钻取特定活动的详细信息，并获取过去 24 小时内的错误、失败部署、警报、服务运行状况和安全更改的“快速见解”。 有关日志和筛选选项的详细信息，请参阅[查看活动日志](../azure-resource-manager/management/view-activity-logs.md)。

## <a name="next-steps"></a>后续步骤

- [排查 VM 部署问题](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)。
- [在支持包中收集 VM 来宾日志](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)。