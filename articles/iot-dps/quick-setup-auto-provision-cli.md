---
title: 快速入门 - 使用 Azure CLI 设置 Azure IoT 中心设备预配服务
description: 快速入门 - 使用 Azure CLI 设置 Azure IoT 中心设备预配服务 (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0a8225e63844b0eefa9d8a6922732769b723e200
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273735"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>快速入门：使用 Azure CLI 设置 IoT 中心设备预配服务

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本快速入门详述了如何使用 Azure CLI 创建 IoT 中心和 IoT 中心设备预配服务并将两个服务链接到一起。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> 在本快速入门中创建的 IoT 中心和预配服务将会充当可以公开发现的 DNS 终结点。 如果决定更改用于这些资源的名称，请确保不使用任何敏感信息。
>

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](/cli/azure/group#az_group_create)”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在 *westus* 位置创建名为 *my-sample-resource-group* 的资源组。

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> 此示例在“美国西部”位置创建资源组。 可运行 `az account list-locations -o table` 命令，查看可用位置的列表。
>
>

## <a name="create-an-iot-hub"></a>创建 IoT 中心

使用 [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 命令创建 IoT 中心。

以下示例在 *westus* 位置创建名为 *my-sample-hub* 的 IoT 中心。 IoT 中心名称在 Azure 中必须是全局唯一的，因此，你可能需要向示例名称添加一个唯一的前缀或后缀，或者选择一个新名称。 请确保名称遵循 IoT 中心的正确命名约定：名称长度应为 3-50 个字符，只能包含大写或小写字母数字字符或连字符（“-”）。 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>创建设备预配服务

使用 [az iot dps create](/cli/azure/iot/dps#az_iot_dps_create) 命令创建设备预配服务。 

以下示例在 westus 位置创建名为 my-sample-dps 的预配服务。 你还需要为自己的预配服务选择一个全局唯一的名称。 请确保名称遵循 IoT 中心设备预配服务的正确命名约定：名称长度应为 3-64 个字符，只能包含大写或小写字母数字字符或连字符（“-”）。

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> 此示例在“美国西部”位置创建预配服务。 若要查看可用位置的列表，可以运行 `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` 命令，也可以转到[Azure 状态](https://azure.microsoft.com/status/)页，在其中搜索“设备预配服务”。 在命令中，可以使用一个单词或多个单词的格式来指定位置，例如：westus、West US、WEST US，等等。该值不区分大小写。 如果使用多个单词的格式来指定位置，请将值置于引号中，例如 `--location "West US"`。
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>获取 IoT 中心的连接字符串

需要提供 IoT 中心的连接字符串才能将其与设备预配服务链接到一起。 使用 [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string) 命令获取连接字符串，并使用其输出设置一个变量。链接这两个资源时，需要用到该变量。 

以下示例将 hubConnectionString 变量设置为中心的 iothubowner 策略的主键连接字符串值（可以使用 `--policy-name` 参数指定其他策略）   。 用“my-sample-hub”替换先前选择的唯一 IoT 中心名称  。 此命令使用 Azure CLI [查询](/cli/azure/query-azure-cli)和[输出](/cli/azure/format-output-azure-cli#tsv-output-format)选项从命令输出提取连接字符串。

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

可以使用 `echo` 命令查看连接字符串。

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> 这两个命令适用于在 Bash 下运行的主机。
> 
> 如果使用本地 Windows/CMD shell 或 PowerShell 主机，请修改这些命令，以便使用该环境的正确语法。
>
> 如果使用 Azure Cloud Shell，请检查 shell 窗口左侧的“环境”下拉框中是否显示“Bash”。
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>将 IoT 中心和预配服务相链接

使用 [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create) 命令将 IoT 中心与预配服务相链接。 

以下示例将 westus 位置中名为 my-sample-hub 的 IoT 中心与名为 my-sample-dps 的设备预配服务相链接。 用这些名称替换先前选择的唯一 IoT 中心和设备预配服务名称。 该命令使用上一步在 hubConnectionString 变量中存储的 IoT 中心的连接字符串  。

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

此命令可能需要花费几分钟时间完成。

## <a name="verify-the-provisioning-service"></a>验证预配服务

使用 [az iot dps show](/cli/azure/iot/dps#az_iot_dps_show) 命令获取预配服务的详细信息。

以下示例获取名为 *my-sample-dps* 的预配服务的详细信息。 用此名称替换自己的设备预配服务名称。

```azurecli-interactive
az iot dps show --name my-sample-dps
```
链接的 IoT 中心显示在 *properties.iotHubs* 集合中。

![验证预配服务](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>清理资源

本教程系列中的其他快速入门教程是在本文的基础上制作的。 如果打算继续学习后续的快速入门或相关教程，请不要清除在本快速入门中创建的资源。 如果不打算继续，可以使用以下命令删除预配服务、IoT 中心或资源组及其所有资源。 用自己的资源名称替换下面写的资源名称。

若要删除预配服务，请运行 [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete) 命令：

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
若要删除 IoT 中心，请运行 [az iot hub delete](/cli/azure/iot/hub#az_iot_hub_delete) 命令：

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

若要删除资源组及其所有资源，请运行 [az group delete](/cli/azure/group#az_group_delete) 命令：

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署 IoT 中心和设备预配服务实例，并链接了这两个资源。 若要了解如何使用此设置来预配模拟设备，请继续学习本快速入门中关于如何创建模拟设备的内容。

> [!div class="nextstepaction"]
> [快速入门：创建模拟设备](./quick-create-simulated-device-tpm.md)
