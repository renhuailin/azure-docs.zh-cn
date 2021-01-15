---
title: 快速入门 - 使用 ARM 模板创建 Azure API 管理实例
description: 了解如何使用 Azure 资源管理器模板（ARM 模板）在“开发人员”层创建 Azure API 管理实例。
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: 1610c51b613712c06410247e2eb673a50a9988b3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792236"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>快速入门：使用 ARM 模板创建新的 Azure API 管理服务实例

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建 Azure API 管理 (APIM) 服务实例。 APIM 可帮助组织将 API 发布到外部、合作伙伴和内部开发人员，以发挥其数据和服务的潜力。 API 管理通过开发人员参与、商业洞察力、分析、安全性和保护提供了核心竞争力以确保成功的 API 程序。 使用 APIM 可以为在任何位置托管的现有后端服务创建和管理新式 API 网关。 有关详细信息，请参阅[概述](api-management-key-concepts.md)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![“部署到 Azure”](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-azure-api-management-create/)。

:::code language="json" source="~/quickstart-templates/101-azure-api-management-create/azuredeploy.json":::

模板中定义了以下资源：

- **[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service)**

可以在 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular)中找到更多 Azure API 管理模板示例。

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开一个模板。 该模板将创建一个具有自动生成名称的 API 管理服务实例。

    [![“部署到 Azure”](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

    在此示例中，该实例在“开发人员”层配置，这是评估 Azure API 管理的一个经济选择。 此层不用于生产。 有关对 API 管理层进行缩放的详细信息，请参阅[升级和缩放](upgrade-and-scale.md)。

1. 选择或输入以下值。
    - 订阅：选择一个 Azure 订阅。
    - **资源组**：选择“新建”，为资源组输入一个独一无二的名称，然后选择“确定”。 
    - 区域：选择资源组的位置。 示例：“美国中部”。
    - **发布服务器电子邮件**：输入电子邮件地址以接收通知。
    - **发布服务器名称**：输入为 API 发布服务器选择的名称。
    - **Sku**：接受“开发人员”的默认值。
    - **Sku 计数**：接受默认值。
    - **位置**：接受 API 管理服务的生成位置。

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="API 管理模板属性":::

1. 选择“查看 + 创建”，然后查看条款和条件。 如果同意，请选择“创建”。

    > [!TIP]
    >  在“开发人员”层中创建和激活 API 管理服务可能需要 30 到 40 分钟。

1. 成功创建实例后，你会收到通知：

    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="部署通知":::

 使用 Azure 门户部署模板。 除了 Azure 门户，还可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-cli.md)。

## <a name="review-deployed-resources"></a>查看已部署的资源

请使用 Azure 门户来检查已部署的资源，也可以使用 Azure CLI 或 Azure PowerShell 等工具列出已部署的资源。

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“API 管理服务”，然后选择所创建的服务实例。
1. 在“概览”页上查看服务的属性。

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="服务概述页":::

当 API 管理服务实例处于联机状态时，便可以使用它了。 从教程开始[导入并发布](import-and-publish.md)你的第一个 API。

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用后续的教程，你可能需要保留 API 管理实例。 如果不再需要资源组，可以将其删除，这将删除资源组中的资源。

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“资源组”。 还可以在“主页”上选择“资源组”。
1. 在“资源组”页上，选择相应资源组。
1. 在资源组页上，选择“删除资源组”。

    :::image type="content" source="media/quickstart-arm-template/delete-resource-group.png" alt-text="删除资源组":::
1. 键入资源组的名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：导入和发布第一个 API](import-and-publish.md)
