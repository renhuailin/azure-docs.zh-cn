---
author: robinsh
ms.author: robinsh
ms.topic: include
ms.date: 07/07/2021
ms.openlocfilehash: eacee8b325317e98cd05093b71a21889f9a933ed
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739247"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>将使用者组添加到 IoT 中心

[使用者组](../articles/event-hubs/event-hubs-features.md#event-consumers)提供事件流的独立视图，可让应用和 Azure 服务单独使用同一事件中心终结点内的数据。 在本部分中，需要将一个使用者组添加到 IoT 中心的内置终结点，本教程稍后部分将使用该组从该终结点拉取数据。

要将使用者组添加到 IoT 中心，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中打开 IoT 中心。

1. 在左侧窗格中，选择“内置终结点”。 在“使用者组”下的文本框中，输入新使用者组的名称。 

   :::image type="content" source="./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png" alt-text="在 IoT 中心创建使用者组":::

1. 单击文本框以外的任意位置以保存使用者组。