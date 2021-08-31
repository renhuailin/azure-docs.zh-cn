---
title: 监视 Azure Stack Edge Pro GPU 设备上 VM 的 CPU 和内存
description: 了解如何在 Azure 门户中监视 Azure Stack Edge Pro 设备上 VM 的 CPU 和内存指标。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: c58b7fd1cf4ab08dd8d645e4fc5f537d9ee937e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778536"
---
# <a name="monitor-vm-metrics-for-cpu-memory-on-azure-stack-edge-pro-gpu"></a>监视 Azure Stack Edge Pro GPU 上 VM 的 CPU 和内存指标

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何监视 Azure Stack Edge Pro GPU 设备上虚拟机的 CPU 和内存指标。

## <a name="about-vm-metrics"></a>关于 VM 指标

利用虚拟机的“指标”选项卡，你可以查看 CPU 和内存指标，调整时间段，并在相关时段内进行放大。

VM 指标基于从 VM 的来宾操作系统收集的 CPU 和内存使用情况数据。 资源使用率每分钟采样一次。

如果设备已断开连接，则将在设备上缓存指标。 重新连接设备时，将从缓存中推送指标，并更新 VM 指标。

## <a name="monitor-cpu-and-memory-metrics"></a>存储器 CPU 和内存指标

1. 在 Azure 门户中打开设备，转到“虚拟机”。 选择虚拟机，并选择“指标”。

    ![显示 Azure Stack Edge 设备上虚拟机的“指标”选项卡的屏幕截图。 突出显示“指标”选项卡。](media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-01.png)

2. 默认情况下，关系图显示前一小时的平均 CPU 和内存使用率。 若要查看不同时间段的数据，请选择“显示过去时段的数据”旁边的其他选项。

    ![Azure Stack Edge 设备上虚拟机的“指标”选项卡的屏幕截图。 突出显示“显示过去时段的数据”选项和所选值。](./media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-02.png)

3. 使用鼠标指向任一图表中的任意位置，以显示一条可向左或向右移动以查看更早或更晚数据示例的竖线。 单击以打开该时间段的详细信息视图。

    ![显示虚拟机的“指标”选项卡的屏幕截图。 将鼠标悬停在图表的某个区域时，显示的指针将突出显示。](./media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-03.png)


## <a name="next-steps"></a>后续步骤

- [监视设备上的 VM 活动](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)。
- [在支持包中收集 VM 来宾日志](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)。
