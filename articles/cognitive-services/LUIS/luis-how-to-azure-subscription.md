---
title: 如何创建和管理 LUIS 资源
titleSuffix: Azure Cognitive Services
description: 了解如何使用和管理 LUIS 应用的 Azure 资源。
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 07/12/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 4fe1e8cd4c78d4411a7cb9058648fc0e782cb0ee
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113666296"
---
# <a name="how-to-create-and-manage-luis-resources"></a>如何创建和管理 LUIS 资源

使用本文了解可与 LUIS 结合使用的 Azure 资源类型，以及如何管理它们。

## <a name="authoring-resource"></a>创作资源

创作资源可用于创建、管理、训练、测试和发布应用程序。 LUIS 创作资源有一个可用的[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)，即免费 (F0) 层，该层可为你提供：

* 100 万个创作事务 
* 每月 1,000 个测试预测终结点请求。

可使用 [v3.0-preview LUIS 编程 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) 来管理创作资源。 

## <a name="prediction-resource"></a>预测资源

预测资源可让你在超出创作资源提供的 1,000 个请求范围之外查询预测终结点。 预测资源有两个可用的[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)：

* 免费 (F0) 预测资源，它每月提供 10,000 个预测终结点请求。
* 标准 (S0) 预测资源，这是付费层。 

可使用 [v3.0-preview LUIS 终结点 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5f68f4d40a511ce5a7440859) 来管理预测资源。

