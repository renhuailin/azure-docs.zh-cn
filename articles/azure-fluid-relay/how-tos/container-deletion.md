---
author: trngo
description: 了解如何使用 az-cli 删除单个容器
title: 删除 Fluid 容器
ms.author: trngo
ms.date: 09/28/2021
ms.service: azure-fluid
ms.topic: reference
ms.openlocfilehash: 8a94c06bd80b6997c5bd80cbb147bf68ccf91a4b
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728658"
---
# <a name="delete-fluid-containers-in-microsoft-azure-fluid-relay-server"></a>删除 Microsoft Azure Fluid Relay Server 中的 Fluid 容器

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

在此方案中，我们将删除现有的 Fluid 容器。 删除某个容器后，引用该容器的应用程序不再可以访问该容器或其数据。 

## <a name="requirements-to-delete-a-fluid-container"></a>删除 Fluid 容器所要满足的要求
- 若要开始，需安装 [Azure CLI](/cli/azure/install-azure-cli)。 如果已安装 Azure CLI，请运行 `az version` 来验证其版本是否为 2.0.67 或更高。
- 若要删除某个 Fluid 容器，必须确保应用程序及其客户端不再连接到该容器。

## <a name="list-the-containers-within-a-fluid-relay-resource"></a>列出 Fluid Relay 资源中的容器
若要查看属于 Fluid Relay 资源的所有容器，可运行以下命令：
```
az rest --method get --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers?api-version=<apiVersion>
```
subscriptionId：Fluid Relay 资源所属的 Azure 订阅的 ID。

resourceGroupName：Fluid Relay 资源所在的资源组的名称。

frsResourceName：Fluid Relay 资源的名称。 请注意，此值不同于 Fluid Relay 资源的 tenantId。

apiVersion：资源提供程序的 API 版本。 支持的最低版本为 2021-08-30-preview。  


## <a name="sample-output"></a>示例输出
输出将包含属于你的 Fluid Relay 资源的容器列表及其属性。
```json
{
  "value": [
    {
      "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.FluidRelay/fluidRelayServers/<frsResourcename>/fluidRelayContainers/<containerId>",
      "name": "<containerId>",
      "properties": {
        "frsContainerId": "<containerId>",
        "frsTenantId": "<frsResourceTenantId>"
      },
      "resourceGroup": "<resourceGroupname>",
      "type": "Microsoft.FluidRelay/fluidRelayServers/fluidRelayContainers"
    },
    ...
  ]
}
```


## <a name="delete-an-existing-container"></a>删除现有容器
若要删除某个容器，需要在上述输出中确定该容器的 containerId，然后运行以下命令： 
```
az rest --method delete --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers/<frsContainerId>?api-version=<api-version>
```
  frsContainerId：要删除的 Fluid 容器的 ID。 
