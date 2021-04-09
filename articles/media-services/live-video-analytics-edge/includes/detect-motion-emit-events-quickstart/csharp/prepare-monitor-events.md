---
ms.openlocfilehash: 882ba60e16f770651a1d9fe3b02b61be2b4c34c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99531704"
---
你将在 IoT Edge 模块上使用实时视频分析来检测传入的实时视频流中的运动并将事件发送到 IoT 中心。 若要查看这些事件，请执行以下步骤：

1. 在 Visual Studio Code 中打开“资源管理器”窗格，然后在左下角查找“Azure IoT 中心”。
1. 展开“设备”节点。
1. 右键单击“lva-sample-device”，然后选择“开始监视内置事件终结点” 。

    ![开始监视内置事件终结点](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> 系统可能会要求你提供 IoT 中心的内置终结点信息。 若要获取此信息，请在 Azure 门户中导航到 IoT 中心，然后在左侧导航窗格中查找“内置终结点”选项。 单击此处，在“与事件中心兼容的终结点”部分下查找“与事件中心兼容的终结点” 。 复制并使用框中的文本。 终结点将如下所示：  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
