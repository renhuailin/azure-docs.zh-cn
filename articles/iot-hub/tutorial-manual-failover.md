---
title: 教程 - Azure IoT 中心的手动故障转移 | Microsoft Docs
description: 教程 - 了解如何对 IoT 中心手动执行到其他区域的故障转移并确认其正常运行，然后将其返回到原始区域并再次检查。
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 08/10/2021
ms.author: robinsh
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: 898cc8dcb634ed9beab26c0a0d5de9a9fdda068c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732866"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>教程：为 IoT 中心执行手动故障转移

手动故障转移是 IoT 中心服务的一项功能，允许客户将其中心的操作从主要区域[故障转移](https://en.wikipedia.org/wiki/Failover)到相应的 Azure 异地配对区域。 在出现区域性灾难或者服务中断时间延长的情况下，可以进行手动故障转移。 也可通过执行计划内故障转移来测试灾难恢复功能，虽然我们建议使用测试性 IoT 中心而不是在生产环境中运行的 IoT 中心。 对于 2017 年 5 月 18 日之后创建的 IoT 中心，提供给客户的手动故障转移功能不另外收费。

将在本教程中执行以下任务：

> [!div class="checklist"]
> * 使用 Azure 门户创建 IoT 中心。 
> * 执行故障转移。 
> * 查看在辅助位置运行的中心。
> * 执行故障回复，让 IoT 中心的操作返回到主位置。 
> * 确认中心在正确的位置正确运行。

有关使用 IoT 中心的手动故障转移和 Microsoft 发起的故障转移的详细信息，请参阅[跨区域灾难恢复](iot-hub-ha-dr.md#cross-region-dr)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 确保已在防火墙中打开端口 8883。 本教程中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="perform-a-manual-failover"></a>执行手动故障转移

> [!NOTE]
> 一个 IoT 中心每天的限制是两次故障转移和两次故障回复。

1. 单击“资源组”，然后选择相应资源组。 本教程使用 ManlFailRG。 在资源列表中单击你的中心。 

1. 在 IoT Hub 窗格上的“设置”下，单击“故障转移”。

   ![显示 IoT 中心属性窗格的屏幕截图](./media/tutorial-manual-failover/trigger-failover-01.png)

1. 在“手动故障转移”窗格上，可以看到 **当前位置** 和 **故障转移位置**。 当前位置始终指示中心当前处于活动状态的位置。 故障转移位置是标准的 [Azure 异地配对区域](../best-practices-availability-paired-regions.md)，与当前位置配对。 不能更改位置值。 在本教程中，当前位置为 `West US 2`，故障转移位置为 `West Central US`。

   ![显示“手动故障转移”窗格的屏幕截图](./media/tutorial-manual-failover/trigger-failover-02.png)

1. 在“手动故障转移”窗格顶部单击“启动故障转移”。 

1. 在“确认”窗格中填充 IoT 中心的名称，确认是它需要故障转移。 然后，若要启动故障转移，请单击“故障转移”。

   执行手动故障转移所需时间与中心的已注册设备数成正比。 例如，如果有 1 百万台设备，可能需要 15 分钟，但如果有 5 百万台设备，则可能需要 1 小时或更长的时间。

   ![显示“手动故障转移”确认窗格的屏幕截图](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   在手动故障转移进程正在运行时，会显示一个横幅，告诉你正在进行手动故障转移。 

   ![显示手动故障转移正在进行的屏幕截图](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   如果在关闭“IoT 中心”窗格后又在“资源组”窗格中通过单击来打开它，则会看到一个横幅，告诉你该中心正在进行手动故障转移。 

   ![显示 IoT 中心正在进行故障转移的屏幕截图](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   故障转移完成后，“手动故障转移”页上的当前区域和故障转移区域会互换，中心重新处于活动状态。 在本示例中，当前位置现在为 `WestCentralUS`，故障转移位置现在为 `West US 2`。 

   ![显示故障转移已完成的屏幕截图](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   “概述”页还显示一个横幅，指示故障转移已完成，并且 IoT 中心正在 `West Central US` 中运行。

   ![“概述”页中显示故障转移已完成的屏幕截图](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>执行故障回复 

执行手动故障转移以后，可以将中心的操作切换回原始的主要区域 -- 这称为故障回复。 如果刚执行故障转移，则需等待大约一小时，然后才能请求故障回复。 如果尝试在比这更短的时间内执行故障回复，则会显示错误消息。

故障回复的执行方式与手动故障转移一样。 下面是步骤： 

1. 若要执行故障回复，请返回到 Iot 中心的“Iot 中心”窗格。

2. 在 IoT Hub 窗格上的“设置”下，单击“故障转移”。 

3. 在“手动故障转移”窗格顶部单击“启动故障转移”。 

4. 在“确认”窗格中填充 IoT 中心的名称，确认是它需要故障回复。 然后，若要启动故障回复，请单击“确定”。 

   ![手动故障回复请求的屏幕截图](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   横幅会如“执行故障转移”部分所述一样显示。 故障回复完成之后，它会再次将 `West US 2` 显示为当前位置，将 `West Central US` 显示为故障转移位置，就像初始设置的一样。

## <a name="clean-up-resources"></a>清理资源 

若要删除为本教程创建的资源，请删除资源组。 此操作会一并删除组中包含的所有资源。 在本示例中，它会删除 IoT 中心和资源组本身。 

1. 单击“资源组”。 

2. 找到并选择资源组“ManlFailRG”。 单击可将其打开。 

3. 单击“删除资源组”。 系统提示时，请输入资源组的名称，然后单击“删除”进行确认。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何执行以下任务，以便配置并执行手动故障转移以及请求故障回复：

> [!div class="checklist"]
> * 使用 Azure 门户创建 IoT 中心。 
> * 执行故障转移。 
> * 查看在辅助位置运行的中心。
> * 执行故障回复，让 IoT 中心的操作返回到主位置。 
> * 确认中心在正确的位置正确运行。

请继续学习下一教程，了解如何从后端服务配置设备。 

> [!div class="nextstepaction"]
> [配置设备](tutorial-device-twins.md)
