---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: fbb4e53e0047b9768a70c01aecfb7f31ae213b3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026342"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>将使用者组添加到 IoT 中心

[使用者组](../articles/event-hubs/event-hubs-features.md#event-consumers)提供事件流的独立视图，可让应用和 Azure 服务单独使用同一事件中心终结点内的数据。 在本部分中，需要将一个使用者组添加到 IoT 中心的内置终结点，本教程稍后部分将使用该组从该终结点拉取数据。

要将使用者组添加到 IoT 中心，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中打开 IoT 中心。

2. 在左窗格中选择“内置终结点”，在右窗格中选择“事件”，在“使用者组”下面输入名称  。 选择“保存”。

   ![在 IoT 中心创建使用者组](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)