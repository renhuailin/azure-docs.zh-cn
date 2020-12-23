---
title: 使用创作和运行时密钥 - LUIS
description: 首次使用 LUIS 时，不需要创建创作密钥。 如果要发布应用，然后使用运行时终结点，则需创建运行时密钥并将其分配给应用。
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 19c27dc80f9af013c458663c9c7afb0033683acd
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97348061"
---
# <a name="create-luis-resources"></a>创建 LUIS 资源

创作和查询预测运行时资源为你的语言理解 (LUIS) 应用和预测终结点提供身份验证。

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS 资源

LUIS 允许三类 Azure 资源和一类非 Azure 资源：

|资源|目的|认知服务 `kind`|认知服务 `type`|
|--|--|--|--|
|创作资源|可用于创建、管理、训练、测试和发布应用程序。 如果计划通过编程方式或 LUIS 门户创作 LUIS 应用，请[创建 LUIS 创作资源](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal)。 在将 Azure 创作资源链接到应用程序之前，你需要 [迁移你的 LUIS 帐户](luis-migration-authoring.md#what-is-migration) 。 可以通过为人员分配[参与者角色](#contributions-from-other-authors)来控制对创作资源的权限。 <br><br> LUIS 创作资源有一个层级可用：<br> <ul> <li>**免费 F0 创作资源**，它每月提供 100 万个免费创作事务和 1,000 个免费测试预测终结点请求。 |`LUIS.Authoring`|`Cognitive Services`|
|预测资源| 在发布 LUIS 应用程序后，请使用预测资源/密钥来查询预测终结点请求。 请在客户端应用发出的预测请求超出创作资源或入门资源提供的 1,000 个请求之前创建 LUIS 预测资源。 <br><br> 预测资源有两个层级可用：<br><ul> <li> **免费 F0 预测资源**，它每月提供 10,000 个免费预测终结点请求。<br> <li> **标准 S0 预测资源**，这是付费层级。 [详细了解定价。](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|入门版/试用版资源|可用于创建、管理、训练、测试和发布应用程序。 如果在首次登录到 LUIS 时选择入门版资源选项，则会默认创建此资源。 入门版密钥最终会被弃用。 所有 LUIS 用户需要 [迁移其帐户](luis-migration-authoring.md#what-is-migration) 并将其 LUIS 应用程序链接到创作资源。 不同于创作资源，此资源不提供用于 Azure 基于角色的访问控制的权限。 <br><br> 与创作资源一样，入门版资源也提供 100 万个免费创作事务和 1,000 个免费测试预测终结点请求。|-|不是 Azure 资源。|
|[认知服务多服务资源密钥](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|与 LUIS 和其他受支持的认知服务共享的查询预测终结点请求。|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUIS 提供了两种类型的 F0（免费层级）资源：一种用于创作事务，另一种用于预测事务。 如果用于预测事务的免费配额不足，请确保你使用的是每月提供 10,000 个免费事务的 F0 预测资源，而不是每月提供 1,000 个预测事务的创作资源。

在 Azure 资源创建过程完成之后，请在 LUIS 门户中为应用[分配资源](#assign-a-resource-to-an-app)。

> [!important]
> 你应当在要进行发布和查询的[区域](luis-reference-regions.md#publishing-regions)中创作 LUIS 应用。

## <a name="resource-ownership"></a>资源所有权

Azure 资源（例如 LUIS 资源）归包含该资源的订阅所有。

若要更改资源的所有权，可以执行以下操作之一：
* 转让订阅的 [所有权](../../cost-management-billing/manage/billing-subscription-transfer.md) 。
* 将 LUIS 应用导出为文件，然后在其他订阅上导入该应用。 可以从 LUIS 门户中的“我的应用”页面进行导出。

## <a name="resource-limits"></a>资源限制

### <a name="authoring-key-creation-limits"></a>创作密钥创建限制

可以按订阅为每个区域创建最多 10 个创作密钥。 发布区域不同于创作区域。 请确保在与你希望将客户端应用程序置于其中的发布区域对应的创作区域中创建应用。 有关创作区域映射到发布区域的方式的信息，请参阅 [创作和发布区域](luis-reference-regions.md)。 

有关密钥限制的详细信息，请参阅 [密钥限制](luis-limits.md#key-limits)。

### <a name="errors-for-key-usage-limits"></a>有关密钥使用限制的错误

使用限制取决于定价层。

如果超过了每秒事务数 (TPS) 配额，则会出现 HTTP 429 错误。 如果超过了每月事务数 (TPM) 配额，则会出现 HTTP 403 错误。


### <a name="reset-an-authoring-key"></a>重置创作密钥

对于已 [迁移的创作资源](luis-migration-authoring.md) 应用：如果你的创作密钥已泄露，请在创作资源的 " **密钥** " 页上重置 Azure 门户中的密钥。

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


### <a name="contributions-from-other-authors"></a>其他作者的贡献

对于已 [迁移的创作资源](luis-migration-authoring.md)应用：可以使用 "**访问控制" (IAM)** "页来管理 Azure 门户中创作资源的 _参与者_。 了解如何使用协作者的电子邮件地址和参与者角色[添加用户](luis-how-to-collaborate.md)。

对于尚未迁移的应用：你可以在 LUIS 门户中的“管理 > 协作者”页上管理所有协作者。

### <a name="query-prediction-access-for-private-and-public-apps"></a>专用和公共应用的查询预测访问权限

对于专用应用，所有者和参与者可以使用查询预测运行时访问权限。 对于公共应用，具有自己的 Azure [认知服务](../cognitive-services-apis-create-account.md)或 [LUIS](#create-resources-in-the-azure-portal) 运行时资源和公共应用 ID 的用户可以使用运行时访问权限。

目前没有公共应用的目录。

### <a name="authoring-permissions-and-access"></a>创作权限和访问权限
从 [LUIS](luis-reference-regions.md#luis-website) 门户或[创作 API](https://go.microsoft.com/fwlink/?linkid=2092087) 访问应用的权限由 Azure 创作资源控制。

所有者和所有参与者具有创作应用所需的访问权限。

|创作访问权限包括：|说明|
|--|--|
|添加或删除终结点密钥||
|导出版本||
|导出终结点日志||
|导入版本||
|公开应用|如果应用是公共的，任何拥有创作密钥或终结点密钥的人员都可以查询应用。|
|修改模型|
|发布|
|查看用于[主动学习](luis-how-to-review-endpoint-utterances.md)的终结点陈述|
|定型|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>预测终结点运行时访问权限

查询预测终结点所需的访问权限由“应用程序信息”页上“管理”部分的设置进行控制。

|[专用终结点](#runtime-security-for-private-apps)|[公共终结点](#runtime-security-for-public-apps)|
|:--|:--|
|可供所有者和参与者使用|可供所有者、参与者以及知道应用 ID 的任何其他人使用|

可以通过在服务器到服务器环境中调用 LUIS 运行时密钥来控制谁可以查看该密钥。 如果在机器人上使用 LUIS，则机器人和 LUIS 之间的连接已经更安全。 如果直接调用 LUIS 终结点，则应创建具有受控访问权限（通过 [Azure AD](https://azure.microsoft.com/services/active-directory/) 之类的方式）的服务器端 API（例如 Azure [函数](https://azure.microsoft.com/services/functions/)）。 如果调用并验证服务器端 API，则在确认授权后将调用传递到 LUIS。 此策略不能防止中间人攻击。 但它会对你的用户的密钥和终结点 URL 进行模糊处理，允许你跟踪访问，并允许你添加终结点响应日志记录（例如 [Application Insights](https://azure.microsoft.com/services/application-insights/)）。

### <a name="runtime-security-for-private-apps"></a>专用应用的运行时安全性

专用应用的运行时仅适用于以下密钥：

|密钥和用户|说明|
|--|--|
|所有者的创作密钥| 最多 1,000 个终结点命中数|
|协作者/参与者的创作密钥| 最多 1,000 个终结点命中数|
|由作者或协作者/参与者分配给 LUIS 的任何密钥|基于密钥用法层|

### <a name="runtime-security-for-public-apps"></a>公共应用的运行时安全性

当你的应用配置为公共后，任何有效的 LUIS 创作密钥或 LUIS 终结点密钥都可以查询它，只要该密钥未使用整个终结点配额。

不是所有者或参与者的用户只有在获得应用 ID 后才能访问公共应用的运行时。 LUIS 没有为用户提供公共市场或任何其他搜索公共应用的方式。

公共应用在所有区域中发布。 因此，具有基于区域的 LUIS 资源密钥的用户可以在与资源密钥关联的任何区域中访问该应用。


### <a name="control-access-to-your-query-prediction-endpoint"></a>控制对查询预测终结点的访问

可以通过在服务器到服务器环境中调用 LUIS 预测运行时终结点密钥来控制谁可以查看该密钥。 如果在机器人上使用 LUIS，则机器人和 LUIS 之间的连接已经更安全。 如果直接调用 LUIS 终结点，则应创建具有受控访问权限（通过 [Azure AD](https://azure.microsoft.com/services/active-directory/) 之类的方式）的服务器端 API（例如 Azure [函数](https://azure.microsoft.com/services/functions/)）。 如果调用服务器端 API 并且身份验证和授权得到验证，则将调用传递到 LUIS。 此策略不能防止中间人攻击。 但它会对你的用户的终结点进行模糊处理，允许你跟踪访问，并允许你添加终结点响应日志记录（例如 [Application Insights](https://azure.microsoft.com/services/application-insights/)）。

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>登录 LUIS 门户并开始创作

1. 登录 [LUIS 门户](https://www.luis.ai)并同意使用条款。
1. 通过选择你的 Azure LUIS 创作密钥，开始创作 LUIS 应用：

   ![显示了欢迎屏幕的屏幕截图。](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 完成资源选择过程后，[创建一个新应用](luis-how-to-start-new-app.md#create-new-app-in-luis)。


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>在 Azure CLI 中创建资源

使用 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 单独创建每项资源。

资源 `kind`：

* 创作：`LUIS.Authoring`
* 预测：`LUIS`

1. 登录 Azure CLI：

    ```azurecli
    az login
    ```

    此命令打开一个浏览器，使你可以选择正确的帐户并提供身份验证。

1. 创建一个 `LUIS.Authoring` 种类的名为 `my-luis-authoring-resource` 的 LUIS 创作资源。 在 `westus` 区域的名为 `my-resource-group` 的现有资源组中创建它。

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. 创建 `LUIS` 种类的名为 `my-luis-prediction-resource` 的 LUIS 预测终结点资源。 在 `westus` 区域的名为 `my-resource-group` 的现有资源组中创建它。 如果需要比免费层提供的吞吐量更高的吞吐量，请将 `F0` 更改为 `S0`。 [详细了解定价层和吞吐量](luis-limits.md#key-limits)。

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > 在 LUIS 门户的“管理 > Azure 资源”页上分配这些密钥之前，该门户不会使用这些密钥。 

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>在 LUIS 门户中分配资源

可以在 LUIS 中为单个应用或所有应用分配创作资源。 以下过程将所有应用分配给单个创作资源。

1. 登录到 [LUIS 门户](https://www.luis.ai)。
1. 在右上角选择你的用户帐户，然后选择“设置”。
1. 在“用户设置”页上选择“添加创作资源”，然后选择现有的创作资源。  选择“保存” 。

## <a name="assign-a-resource-to-an-app"></a>将资源分配给应用

>[!NOTE]
>如果你没有 Azure 订阅，将无法分配或创建新资源。 需要创建 [Azure 免费帐户](https://azure.microsoft.com/en-us/free/) ，然后返回到 LUIS 从门户创建新的资源。

可以使用此过程创建创作或预测资源，或将其分配给应用程序： 

1. 登录到 [LUIS 门户](https://www.luis.ai)。 从“我的应用”列表中选择应用。
1. 转到“管理” > “Azure 资源” ：

    ![显示了“Azure 资源”页面的屏幕截图。](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. 在“预测资源”或“创作资源”选项卡上，选择“添加预测资源”或“添加创作资源”按钮。   
1. 使用窗体中的字段查找正确的资源，然后选择“保存”。
1. 如果没有现有的资源，可通过在窗口底部选择“是否创建新 LUIS 资源?”来创建一个。


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>在不使用 LUIS 门户的情况下分配查询预测运行时资源

对于 CI/CD 管道等自动过程，可能需要自动将 LUIS 运行时资源分配到 LUIS 应用。 为此，请完成以下步骤：

1. 从此 [网站](https://resources.azure.com/api/token?plaintext=true)获取 Azure 资源管理器令牌。 此令牌将会过期，因此立即使用它。 该请求将返回 Azure 资源管理器令牌。

    ![显示用于请求 Azure 资源管理器令牌的网站的屏幕截图。](./media/luis-manage-keys/get-arm-token.png)

1. 使用令牌跨订阅请求 LUIS 运行时资源。 使用用户帐户有权访问的 [GET LUIS Azure 帐户 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)。

    此 POST API 需要以下值：

    |标头|Value|
    |--|--|
    |`Authorization`|`Authorization` 的值为 `Bearer {token}`。 标记值的前面必须是单词 `Bearer` 和空格。|
    |`Ocp-Apim-Subscription-Key`|你的创作密钥。|

    API 将返回一个 JSON 对象的数组，这些对象表示你的 LUIS 订阅。 返回的值包括订阅 ID、资源组和资源名称，返回为 `AccountName` 。 在数组中查找要分配给 LUIS 应用的 LUIS 资源的项。

1. 使用将 [LUIS Azure 帐户分配给应用程序](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API，将令牌分配给 LUIS 资源。

    此 POST API 需要以下值：

    |类型|设置|Value|
    |--|--|--|
    |标头|`Authorization`|`Authorization` 的值为 `Bearer {token}`。 标记值的前面必须是单词 `Bearer` 和空格。|
    |标头|`Ocp-Apim-Subscription-Key`|你的创作密钥。|
    |标头|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS 应用 ID。
    |正文||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    此 API 成功后，将返回 `201 - created status` 。

## <a name="unassign-a-resource"></a>取消分配资源

1. 登录到 [LUIS 门户](https://www.luis.ai)，然后从“我的应用”列表中选择一个应用。
1. 转到“管理” > “Azure 资源” 。
1. 在“预测资源”或“创作资源”选项卡上，选择与资源对应的“取消分配资源”按钮。  

取消分配资源时，不会将其从 Azure 中删除。 只会将其从 LUIS 取消链接。


## <a name="delete-an-account"></a>删除帐户

要了解删除帐户时一并删除了哪些数据，请参阅[数据存储和删除](luis-concept-data-storage.md#accounts)。

## <a name="change-the-pricing-tier"></a>更改定价层

1.  在 [Azure 门户](https://portal.azure.com)中，查找并选择你的 LUIS 订阅：

    ![显示了 Azure 门户中的 LUIS 订阅的屏幕截图。](./media/luis-usage-tiers/find.png)
1.  选择“定价层”以查看可用的定价层：

    ![显示了定价层菜单项的屏幕截图。](./media/luis-usage-tiers/subscription.png)
1.  选择定价层，然后单击“选择”以保存更改：

    ![显示了如何选择并保存定价层的屏幕截图。](./media/luis-usage-tiers/plans.png)

    定价更改完成后，会出现一个用于验证定价层更新的弹出窗口：

    ![用于验证定价更新的弹出窗口的屏幕截图。](./media/luis-usage-tiers/updated.png)
1. 请记住在“发布”页[分配此终结点密钥](#assign-a-resource-to-an-app)，并将其用于所有终结点查询。

## <a name="view-azure-resource-metrics"></a>查看 Azure 资源指标

### <a name="view-a-summary-of-azure-resource-usage"></a>查看 Azure 资源使用情况的摘要
可以在 Azure 门户中查看 LUIS 使用情况信息。 “概述”页会显示摘要，其中包含最近的调用和错误。 如果你发出 LUIS 终结点请求，则最多需要五分钟才会显示更改。

![显示“概述”页面的屏幕截图。](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>自定义 Azure 资源使用图表
“指标”页提供了更详细的数据视图：

![显示了“指标”页面的屏幕截图。](./media/luis-usage-tiers/metrics-default.png)

可针对特定的时间段和指标类型配置指标图表：

![显示了自定义图表的屏幕截图。](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>总事务数阈值警报
如果你希望知道何时达到特定的事务阈值（例如 10,000 个事务），则可以创建警报：

![显示了“警报规则”页面的屏幕截图。](./media/luis-usage-tiers/alert-default.png)

添加针对特定时间段内“总调用数”指标的指标警报。 添加应接收该警报的所有人员的电子邮件地址。 添加应接收该警报的所有系统的 Webhook。 还可在触发警报时运行逻辑应用。

## <a name="next-steps"></a>后续步骤

* 了解[如何使用版本](luis-how-to-manage-versions.md)来控制应用生命周期。
* 迁移到新的 [创作资源](luis-migration-authoring.md)。
