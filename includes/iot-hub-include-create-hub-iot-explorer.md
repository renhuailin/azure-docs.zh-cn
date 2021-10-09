---
title: include 文件
description: include 文件
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 92a81cc28b605c715e2522c6ad78e5732a379c8f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610207"
---
## <a name="create-an-iot-hub"></a>创建 IoT 中心
在本部分中，使用 Azure CLI 创建 IoT 中心和资源组。  Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 IoT 中心充当中央消息中心，用于 IoT 应用程序与设备之间的双向通信。

创建 IoT 中心和资源组：

1. 启动 Azure CLI： 
    - 如果使用 Cloud Shell，请在 CLI 命令上选择 **试用** 按钮，以在拆分浏览器窗口中启动 Cloud Shell。 或者，你可以在单独的浏览器选项卡中打开 [Cloud Shell](https://shell.azure.com/bash) 。
    - 如果你在本地使用 Azure CLI，请打开控制台（例如 Windows CMD、PowerShell 或 Bash），并[登录到 Azure CLI](/cli/azure/authenticate-azure-cli)。
    
    若要在本快速入门的其余部分运行 CLI 命令：请复制命令语法，将其粘贴到 Cloud Shell 窗口或 CLI 控制台中，编辑变量值，然后按 Enter。

1. 运行 [az extension add](/cli/azure/extension#az_extension_add)，将“azure-iot”扩展安装或升级到当前版本。

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. 运行 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 以下命令在“eastus”  位置创建名为“MyResourceGroup”的资源组  。 
    >[!NOTE]
    > 可以选择设置备用位置。 若要查看可用位置，请运行 `az account list-locations`。 本教程使用示例命令中所示的 eastus。 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```

1. 运行 [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 命令创建 IoT 中心。 创建 IoT 中心可能需要数分钟的时间。 

    *YourIotHubName*。 使用你为 IoT 中心选择的名称，在以下命令中替换此占位符和周围的大括号。 IoT 中心名称必须在 Azure 中全局唯一。 无论你在何处看到占位符，都请使用本快速入门的其余部分中的 IoT 中心名称。

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```
    > [!TIP]
    > 创建 IoT 中心后，你将在本快速入门的其余部分使用 Azure IoT Explorer 与 IoT 中心进行交互。 IoT 资源管理器是一种 GUI 应用程序，让你可以连接到现有的 IoT 中心并添加、管理和监视设备。 若要了解详细信息，请参阅[安装并使用 Azure IoT 资源管理器](../articles/iot-fundamentals/howto-use-iot-explorer.md)。 或者，你可以继续使用 CLI 命令。

### <a name="configure-iot-explorer"></a>配置 IoT Explorer

在本快速入门的其余部分中，你将使用 IoT Explorer 将设备注册到 IoT 中心并查看设备遥测数据。 在本部分中，将配置 IoT Explorer 以连接到刚创建的 IoT 中心，并从公共模型存储库中读取即插即用模型。 

> [!NOTE]
> 也可使用 Azure CLI 来注册设备。 使用 [az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) --device-id mydevice --hub-name {YourIoTHubName} 命令注册新设备，使用 [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) --device-id mydevice --hub-name {YourIoTHubName} 命令获取设备的主连接字符串 。 记下设备连接字符串后，可直接跳到[运行模拟设备](#run-a-simulated-device)。

若要添加与 IoT 中心的连接：

1. 运行 [az iot hub connection-string show](/cli/azure/iot/hub/connection-string#az_iot_hub_connection_string_show) 命令来获取用于 IoT 中心的连接字符串。

    ```azurecli
    az iot hub connection-string  show --hub-name {YourIoTHubName}
    ```

1. 复制不带两旁引号字符的连接字符串。
1. 在 Azure IoT Explorer 的左侧菜单中，选择“IoT 中心”，然后选择“+ 添加连接”。
1. 将连接字符串粘贴到“连接字符串”框中。
1. 选择“保存”。 

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-add-connection.png" alt-text="在 IoT Explorer 中添加连接的屏幕截图":::

1. 如果连接成功，IoT Explorer 将切换到“设备”视图。

若要添加公共模型存储库：

1. 在 IoT Explorer 中，选择“主页”以返回到主页视图。
1. 在左侧菜单中，选择“IoT 即插即用设置”，然后从下拉菜单中依次选择“+添加”和“公共存储库”。
1. 此时会在 `https://devicemodels.azure.com` 中显示公共模型存储库的条目。

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-add-public-repository.png" alt-text="在 IoT Explorer 中添加公共模型存储库的屏幕截图":::

1. 选择“保存”。 

### <a name="register-a-device"></a>注册设备

在本部分中，将创建一个新设备实例，并将其注册到所创建的 IoT 中心。 在后面的部分中，将使用新注册的设备的连接信息安全连接模拟设备。

若要注册设备：

1. 从 IoT Explorer 的主页视图中，选择“IoT 中心”。
1. 此时应出现之前添加的连接。 在“连接属性”下面选择“查看此中心的设备”。
1. 选择“+ 新建”，然后输入设备的设备 ID；例如，mydevice。 将所有其它属性保持不变。
1. 选择“创建”  。

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-device-created.png" alt-text="Azure IoT Explorer 设备标识的屏幕截图":::

1. 使用复制按钮复制并记下“主连接字符串”字段。 稍后将需要使用此连接字符串。
