---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: mikben
ms.openlocfilehash: dfdeedd058131912db6884a49cf92ac1020b6801
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113762374"
---
## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/dotnet/)。
- 安装 [Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli) 

## <a name="create-azure-communication-services-resource"></a>创建 Azure 通信服务资源

若要创建 Azure 通信服务资源，请先[登录 Azure CLI](/cli/azure/authenticate-azure-cli)。 你可以通过使用命令 ```az login``` 和提供凭据在终端完成此操作。 运行以下命令以创建资源：

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

如果要选择特定订阅，你还可以指定 ```--subscription``` 标志并提供订阅 ID。
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

可使用以下选项来配置通信服务资源：

* 资源组
* Azure 通信服务资源的名称
* 将要与资源关联的地理位置

在下一步中，可以为资源分配标记。 使用标记组织 Azure 资源。 有关标记的详细信息，请参阅[资源标记文档](../../../azure-resource-manager/management/tag-resources.md)。

## <a name="manage-your-communication-services-resource"></a>管理通信服务资源

若要向通信服务资源添加标记，请运行以下命令： 你也可以指向特定的订阅。

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

若要了解其他命令，请查看 [az communication](/cli/azure/communication)。