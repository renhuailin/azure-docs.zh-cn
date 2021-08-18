---
title: 如何使用 Azure 门户调用 Azure 视频分析器的直接方法
description: 本文概述如何使用 Azure 门户调用 Azure 视频分析器的直接方法。
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 944819e70d94b365a8f7c3e885ffc0175d84eca7
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114603860"
---
# <a name="use-azure-portal-to-invoke-direct-methods-for-azure-video-analyzer"></a>使用 Azure 门户调用 Azure 视频分析器的直接方法

借助 IoT 中心，你可以从云中对边缘设备调用[直接方法](../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules)。 Azure 视频分析器（视频分析器）公开多个[直接方法](./direct-methods.md)，这些方法可用于定义、部署和激活不同的管道以分析实时视频。

在本文中，你将了解如何通过 Azure 门户对视频分析器模块调用直接方法调用。

## <a name="prerequisites"></a>先决条件

* 你使用[快速入门：视频分析器](get-started-detect-motion-emit-events.md)中所述的方法或通过[门户](./deploy-iot-edge-device.md)在边缘设备上运行视频分析器模块。
* 你了解[视频分析器](overview.md)和[管道概念](pipeline.md)。

## <a name="invoking-direct-methods-via-azure-portal"></a>通过 Azure 门户调用直接方法

视频分析器公开的每个[直接方法](./direct-methods.md)都可以通过 Azure 门户进行调用。 下面的步骤提供了调用一种直接方法的详细操作。 你可以使用类似的步骤调用其他直接方法。 但是，每个直接方法都需要特定的有效负载。

使用 `livePipelineList` 方法调用检索视频分析器模块上当前部署的所有管道拓扑的列表。 使用以下步骤来调用此直接方法：

1. 登录 Azure 门户
1. 从门户主页找到相关的资源组来定位 IoT 中心，或者如果你知道自己的 IoT 中心，请选择它。
    ![门户主页中的资源组](./media/use-azure-portal-to-invoke-direct-methods/portal-rg-home.png)
1. 在 IoT 中心页上，选择“自动设备管理”下的“IoT Edge”以列出各个设备 ID。 选择相关设备 ID 以列出该设备上运行的模块。
    ![iot 中心页](./media/use-azure-portal-to-invoke-direct-methods/iot-hub-page.png)
1. 选择视频分析器模块以打开其配置页面。<br><br>
    ![选择视频分析器模块以打开其配置页面](./media/use-azure-portal-to-invoke-direct-methods/modules.png)
1. 选择“直接方法”菜单选项。 <br><br>
    ![单击“直接方法”菜单选项](./media/use-azure-portal-to-invoke-direct-methods/module-details.png)
    > [!NOTE]
    > 如当前页面所示，你可能需要在连接字符串部分中添加一个值。 你无需隐藏或更改“设置名称”部分中的任何内容。 这些内容可以公开。

1. 在“方法名称”字段中，键入“livePipelineList”。
1. 将下面的 JSON 复制并粘贴到“有效负载”字段中。
    ```json
    {
    "@apiVersion": "1.0"
    }
    ```
1. 选择页面顶部的“调用方法”按钮。<br><br>
    ![“调用方法”按钮](./media/use-azure-portal-to-invoke-direct-methods/direct-method.png)
1. 你应该会在“结果”区域中看到一条状态 200 消息。<br><br>
    ![连接超时](./media/use-azure-portal-to-invoke-direct-methods/connection-timeout.png)

## <a name="responses"></a>响应

| 条件             | 状态代码 | 详细错误代码 |
|-----------------------|-------------|---------------------|
| Success               | 200         | 空值                 |
| 一般用户错误   | 400 range   |                     |
| 一般服务器错误 | 500 range   |                     |

## <a name="next-steps"></a>后续步骤

可在[直接方法](./direct-methods.md)页上找到更多直接方法。

> [!NOTE]
> 管道可激活特定拓扑，因此在激活管道之前，请确保已设置正确的拓扑。

[快速入门：检测运动并发出事件](detect-motion-emit-events-quickstart.md)是很好的参考文档，它可以帮助你了解要进行的直接方法调用的确切顺序。
