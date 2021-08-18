---
title: 在 API 管理中将 Azure Function App 作为 API 导入
titleSuffix: Azure API Management
description: 本文演示如何将 Azure Function App 作为 API 导入 Azure API 管理中。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: apimpm
ms.openlocfilehash: cf4ff32af5b0960d22496512e8d624df0f4cf5e5
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114669239"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>在 Azure API 管理中将 Azure Function App 作为 API 导入

Azure API 管理支持将 Azure Function App 作为新 API 导入或将其追加到现有 API。 此过程会在 Azure Function App 中自动生成一个主机密钥，然后将其分配给 Azure API 管理中的命名值。

本文详细介绍如何在 Azure API 管理中将 Azure Function App 作为 API 导入和测试。 

可以了解如何执行以下操作：

> [!div class="checklist"]
> * 将 Azure Function App 作为 API 导入
> * 将 Azure Function App 追加到 API
> * 查看新的 Azure Function App 主机密钥和 Azure API 管理命名值
> * 在 Azure 门户中测试 API

## <a name="prerequisites"></a>先决条件

* 完成[创建 Azure API 管理实例](get-started-create-service-instance.md)快速入门。
* 确保订阅中有 Azure Functions 应用。 有关详细信息，请参阅[创建 Azure Function App](../azure-functions/functions-get-started.md)。 函数必须将 HTTP 触发器和授权级别设置设为“匿名”或“函数”。 

> [!NOTE]
> 可以使用 Visual Studio Code 的 API 管理扩展来导入和管理 API。 按照 [API 管理扩展教程](visual-studio-code-tutorial.md)进行安装并开始使用。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> 将 Azure Function App 作为新 API 导入

执行以下步骤，通过 Azure Function App 创建新的 API。

1. 在 Azure 门户中导航到 API 管理服务，然后从菜单中选择“API”。

2. 在“添加新 API”列表中，选择“Function App”。 

    :::image type="content" source="./media/import-function-app-as-api/add-01.png" alt-text="显示函数应用磁贴的屏幕截图。":::

3. 单击“浏览”，选择要导入的 Functions。

    :::image type="content" source="./media/import-function-app-as-api/add-02.png" alt-text="突出显示“浏览”按钮的屏幕截图。":::

4. 单击“Function App”部分，从可用 Function App 的列表中进行选择。

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="突出显示“函数应用”部分的屏幕截图。":::

5. 找到要从其导入 Functions 的 Function App，然后单击它并按“选择”。

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="突出显示“选择”按钮以及你要从中导入函数的“函数应用”的屏幕截图。":::

6. 选择要导入的 Functions，然后单击“选择”。
    * 只能使用“匿名”或“函数”授权级别导入基于 HTTP 触发器的函数。 

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="突出显示要导入的函数和“选择”按钮的屏幕截图。":::

