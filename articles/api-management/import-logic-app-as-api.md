---
title: 使用 Azure 门户将逻辑应用导入为 API | Microsoft Docs
description: 本文演示如何使用 API 管理 (APIM) 将逻辑应用导入为 API。
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: danlep
ms.openlocfilehash: 509a143a368c4626b7d0a39e1c3f2d15c8a3d0b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667761"
---
# <a name="import-a-logic-app-as-an-api"></a>将逻辑应用导入为 API

本文介绍如何将逻辑应用导入为 API 并测试导入的 API。

在本文中，学习如何：

> [!div class="checklist"]
>
> -   将逻辑应用导入为 API
> -   在 Azure 门户中测试 API

## <a name="prerequisites"></a>先决条件

-   请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)
-   确保订阅中已有一个公开 HTTP 终结点的逻辑应用。 有关详细信息，请参阅[使用 HTTP 终结点触发工作流](../logic-apps/logic-apps-http-endpoint.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>导入和发布后端 API

1. 在 Azure 门户中导航到 API 管理服务，然后从菜单中选择“API”  。
1. 从“添加新的 API”列表中选择“逻辑应用”   。

    :::image type="content" source="./media/import-logic-app-as-api/logic-app-select.png" alt-text="选择逻辑应用类别":::

1. 按“浏览”查看订阅中使用 HTTP 触发器的逻辑应用列表  。 
    * 不使用 HTTP 触发器的逻辑应用不会出现在此列表中。

    :::image type="content" source="./media/import-logic-app-as-api/browse-logic-apps.png" alt-text="通过适当的触发器浏览现有逻辑应用":::

1. 选择逻辑应用。 

    :::image type="content" source="./media/import-logic-app-as-api/select-logic-app-import-2.png" alt-text="选择逻辑应用":::

1. API 管理找到与所选应用关联的 swagger 后，将其提取并导入。
1. 添加 API URL 后缀。 
    * 后缀在该 API 管理实例中唯一标识此特定 API。

    :::image type="content" source="./media/import-logic-app-as-api/create-from-logic-app.png" alt-text="完成字段":::

1. 若要发布 API 并使其可供开发人员使用，请切换到“完整”视图并将其与“产品”关联 。 本例中使用“无限制”产品。 
    * 你可以在创建过程中或之后通过“设置”选项卡将 API 添加到产品中。

    >[!NOTE]
    > 产品是通过开发人员门户向开发人员提供的一个或多个 API 的关联。 首先，开发人员必须订阅产品才能访问 API。 订阅后，他们会得到一个订阅密钥，可用于该产品中的任何 API。 作为 API 管理实例的创建者，你默认成为管理员且订阅了每个产品。
    >
    > 每个 API 管理实例默认附带两个示例产品：
    > - **入门**
    > - **不受限制**

1. 输入其他 API 设置。 
    * 可以在创建过程中或之后通过转到“设置”选项卡来设置这些值。在[导入和发布第一个 API](import-and-publish.md#import-and-publish-a-backend-api) 教程中对这些设置进行了说明。
1. 选择“创建”  。

## <a name="test-the-api-in-the-azure-portal"></a>在 Azure 门户中测试 API

可直接从 Azure 门户调用操作，这样可以方便地查看和测试 API 的操作。

:::image type="content" source="./media/import-logic-app-as-api/test-logic-app-api.png" alt-text="测试逻辑应用":::

1. 选择上一步中创建的 API。
2. 按“测试”选项卡  。
3. 选择要测试的操作。

    * 该页将显示查询参数和标头的字段。 
    * 其中一个标头是“Ocp-Apim-Subscription-Key”，用于提供和此 API 关联的产品订阅密钥。 
    * 作为 API 管理实例的创建者，你已是管理员，因此会自动填充该密钥。

4. 按“发送”。 

    * 如果测试成功，后端以“200 正常”和某些数据做出响应。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>每个逻辑应用程序都有一个 **manual-invoke** 操作。 若要构造包含多个逻辑应用的 API 并避免冲突，需要将函数重命名。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>
> [转换和保护已发布的 API](transform-api.md)
