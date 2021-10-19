---
title: 如何：预配 Azure Fluid Relay 服务
description: 如何使用 Azure 门户预配 Azure Fluid Relay 服务
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 29397802e0f4e4c4a47e82db8c1ef17bc89c7d9a
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661517"
---
# <a name="how-to-provision-an-azure-fluid-relay-service"></a>如何：预配 Azure Fluid Relay 服务

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

在将应用连接到 Azure Fluid Relay 服务器之前，必须在 Azure 帐户上预配 Azure Fluid Relay 服务器资源。 本文逐步介绍如何预配并准备好使用 Azure Fluid Relay 服务。 

## <a name="prerequisites"></a>先决条件

若要创建 Azure Fluid Relay 服务，必须拥有 Azure 帐户。 如果没有帐户，可以[免费试用 Azure](https://azure.com/free)。

## <a name="create-a-resource-group"></a>创建资源组
资源组是 Azure 资源的逻辑集合。 所有资源在资源组中进行部署和管理。 创建资源组：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在左侧导航栏中，选择“资源组”  。 然后选择“添加”  。

    :::image type="content" source="../images/add-resource-group.png" alt-text="Azure 门户上“资源组”页面的屏幕截图。":::

3. 对于“订阅”，请选择要在其中创建资源组的 Azure 订阅的名称。 

    :::image type="content" source="../images/create-resource-group.png" alt-text="Azure 门户上“创建资源组”页面的屏幕截图。":::

1. 键入资源组的唯一名称。 系统会立即检查该名称是否在当前选定的 Azure 订阅中可用。
1. 选择资源组所在的区域。
1. 选择“查看 + 创建”  。
1. 在“查看 + 创建”页面上，选择“创建”。

## <a name="create-a-fluid-relay-resource"></a>创建 Fluid Relay 资源
每个 Azure Fluid Relay 服务器资源都提供一个租户，供你在 Fluid 应用程序中使用。 在该租户中，你可以创建许多容器/会话。 若要使用门户在资源组中创建 Fluid Relay，请执行以下操作：

1. 在 Azure 门户中，选择屏幕左上角的“创建资源”。
2. 搜索“Fluid”
 
    :::image type="content" source="../images/marketplace-fluid-relay.png" alt-text="“创建资源”页面的屏幕截图，其中显示了“Fluid”一词的搜索结果。":::

3. 选择“Fluid Relay”，然后选择“创建”。
 
    :::image type="content" source="../images/fluid-relay-details-page.png" alt-text="Azure Fluid Relay 市场详细信息页面的屏幕截图。":::

4. 在“创建”页面上执行以下步骤：

    :::image type="content" source="../images/create-fluid-relay-server.png" alt-text="如何配置新 Azure Fluid Relay 服务器的屏幕截图。":::

    1. 选择要在其中创建命名空间的订阅。
    2. 选择在上一步创建的  资源组。
    3. 输入 Fluid Relay 资源的名称。
    4. 选择命名空间所在的位置。
    
    > [!NOTE]
    > 在公共预览期间，仅支持美国西部 2、欧洲西部和东南亚区域

5. 单击页面底部的“查看 + 创建”按钮。

6. 在“查看 + 创建”页面上查看设置，然后选择“创建”。 等待部署完成。

    :::image type="content" source="../images/create-server-validation-complete.png" alt-text="验证成功完成后新服务页面的屏幕截图。":::

7. 在“部署”页面上选择“转到资源”，以便导航到你的命名空间所对应的页面。

    :::image type="content" source="../images/deployment-complete.png" alt-text="指示部署已完成的 Azure 门户的屏幕截图。":::

8. 确认你看到与此示例类似的 Fluid Relay 页面。

    :::image type="content" source="../images/resource-details.png" alt-text="已部署的 Fluid Relay 资源的示例详细信息页面的屏幕截图。":::

## <a name="next-steps"></a>后续步骤
你刚刚创建了一个资源组并在该组中预配了 Azure Fluid Relay 资源。 接下来，可以[连接到应用中的 Azure Fluid Relay 服务](../quickstarts/quickstart-dice-roll.md)。