> [!Note]
> * 还可使用[多服务资源](../cognitive-services-apis-create-account-cli.md?tabs=multiservice)来获取可用于多项认知服务的单个终结点。
> * LUIS 提供了两种类型的 F0（免费层级）资源：一种用于创作事务，另一种用于预测事务。 如果用于预测事务的免费配额不足，请确保你使用的是每月提供 10,000 个免费事务的 F0 预测资源，而不是每月提供 1,000 个预测事务的创作资源。
> * 你应当在要进行发布和查询的[区域](luis-reference-regions.md#publishing-regions)中创作 LUIS 应用。

## <a name="create-luis-resources"></a>创建 LUIS 资源

若要创建 LUIS 资源，可使用 LUIS 门户、[Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)或 Azure CLI。 创建资源后，需要将它们分配给应用，供其使用。

# <a name="luis-portal"></a>[LUIS 门户](#tab/portal)

### <a name="create-a-luis-authoring-resource-using-the-luis-portal"></a>使用 LUIS 门户创建 LUIS 创作资源

1. 登录 [LUIS 门户](https://www.luis.ai)，选择你所在的国家/地区，然后同意使用条款。 如果在门户中看到“我的应用”部分，则表示 LUIS 资源已存在，可跳过下一步。

2. 在出现的“选择创作”窗口中，找到你的 Azure 订阅和 LUIS 创作资源。 如果没有资源，可新建一个。

    :::image type="content" source="./media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="选择语言理解创作资源的类型。":::
    
    创建新的创作资源时，请提供以下信息：
    * **租户名称**：与 Azure 订阅关联的租户。
    * **Azure 订阅名称**：将对资源计费的订阅。
    * **Azure 资源组名称**：你选择或创建的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。
    * **Azure 资源名称**：你选择的自定义名称，用作创作和预测终结点查询的 URL 的一部分。
    * **定价层**：定价层确定每秒和每月的最大事务数。

### <a name="create-a-luis-prediction-resource-using-the-luis-portal"></a>使用 LUIS 门户创建 LUIS 预测资源

[!INCLUDE [Create LUIS Prediction resource in LUIS portal](./includes/add-prediction-resource-portal.md)]

# <a name="without-luis-portal"></a>[不使用 LUIS 门户](#tab/without-portal)

### <a name="create-luis-resources-without-using-the-luis-portal"></a>在不使用 LUIS 门户的情况下创建 LUIS 资源

使用 [Azure CLI](/cli/azure/install-azure-cli) 单独创建每项资源。

> [!TIP]
> * 创作资源 `kind` 为 `LUIS.Authoring`  
> * 预测资源 `kind` 为 `LUIS` 

1. 登录 Azure CLI：

    ```azurecli
    az login
    ```

    此命令打开一个浏览器，使你可以选择正确的帐户并提供身份验证。

2. 创建一个 `LUIS.Authoring` 种类的名为 `my-luis-authoring-resource` 的 LUIS 创作资源。 在 `westus` 区域的名为 `my-resource-group` 的现有资源组中创建它。

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

3. 创建 `LUIS` 种类的名为 `my-luis-prediction-resource` 的 LUIS 预测终结点资源。 在 `westus` 区域的名为 `my-resource-group` 的现有资源组中创建它。 如果需要比免费层提供的吞吐量更高的吞吐量，请将 `F0` 更改为 `S0`。 [详细了解定价层和吞吐量](luis-limits.md#key-limits)。

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

---


## <a name="assign-luis-resources"></a>分配 LUIS 资源

创建资源并不一定意味着要使用它，你需要将其分配给应用。 可以在 LUIS 中为单个应用或所有应用分配创作资源。

# <a name="luis-portal"></a>[LUIS 门户](#tab/portal)

### <a name="assign-resources-using-the-luis-portal"></a>使用 LUIS 门户分配资源

**将创作资源分配给所有应用** 

 以下过程将创作资源分配给所有应用。

1. 登录到 [LUIS 门户](https://www.luis.ai)。
1. 在右上角选择你的用户帐户，然后选择“设置”。
1. 在“用户设置”页上选择“添加创作资源”，然后选择现有的创作资源。  选择“保存” 。

**将资源分配给特定应用**

以下过程将资源分配给特定应用。

1. 登录到 [LUIS 门户](https://www.luis.ai)。 从“我的应用”列表中选择应用。
1. 转到“管理” > “Azure 资源” ：

    :::image type="content" source="./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png" alt-text="选择语言理解预测资源的类型。" lightbox="./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png":::

1. 在“预测资源”或“创作资源”选项卡上，选择“添加预测资源”或“添加创作资源”按钮。   
1. 使用窗体中的字段查找正确的资源，然后选择“保存”。

# <a name="without-luis-portal"></a>[不使用 LUIS 门户](#tab/without-portal)

## <a name="assign-prediction-resource-without-using-the-luis-portal"></a>在不使用 LUIS 门户的情况下分配预测资源

对于 CI/CD 管道等自动化进程，可使用以下步骤将 LUIS 资源自动分配给 LUIS 应用：

1. 获取 [Azure 资源管理器令牌](https://resources.azure.com/api/token?plaintext=true)，它是一个字母数字字符的字符串。 此令牌即将过期，请立即使用。 也可使用以下 Azure CLI 命令。

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
1. 使用令牌跨订阅请求 LUIS 运行时资源。 使用 API 获取用户帐户有权访问的 [LUIS Azure 帐户](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)。

    此 POST API 需要以下值：

    |标头|Value|
    |--|--|
    |`Authorization`|`Authorization` 的值为 `Bearer {token}`。 单词 `Bearer` 和空格前面必须是令牌值。|
    |`Ocp-Apim-Subscription-Key`|你的创作密钥。|

    API 返回 JSON 对象的数组，这些对象表示 LUIS 订阅。 返回的值包括订阅 ID、资源组和资源名称，返回为 `AccountName`。 在要将 LUIS 资源分配给 LUIS 应用的数组中查找一个项。

1. 使用[将 LUIS Azure 帐户分配给应用程序](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API，将令牌分配给 LUIS 资源。

    此 POST API 需要以下值：

    |类型|设置|Value|
    |--|--|--|
    |标头|`Authorization`|`Authorization` 的值为 `Bearer {token}`。 单词 `Bearer` 和空格前面必须是令牌值。|
    |标头|`Ocp-Apim-Subscription-Key`|你的创作密钥。|
    |标头|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS 应用 ID。
    |正文||{`AzureSubscriptionId`：订阅 ID；<br>`ResourceGroup`：具有预测资源的资源组名称；<br>`AccountName`：预测资源的名称}|

    此 API 成功时，将返回 `201 - created status`。

---

## <a name="unassign-a-resource"></a>取消分配资源

取消分配资源时，不会将其从 Azure 中删除。 只会将其从 LUIS 取消链接。

# <a name="luis-portal"></a>[LUIS 门户](#tab/portal)

## <a name="unassign-resources-using-luis-portal"></a>使用 LUIS 门户取消分配资源

1. 登录到 [LUIS 门户](https://www.luis.ai)，然后从“我的应用”列表中选择一个应用。
1. 转到“管理” > “Azure 资源” 。
1. 选择资源相对应的“取消分配资源”按钮。

# <a name="without-luis-portal"></a>[不使用 LUIS 门户](#tab/without-portal)

## <a name="unassign-prediction-resource-without-using-the-luis-portal"></a>在不使用 LUIS 门户的情况下取消分配预测资源

1. 获取 [Azure 资源管理器令牌](https://resources.azure.com/api/token?plaintext=true)，它是一个字母数字字符的字符串。 此令牌即将过期，请立即使用。 也可使用以下 Azure CLI 命令。

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
 
1. 使用令牌跨订阅请求 LUIS 运行时资源。 使用从用户帐户有权访问的[获取 LUIS Azure 帐户 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)。

    此 POST API 需要以下值：

    |标头|Value|
    |--|--|
    |`Authorization`|`Authorization` 的值为 `Bearer {token}`。 单词 `Bearer` 和空格前面必须是令牌值。|
    |`Ocp-Apim-Subscription-Key`|你的创作密钥。|

    API 返回 JSON 对象的数组，这些对象表示 LUIS 订阅。 返回的值包括订阅 ID、资源组和资源名称，返回为 `AccountName`。 在要将 LUIS 资源分配给 LUIS 应用的数组中查找一个项。

1. 使用[从应用程序取消分配 LUIS Azure 帐户](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32554f8591db3a86232e1/console) API，将令牌分配给 LUIS 资源。

    此 DELETE API 需要以下值：

    |类型|设置|Value|
    |--|--|--|
    |标头|`Authorization`|`Authorization` 的值为 `Bearer {token}`。 单词 `Bearer` 和空格前面必须是令牌值。|
    |标头|`Ocp-Apim-Subscription-Key`|你的创作密钥。|
    |标头|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS 应用 ID。
    |正文||{`AzureSubscriptionId`：订阅 ID；<br>`ResourceGroup`：具有预测资源的资源组名称；<br>`AccountName`：预测资源的名称}|

    此 API 成功时，将返回 `200 - OK status`。

---

## <a name="resource-ownership"></a>资源所有权

Azure 资源（例如 LUIS 资源）归包含该资源的订阅所有。

若要更改资源的所有权，可以执行以下操作之一：
* 转让订阅的[所有权](../../cost-management-billing/manage/billing-subscription-transfer.md)。
* 将 LUIS 应用导出为文件，然后在其他订阅上导入该应用。 可以从 LUIS 门户中的“我的应用”页面进行导出。

## <a name="resource-limits"></a>资源限制

### <a name="authoring-key-creation-limits"></a>创作密钥创建限制

可以按订阅为每个区域创建最多 10 个创作密钥。 发布区域不同于创作区域。 请确保在与你希望将客户端应用程序置于其中的发布区域对应的创作区域中创建应用。 有关创作区域映射到发布区域的方式的信息，请参阅[创作和发布区域](luis-reference-regions.md)。 

有关密钥限制的详细信息，请参阅[密钥限制](luis-limits.md#key-limits)。

### <a name="errors-for-key-usage-limits"></a>有关密钥使用限制的错误

使用限制取决于定价层。

如果超过了每秒事务数 (TPS) 配额，则会出现 HTTP 429 错误。 如果超过了每月事务数 (TPM) 配额，则会出现 HTTP 403 错误。

## <a name="change-the-pricing-tier"></a>更改定价层

1.  在 [Azure 门户](https://portal.azure.com)中，转到“所有资源”并选择资源

    :::image type="content" source="./media/luis-usage-tiers/find.png" alt-text="显示了 Azure 门户中的 LUIS 订阅的屏幕截图。" lightbox="./media/luis-usage-tiers/find.png":::

1.  在左侧菜单中选择“定价层”，查看可用的定价层
1.  选择想要的定价层，然后单击“选择”以保存更改。 完成定价更改后，右上方将显示一条通知以及定价层更新。

## <a name="view-azure-resource-metrics"></a>查看 Azure 资源指标

## <a name="view-a-summary-of-azure-resource-usage"></a>查看 Azure 资源使用情况的摘要
可以在 Azure 门户中查看 LUIS 使用情况信息。 “概述”页会显示摘要，其中包含最近的调用和错误。 如果你发出 LUIS 终结点请求，则最多需要五分钟才会显示更改。

:::image type="content" source="./media/luis-usage-tiers/overview.png" alt-text="显示“概述”页面的屏幕截图。" lightbox="./media/luis-usage-tiers/overview.png":::

## <a name="customizing-azure-resource-usage-charts"></a>自定义 Azure 资源使用图表
“指标”页面提供了更详细的数据视图。 可针对特定的“时间段”和“指标”配置指标图表 。

:::image type="content" source="./media/luis-usage-tiers/metrics.png" alt-text="显示“指标”页面的屏幕截图。" lightbox="./media/luis-usage-tiers/metrics.png":::

## <a name="total-transactions-threshold-alert"></a>总事务数阈值警报
如果你希望知道何时达到特定的事务阈值（例如 10,000 个事务），则可以创建警报：

1. 在左侧菜单中，选择“警报”
2. 在顶部菜单栏中，选择“新建警报规则”

    :::image type="content" source="./media/luis-usage-tiers/alerts.png" alt-text="显示“警报规则”页面的屏幕截图。" lightbox="./media/luis-usage-tiers/alerts.png":::

3. 单击“添加条件”

    :::image type="content" source="./media/luis-usage-tiers/alerts-2.png" alt-text="显示警报规则的“添加条件”页面的屏幕截图。" lightbox="./media/luis-usage-tiers/alerts-2.png":::

4. 选择“总调用数”

    :::image type="content" source="./media/luis-usage-tiers/alerts-3.png" alt-text="显示警报的“总调用数”页面的屏幕截图。" lightbox="./media/luis-usage-tiers/alerts-3.png":::

5. 向下滚动到“警报逻辑”部分，然后根据需要设置属性并单击“完成” 

    :::image type="content" source="./media/luis-usage-tiers/alerts-4.png" alt-text="显示“警报逻辑”页面的屏幕截图。" lightbox="./media/luis-usage-tiers/alerts-4.png":::

6. 若要在警报规则触发时发送通知或调用操作，请转到“操作”部分并添加操作组。

    :::image type="content" source="./media/luis-usage-tiers/alerts-5.png" alt-text="显示警报的“操作”页面的屏幕截图。" lightbox="./media/luis-usage-tiers/alerts-5.png":::

### <a name="reset-an-authoring-key"></a>重置创作密钥

对于[已迁移的创作资源](luis-migration-authoring.md)应用：如果创作密钥已泄露，请在 Azure 门户中该创作资源的“密钥”页上重置密钥。

对于尚未迁移的应用：可在 LUIS 门户的所有应用中重置此密钥。 如果通过创作 API 创作应用，则需要将 `Ocp-Apim-Subscription-Key` 的值更改为新密钥。

### <a name="regenerate-an-azure-key"></a>重新生成 Azure 密钥

可以在 Azure 门户的“密钥”页上重新生成 Azure 密钥。

<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>应用所有权、访问权限和安全性

应用是由其 Azure 资源定义的，这些资源取决于所有者的订阅。

你可以移动自己的 LUIS 应用。 可使用以下资源来帮助你通过 Azure 门户或 Azure CLI 实现此目的：

* [在 LUIS 创作资源之间移动应用](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [将资源移动到新的资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [在同一订阅内移动资源或跨订阅移动资源](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


## <a name="next-steps"></a>后续步骤

* 了解[如何使用版本](luis-how-to-manage-versions.md)来控制应用生命周期。


