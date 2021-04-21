---
title: 使用 Azure CLI 创建 IoT 中心 | Microsoft Docs
description: 了解如何使用 Azure CLI 命令创建资源组，然后在该资源组中创建 IoT 中心。 并了解如何删除中心。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: eff0085a4a739e0831b25b1bd28cd234fdbcde3d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766436"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>使用 Azure CLI 创建 IoT 中心

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

本文介绍如何使用 Azure CLI 创建 IoT 中心。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

使用 Azure CLI 创建资源组，并添加 IoT 中心。

1. 创建 IoT 中心时，必须在资源组中创建它。 使用现有资源组，或运行以下[命令创建资源组](/cli/azure/resource)：
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > 上一示例在美国西部位置创建资源组。 可以通过运行以下命令来查看可用位置列表： 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. 运行以下[命令，使用 IoT 中心的全局唯一名称在资源组中创建 IoT 中心](/cli/azure/iot/hub#az_iot_hub_create)：
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


上一命令在计费的 S1 定价层中创建 IoT 中心。 有关详细信息，请参阅 [Azure IoT 中心定价](https://azure.microsoft.com/pricing/details/iot-hub/)。

## <a name="remove-an-iot-hub"></a>删除 IoT 中心

可使用 Azure CLI [删除单个资源](/cli/azure/resource)（例如 IoT 中心），或删除资源组及其所有资源（包括任何 IoT 中心）。

若要[删除 IoT 中心](/cli/azure/iot/hub#az_iot_hub_delete)，请运行以下命令：

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

若要[删除资源组](/cli/azure/group#az_group_delete)及其所有资源，请运行以下命令：

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 IoT 中心，请参阅以下文章：

* [IoT 中心开发人员指南](iot-hub-devguide.md)
* [使用 Azure 门户管理 IoT 中心](iot-hub-create-through-portal.md)