---
title: 配置指标顾问警报
titleSuffix: Azure Cognitive Services
description: 如何使用适用于电子邮件、Web 和 Azure DevOps 的挂钩配置指标顾问警报。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: f63ea23cce0ab43dd5c9e0864619ff32d5dae50d
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341308"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>操作说明：配置警报并使用挂钩获取通知

指标顾问检测到异常后，将使用挂钩根据警报设置触发警报通知。 警报设置可用于多个检测配置，可以使用各种参数自定义警报规则。

## <a name="create-a-hook"></a>创建挂钩

指标顾问支持四种不同类型的挂钩：电子邮件、Teams、Webhook 和 Azure DevOps。 可以选择适用于特定场景的挂钩。      

### <a name="email-hook"></a>电子邮件挂钩

> [!Note]
> 指标顾问资源管理员需要配置电子邮件设置，并在发送异常警报之前将 SMTP 相关信息输入指标顾问。 资源组管理员或订阅管理员需要在指标顾问资源的“访问控制”选项卡中至少分配一个“认知服务指标顾问管理员”角色。 [详细了解电子邮件设置配置](../faq.yml#how-to-set-up-email-settings-and-enable-alerting-by-email-)。 


电子邮件挂钩是将异常警报发送到“电子邮件收件人”部分中指定的电子邮件地址的通道。 两种类型的警报电子邮件将发送：“数据馈送不可用”警报和“事件报表”，其中包含一个或多个异常 。 

若要创建电子邮件挂钩，可使用以下参数： 

|参数 |说明  |
|---------|---------|
| 名称 | 电子邮件挂钩的名称 |
| 电子邮件收件人| 要将警报发送到的电子邮件地址|
| 外部链接 | 启用自定义重定向的可选字段，例如用于故障排除注释。 |
| 自定义异常警报标题 | 标题模板支持 `${severity}`、`${alertSettingName}`、`${datafeedName}`、`${metricName}`、`${detectConfigName}`、`${timestamp}`、`${topDimension}`、`${incidentCount}`、`${anomalyCount}`

选择“确定”后，将创建电子邮件挂钩。 可以在任何警报设置中使用它来接收异常警报。 有关详细步骤，请参阅[在指标顾问中启用异常通知](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp)这一教程。

### <a name="teams-hook"></a>Teams 挂钩

Teams 挂钩是一条通道，用于将异常警报发送到 Microsoft Teams 通道。 Teams 挂钩通过“传入 Webhook”连接器实现。 你可能需要在目标 Teams 通道中提前创建“传入 Webhook”连接器，并获取其 URL。 然后切换回到指标顾问工作区。 

选择左侧导航栏中的“挂钩”选项卡，然后选择页面右上角的“创建挂钩”按钮。 选择“Teams”挂钩类型，以下参数已提供： 

|参数 |说明  |
|---------|---------|
| 名称 | Teams 挂钩的名称 | 
| 连接器 URL | 在目标 Teams 通道创建的“传入 Webhook”连接器中复制的 URL。 |

选择“确定”后，将创建 Teams 挂钩。 可以在任何警报设置中使用该挂钩来通知目标 Teams 通道有异常警报。 有关详细步骤，请参阅[在指标顾问中启用异常通知](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp)这一教程。

### <a name="web-hook"></a>Web 挂钩

Webhook 是使用客户提供的终结点的另一条通知通道。 在时序上检测到的任何异常都将通过 Webhook 通知。 可以通过几个步骤，在指标顾问中启用 Webhook 作为警报通知通道。 

**步骤 1.**  在指标顾问资源中启用托管标识

系统分配的托管标识限制为每个资源一个，并绑定到此资源的生命周期。 可以使用 Azure 基于角色的访问控制 (Azure RBAC) 来授予对托管标识的访问权限。 托管标识通过 Azure AD 进行身份验证，因此无需在代码中存储任何凭据。 

转到 Azure 门户中的“指标顾问”资源，选择“标识”，将其转换为“启用”，则会启用托管标识。 

**步骤 2.** 在指标顾问工作区中创建 Webhook

登录到工作区并选择“挂钩”选项卡，然后选择“创建挂钩”按钮。 


若要创建 Web 挂钩，需要添加以下信息：

|参数 |说明  |
|---------|---------|
|端点     | 触发警报时要调用的 API 地址。 必须是 HTTPS。       |
|用户名/密码 | 用于对 API 地址进行身份验证。 如果不需要身份验证，则将其保留为黑色。         |
|标头     | API 调用中的自定义标头。        |
|Azure Key vaults 中的证书标识符| 如果访问终结点需要通过证书进行身份验证，则证书应存储在 Azure Key vaults 中。 在此处输入该标识符。

> [!Note]
> 创建或修改 Webhook 时，终结点将作为一个测试被调用，其请求正文为空。 API 需要返回 200 HTTP 代码才能成功通过验证。

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="Web 挂钩创建窗口。":::

- 请求方法为 POST
- 超时 30 秒
- 重试 5xx 错误，忽略其他错误。 不会遵循 301/302 重定向请求。
- 请求正文： 
```
{
"value": [{
    "hookId": "b0f27e91-28cf-4aa2-aa66-ac0275df14dd",
    "alertType": "Anomaly",
    "alertInfo": {
        "anomalyAlertingConfigurationId": "1bc6052e-9a2a-430b-9cbd-80cd07a78c64",
        "alertId": "172536dbc00",
        "timestamp": "2020-05-27T00:00:00Z",
        "createdTime": "2020-05-29T10:04:45.590Z",
        "modifiedTime": "2020-05-29T10:04:45.590Z"
    },
    "callBackUrl": "https://kensho2-api.azurewebsites.net/alert/anomaly/configurations/1bc6052e-9a2a-430b-9cbd-80cd07a78c64/alerts/172536dbc00/incidents"
}]
}
```

Step3.（可选） 在 Azure Key vaults 中存储证书并获取标识符。如上所述，如果访问终结点需要通过证书进行身份验证，则证书应存储在 Azure Key vaults 中。 

- 查看[使用 Azure 门户在 Azure Key Vault 中设置和检索证书](../../../key-vault/certificates/quick-create-portal.md)
- 单击已添加的证书，然后可复制“证书标识符”。 
- 然后选择“访问策略”和“添加访问策略”，为“密钥权限”、“机密权限”和“证书权限”授予“Get”权限。 选择主体作为指标顾问资源的名称。 选择“访问策略”页中的“添加”和“保存”按钮。 

**步骤 4.** 接收异常通知。通过 Webhook 推送通知时，你可以调用 Webhook 请求中的“callBackUrl”来提取事件数据。 此 API 的详细信息：

-   [/alert/anomaly/configurations/{configurationId}/alerts/{alertId}/incidents](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/operations/getIncidentsFromAlertByAnomalyAlertingConfiguration)

借助 Webhook 和 Azure 逻辑应用，可以在不配置 SMTP 服务器的情况下发送电子邮件通知。 有关详细步骤，请参阅[在指标顾问中启用异常通知](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp)这一教程。

### <a name="azure-devops"></a>Azure DevOps

指标顾问还支持在 Azure DevOps 中自动创建工作项，以在检测到任何异常时跟踪问题/bug。 所有警报都可以通过 Azure DevOps 挂钩发送。

若要创建 Azure DevOps 挂钩，需要添加以下信息

|参数 |说明  |
|---------|---------|
| 名称 | 挂钩的名称 |
| 组织 | DevOps 所属的组织 |
| Project | DevOps 中的特定项目。 |
| 访问令牌 |  用于对 DevOps 进行身份验证的令牌。 | 

> [!Note]
> 如果希望指标顾问基于异常警报创建工作项，则需要授予写入权限。 创建挂钩后，可以在任何警报设置中使用它们。 在“挂钩设置”页中管理挂钩。 

## <a name="add-or-edit-alert-settings"></a>添加或编辑警报设置

转到“指标详细信息”页面，在左下角找到“警报设置”部分。 它列出了应用于所选检测配置的所有警报设置。 创建新的检测配置时，没有警报设置，并且不会发送任何警报。  
可以使用“添加”、“编辑”和“删除”图标来修改警报设置  。

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="“警报设置”菜单项。":::

选择“添加”或“编辑”按钮，打开一个用来添加或编辑警报设置的窗口 。

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="添加或编辑警报设置":::

警报设置名称：警报设置的名称。 它将显示在警报电子邮件标题中。

挂钩：要向其发送警报的挂钩列表。

上面屏幕截图中标记的部分是一个检测配置的设置。 可以为不同的检测配置设置不同的警报设置。 使用此窗口中的第三个下拉列表选择目标配置。 

### <a name="filter-settings"></a>筛选器设置 

以下是一个检测配置的筛选器设置。

“筛选对象”有四个选项可用于筛选异常：

* 所有序列中的异常：警报中将包括所有异常。         
* 序列组中的异常：按维度值筛选序列。 为某些维度设置特定值。 只有当序列与指定值匹配时，警报中才包含异常。       
* 偏好序列中的异常：警报中仅包含标记为偏好的序列。        |
* 前 N 个序列中的异常：此筛选器适用于只关注值排在前 N 的序列的情况。指标顾问将回溯以前的时间戳，并检查在这些时间戳的序列的值是否在前 N。如果“前 n”计数大于指定的数字，则警报中将包含该异常。        |

筛选异常选项是额外的筛选器，具有以下选项：

- 严重性：仅当异常严重性在指定范围内时才会包含异常。
- 推迟：后 N 个点（时间段）中的异常在警报中触发时，将暂时停止警报。
    - 推迟类型：设置为“序列”时，触发的异常只会推迟其序列 。 对于“指标”，一个触发的异常将推迟此指标中的所有序列。
    - 推迟数：要推迟的点数（时间段）。
    - 重置为不连续：选中此项时，触发的异常将仅推迟后 n 个连续异常。 如果以下数据点之一不是异常，则从该点重置推迟；如果不选择此选项，触发的异常将推迟接下来的 n 个点（周期），即使连续的数据点不是异常。
- 值（可选）：按值筛选。 只有满足条件的点值，将包含异常。 如果使用另一个指标的相应值，则两个指标的维度名称应一致。

未筛选掉的异常将在警报中发送。

### <a name="add-cross-metric-settings"></a>添加交叉指标设置

在“警报设置”页面选择“+ 添加交叉指标设置”来添加其他部分。

“运算符”选择器是每个部分的逻辑关系，用于确定它们是否发送警报。


|操作员  |说明  |
|---------|---------|
|AND     | 仅当序列与每个警报部分匹配且所有数据点是异常时发送警报。 如果指标具有不同的维度名称，则永远不会触发警报。         |
|OR     | 如果至少有一个部分包含异常，则发送警报。         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="多个警报设置部分的运算符":::

## <a name="next-steps"></a>后续步骤

- [使用反馈调整异常检测](anomaly-feedback.md)
- [诊断事件](diagnose-an-incident.md)。
- [配置指标并微调检测配置](configure-metrics.md)
