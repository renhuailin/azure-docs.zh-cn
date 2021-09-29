---
title: 使用模板创建 Azure 服务总线命名空间主题
description: 快速入门：使用 Azure 资源管理器模板创建包含主题和订阅的服务总线命名空间
documentationcenter: .net
author: spelluru
ms.author: spelluru
ms.date: 09/27/2021
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.custom:
- devx-track-azurepowershell
- mode-arm
ms.openlocfilehash: 84fc6657026e3e07ede5250a8304b5d270a24d24
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155470"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>快速入门：使用 Azure 资源管理器模板创建包含主题和订阅的服务总线命名空间

本文介绍如何使用 Azure 资源管理器模板创建服务总线命名空间，并且该命名空间内包含主题和订阅。 本文介绍如何指定要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求

有关创建模板的详细信息，请参阅[创作 Azure 资源管理器模板][Authoring Azure Resource Manager templates]。

有关完整模板，请参阅[包含主题和订阅的服务总线命名空间][Service Bus namespace with topic and subscription]模板。

> [!NOTE]
> 以下 Azure 资源管理器模板可供下载和部署。
>
> * [创建服务总线命名空间](service-bus-resource-manager-namespace.md)
> * [创建包含队列的服务总线命名空间](service-bus-resource-manager-namespace-queue.md)
> * [创建包含队列和授权规则的服务总线命名空间](service-bus-resource-manager-namespace-auth-rule.md)
> * [创建包含主题、订阅和规则的服务总线命名空间](service-bus-resource-manager-namespace-topic-with-rule.md)
>
> 若要查找最新模板，请访问 [Azure 快速入门模板][Azure Quickstart Templates]库并搜索“服务总线”  。

## <a name="what-do-you-deploy"></a>要部署什么？

使用此模板，将部署包含主题和订阅的服务总线命名空间。

[服务总线主题和订阅](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)以“发布/订阅”  模式提供一对多的通信形式。

若要自动运行部署，请单击以下按钮：

[![部署到 Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.servicebus%2Fservicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>参数

使用 Azure 资源管理器，可以定义在部署模板时想要指定的值的参数。 该模板具有一个名为 `Parameters` 的部分，其中包含所有参数值。 为随着要部署的项目或要部署到的环境而变化的值定义参数。 不要为始终保持不变的值定义参数。 每个参数值可在模板中用来定义所部署的资源。

模板定义以下参数：

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

要创建的服务总线命名空间的名称。

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

在服务总线命名空间中创建的主题的名称。

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

在服务总线命名空间中创建的订阅的名称。

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

模板的服务总线 API 版本。

```json
"serviceBusApiVersion": {
       "type": "string",
       "defaultValue": "2017-04-01",
       "metadata": {
           "description": "Service Bus ApiVersion used by the template"
       }
```

## <a name="resources-to-deploy"></a>要部署的资源

创建类型为 **Messaging** 的包含主题和订阅的标准服务总线命名空间。

```json
"resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

有关 JSON 语法和属性，请参阅[命名空间](/azure/templates/microsoft.servicebus/namespaces)、[主题](/azure/templates/microsoft.servicebus/namespaces/topics)和[订阅](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)。

## <a name="commands-to-run-deployment"></a>运行部署的命令

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/quickstarts/microsoft.servicebus/servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az deployment group create \<my-resource-group\> --name \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/quickstarts/microsoft.servicebus/servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>后续步骤

现在，已使用 Azure 资源管理器创建并部署了资源，请通过查看以下文章了解如何管理这些资源：

* [使用 PowerShell 管理服务总线](service-bus-manage-with-ps.md)
* [使用服务总线 Explorer 管理服务总线资源](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/resources/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Service Bus namespace with topic and subscription]: https://azure.microsoft.com/resources/templates/servicebus-create-topic-and-subscription/
