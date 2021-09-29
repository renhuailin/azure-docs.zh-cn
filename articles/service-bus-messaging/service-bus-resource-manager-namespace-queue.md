---
title: 使用 Azure 模板创建 Azure 服务总线命名空间和队列
description: 快速入门：使用 Azure 资源管理器模板创建服务总线命名空间和队列
documentationcenter: .net
author: spelluru
ms.author: spelluru
ms.date: 09/27/2021
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 302326726a1b154deae88e434a80bf7aecf9922a
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155318"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-arm-template"></a>快速入门：使用 ARM 模板创建服务总线命名空间和队列

本文介绍如何使用创建服务总线命名空间和命名空间中队列的 Azure 资源管理器模板（ARM 模板）。 本文介绍如何指定要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.servicebus%2Fservicebus-create-queue%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/servicebus-create-queue/)。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.servicebus/servicebus-create-queue/azuredeploy.json":::

该模板中定义的资源包括：

- [**Microsoft.ServiceBus/namespaces**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/namespaces/queues**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> 以下 ARM 模板可供下载和部署。
>
> * [创建包含队列和授权规则的服务总线命名空间](service-bus-resource-manager-namespace-auth-rule.md)
> * [创建包含主题和订阅的服务总线命名空间](service-bus-resource-manager-namespace-topic.md)
> * [创建服务总线命名空间](service-bus-resource-manager-namespace.md)
> * [创建包含主题、订阅和规则的服务总线命名空间](service-bus-resource-manager-namespace-topic-with-rule.md)

可以从 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)中找到更多模板

## <a name="deploy-the-template"></a>部署模板

使用此模板，将部署包含队列的服务总线命名空间。

[服务总线队列](service-bus-queues-topics-subscriptions.md#queues)为一个或多个竞争使用方提供先入先出 (FIFO) 消息传送方式。

若要自动运行部署，请单击以下按钮：为部署创建新的资源组，以便稍后可以轻松进行清理。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.servicebus%2Fservicebus-create-queue%2Fazuredeploy.json)

## <a name="validate-the-deployment"></a>验证部署

1. 选择顶部的“通知”，查看部署状态。 等待部署成功。 然后，在通知消息中选择“转到资源组”，导航到包含服务总线命名空间的资源组的页面。 

    ![来自部署的通知](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. 确认在资源列表中看到了服务总线命名空间。 

    ![资源组 - 命名空间](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. 从列表中选择命名空间以查看“服务总线命名空间”页。 

## <a name="clean-up-resources"></a>清理资源

1. 在 Azure 门户中，导航到资源组的“资源组”页。
2. 从工具栏选择 **删除资源组**。 
3. 键入资源组的名称，然后选择“删除”。 

    ![资源组 - 删除](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>后续步骤

请参阅以下主题，其中介绍了如何创建命名空间/队列的授权规则：

[使用 ARM 模板为命名空间和队列创建服务总线授权规则](service-bus-resource-manager-namespace-auth-rule.md)

查看以下文章了解如何管理这些资源：

* [使用 PowerShell 管理服务总线](service-bus-manage-with-ps.md)
* [使用服务总线 Explorer 管理服务总线资源](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.servicebus/servicebus-create-queue/azuredeploy.json/
[Azure Quickstart Templates]: https://azure.microsoft.com/resources/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md