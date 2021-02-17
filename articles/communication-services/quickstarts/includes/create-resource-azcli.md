---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: d70d53da48f41f0a6a37da6154d29696d3824325
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819991"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/dotnet/)。

## <a name="create-azure-communication-resource"></a>创建 Azure 通信资源

若要创建 Azure 通信服务资源，请[登录 Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)，然后运行以下命令：

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

可使用以下选项来配置通信服务资源：

* 资源组
* Azure 通信服务资源的名称
* 将要与资源关联的地理位置

在下一步中，可以为资源分配标记。 使用标记组织 Azure 资源。 有关标记的详细信息，请参阅[资源标记文档](../../../azure-resource-manager/management/tag-resources.md)。

## <a name="manage-your-communication-services-resource"></a>管理通信服务资源

若要向通信服务资源添加标记，请运行以下命令：

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

若要了解其他命令，请查看 [az communication](https://docs.microsoft.com/cli/azure/ext/communication/communication)。