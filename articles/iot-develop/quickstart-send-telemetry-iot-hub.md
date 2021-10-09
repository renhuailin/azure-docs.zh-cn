---
title: 有关将设备遥测数据发送到 Azure IoT 中心的快速入门
description: 本快速入门为设备开发人员介绍如何安全地将设备连接到 Azure IoT 中心。 使用适用于 C、C#、Python、Node.js 或 Java 的 Azure IoT 设备 SDK 生成适用于 Windows、Linux 或 Raspberry Pi OS (Raspian) 的模拟设备客户端。 最后，将设备连接到 Azure IoT 中心并发送遥测。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: quickstart
ms.date: 08/03/2021
ms.collection: embedded-developer, application-developer
zone_pivot_groups: iot-develop-set1
ms.openlocfilehash: f7b1543b91d604b6640bf97877bf2c134deee866
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094028"
---
# <a name="quickstart-send-telemetry-from-an-iot-plug-and-play-device-to-azure-iot-hub"></a>快速入门：将遥测从 IoT 即插即用发送到 Azure IoT 中心

**适用对象**：[设备应用程序开发人员](about-iot-develop.md#device-application-development)

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-c](../../includes/iot-develop-send-telemetry-iot-hub-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-csharp](../../includes/iot-develop-send-telemetry-iot-hub-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-java](../../includes/iot-develop-send-telemetry-iot-hub-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-node](../../includes/iot-develop-send-telemetry-iot-hub-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-python](../../includes/iot-develop-send-telemetry-iot-hub-python.md)]

:::zone-end
    
## <a name="clean-up-resources"></a>清理资源
如果不再需要本快速入门中创建的 Azure 资源，可以使用 Azure CLI 将其删除。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。

若要按名称删除资源组，请执行以下操作：
1. 运行 [az group delete](/cli/azure/group#az_group_delete) 命令。 此命令将删除创建的资源组、IoT 中心和设备注册。

    ```azurecli-interactive
    az group delete --name MyResourceGroup
    ```
1. 运行 [az group list](/cli/azure/group#az_group_list) 命令，确认资源组是否已删除。  

    ```azurecli-interactive
    az group list
    ```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解一个可将设备安全连接到云并发送设备到云的遥测数据的基本 Azure IoT 应用程序工作流。 你已使用 Azure CLI 创建了 Azure IoT 中心和设备实例。 然后，你已使用 Azure IoT SDK 创建了一个模拟设备，将其连接到了中心，并发送了遥测数据。 你还使用 Azure 门户监视了遥测数据。

接下来，请浏览以下这些文章，以详细了解如何使用 Azure IoT 构建设备解决方案。 

> [!div class="nextstepaction"]
> [控制连接到 IoT 中心的设备](../iot-hub/quickstart-control-device.md)
> [!div class="nextstepaction"]
> [将遥测数据发送到 IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [将 MXCHIP AZ3166 Devkit 连接到 IoT Central](quickstart-devkit-mxchip-az3166.md)
