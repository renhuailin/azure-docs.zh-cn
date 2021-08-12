---
title: 快速入门：连接设备，将遥测数据发送至 Azure IoT Central
description: 本快速入门向设备开发人员展示如何安全地将设备连接到 Azure IoT Central。 你将使用适用于 C、C#、Python、Node.js 或 Java 的 Azure IoT 设备 SDK 在模拟设备上运行客户端应用，然后连接到 IoT Central 并发送遥测数据。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: quickstart
ms.date: 04/27/2021
ms.collection: embedded-developer, application-developer
zone_pivot_groups: iot-develop-set1
ms.openlocfilehash: 376d26a591fb2cbe0d33fb9bdc261bcb535203e5
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712948"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central"></a>快速入门：将来自设备的遥测数据发送到 Azure IoT Central

**适用对象**：[设备应用程序开发人员](about-iot-develop.md#device-application-development)

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-develop-send-telemetry-central-c](../../includes/iot-develop-send-telemetry-central-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-develop-send-telemetry-central-csharp](../../includes/iot-develop-send-telemetry-central-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-develop-send-telemetry-central-java](../../includes/iot-develop-send-telemetry-central-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [iot-develop-send-telemetry-central-nodejs](../../includes/iot-develop-send-telemetry-central-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-develop-send-telemetry-central-python](../../includes/iot-develop-send-telemetry-central-python.md)]

:::zone-end

## <a name="view-telemetry"></a>查看遥测数据
模拟设备在连接到 IoT Central 之后，将开始发送遥测数据。 你可以在 IoT Central 中查看有关连接的设备的遥测数据和其他详细信息。 

在 IoT Central 中，选择 **设备**，单击设备名称，然后选择 **概述** 选项卡。此视图显示两个恒温器设备的温度图。

:::image type="content" source="media/quickstart-send-telemetry-central/iot-central-telemetry-output-overview.png" alt-text="IoT Central 设备遥测数据概述":::

选择 **原始数据** 选项卡。此视图显示每次发送恒温器读数时的遥测数据。

:::image type="content" source="media/quickstart-send-telemetry-central/iot-central-telemetry-output-raw.png" alt-text="IoT Central 设备遥测原始输出":::

设备现已建立安全连接，并在向 Azure IoT 发送遥测数据。
    
## <a name="clean-up-resources"></a>清理资源
如果不再需要本快速入门中创建的 IoT Central 资源，可以将其删除。 或者，如果你打算继续学习本指南中的文档，可以保留你创建的应用程序，以将其重复用于其他示例。

若要删除 Azure IoT Central 示例应用程序及其所有设备和资源，请执行以下操作：
1. 选择“管理” > “你的应用程序”。 
1. 选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解一个可将设备安全连接到云并发送设备到云的遥测数据的基本 Azure IoT 应用程序工作流。 你使用 Azure IoT Central 来创建应用程序和设备实例。 然后，使用 Azure IoT 设备 SDK 创建一个模拟设备，将其连接到 IoT Central，并发送遥测数据。 你还使用了 IoT Central 来监视遥测数据。

接下来，请浏览以下这些文章，以详细了解如何使用 Azure IoT 构建设备解决方案。 

> [!div class="nextstepaction"]
> [将遥测数据发送到 Azure IoT 中心](./quickstart-send-telemetry-iot-hub.md)
> [!div class="nextstepaction"]
> [创建 IoT Central 应用程序](../iot-central/core/quick-deploy-iot-central.md)