7. 切换到“完整”视图并将“产品”分配到新 API 。 
8. 如果需要，请在创建过程中指定其他字段，也可以稍后通过“设置”选项卡来配置这些字段。 
    * 在[导入和发布第一个 API](import-and-publish.md#import-and-publish-a-backend-api) 教程中对这些设置进行了说明。

    >[!NOTE]
    > 产品是通过开发人员门户向开发人员提供的一个或多个 API 的关联。 首先，开发人员必须订阅产品才能访问 API。 订阅后，他们会得到一个订阅密钥，可用于该产品中的任何 API。 作为 API 管理实例的创建者，你默认成为管理员且订阅了每个产品。
    >
    > 每个 API 管理实例默认附带两个示例产品：
    > - **入门**
    > - **不受限制**

9. 单击 **创建**。

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> 将 Azure Function App 追加到现有 API

执行以下步骤，将 Azure Function App 追加到现有 API。

1. 在 **Azure API 管理** 服务实例中，从左侧的菜单选择“API”。

2. 选择要将 Azure Function App 导入到其中的 API。 单击“...”，并从上下文菜单中选择“导入”。 

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-1.png" alt-text="突出显示“导入”菜单选项的屏幕截图。":::

3. 单击“Function App”磁贴。

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-2.png" alt-text="突出显示“函数应用”磁贴的屏幕截图。":::

4. 在弹出窗口中，单击“浏览”。

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-3.png" alt-text="显示“浏览”按钮的屏幕截图。":::

5. 单击“Function App”部分，从可用 Function App 的列表中进行选择。

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="突出显示“函数应用”列表的屏幕截图。":::

6. 找到要从其导入 Functions 的 Function App，然后单击它并按“选择”。

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="突出显示你要从中导入函数的“函数应用”的屏幕截图。":::

7. 选择要导入的 Functions，然后单击“选择”。

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="突出显示你想要导入的函数的屏幕截图。":::

8. 单击“导入”。

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-4.png" alt-text="从 Function App 追加":::

## <a name="authorization"></a><a name="authorization"></a> 授权

导入 Azure Function App 会自动生成：

* 主机密钥，该密钥位于 Function App 中，其名称为 apim-{*你的 Azure API 管理服务实例名称*}；
* 命名值，该值位于 Azure API 管理实例中，其名称为 {*你的 Azure Function App 实例名称*}-key，其中包含创建的主机密钥。

对于 2019 年 4 月 4 日以后创建的 API，主机密钥将通过 HTTP 请求从 API 管理传递到标头中的 Function App。 较旧的 API 将主机密钥作为[查询参数](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization)传递。 可以通过对与 Function App 关联的后端实体进行 `PATCH Backend` [REST API 调用](/rest/api/apimanagement/2020-12-01/backend/update#backendcredentialscontract)来更改此行为。

> [!WARNING]
> 删除或更改 Azure Function App 主机密钥或 Azure API 管理命名值的值会导致服务之间的通信中断。 这些值不自动同步。
>
> 如需轮换主机密钥，请确保 Azure API 管理中的命名值也得到修改。

### <a name="access-azure-function-app-host-key"></a>访问 Azure Function App 主机密钥

1. 导航到 Azure Function App 实例。

    :::image type="content" source="./media/import-function-app-as-api/keys-01.png" alt-text="突出显示选择函数应用实例的屏幕截图。":::

2. 在侧导航菜单的“函数”部分，选择“应用密钥”。 

    :::image type="content" source="./media/import-function-app-as-api/keys-02b.png" alt-text="突出显示“函数应用”设置选项的屏幕截图。":::

3. 在“主机密钥”部分下方找到密钥。

    :::image type="content" source="./media/import-function-app-as-api/keys-03.png" alt-text="突出显示“主机密钥”部分的屏幕截图。":::

### <a name="access-the-named-value-in-azure-api-management"></a>访问 Azure API 管理中的命名值

导航到 Azure API 管理实例，然后在左侧菜单中选择“命名值”。 Azure Function App 密钥存储在该处。

:::image type="content" source="./media/import-function-app-as-api/api-named-value.png" alt-text="从 Function App 添加":::

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> 在 Azure 门户中测试新的 API

可直接从 Azure 门户调用操作。 使用 Azure 门户可以方便地查看和测试 API 的操作。  

:::image type="content" source="./media/import-function-app-as-api/test-api.png" alt-text="突出显示测试过程的屏幕截图。":::

1. 选择在上一部分创建的 API。

2. 选择“测试”选项卡。

3. 选择要测试的操作。

    * 该页将显示查询参数和标头的字段。 
    * 其中一个标头是“Ocp-Apim-Subscription-Key”，用于提供和此 API 关联的产品订阅密钥。 
    * 作为 API 管理实例的创建者，你已是管理员，因此会自动填充该密钥。 

4. 选择“发送”。

    * 如果测试成功，后端以“200 正常”和某些数据做出响应。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)
