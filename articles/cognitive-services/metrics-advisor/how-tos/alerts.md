---
title: 配置指标顾问警报
titleSuffix: Azure Cognitive Services
description: 如何使用适用于电子邮件、Web 和 Azure DevOps 的挂钩配置指标顾问警报。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 30d8fdf99da7a4854db0985bed6256ecd6f7a366
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420914"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>操作说明：配置警报并使用挂钩获取通知

指标顾问检测到异常后，将使用挂钩根据警报设置触发警报通知。 警报设置可用于多个检测配置，可以使用各种参数自定义警报规则。

## <a name="create-a-hook"></a>创建挂钩

指标顾问支持三种不同类型的挂钩：电子邮件挂钩、Web 挂钩和 Azure DevOps。 可以选择适用于特定场景的挂钩。       

### <a name="email-hook"></a>电子邮件挂钩

> [!Note]
> 指标顾问资源管理员需要配置电子邮件设置，并在发送异常警报之前将 SMTP 相关信息输入指标顾问。 资源组管理员或订阅管理员需要在指标顾问资源的“访问控制”选项卡中至少分配一个“认知服务指标顾问管理员”角色。 [了解有关电子邮件设置配置的详细信息](../faq.md#how-to-set-up-email-settings-and-enable-alerting-by-email)。

若要创建电子邮件挂钩，可使用以下参数： 

电子邮件挂钩是将异常警报发送到“电子邮件收件人”部分中指定的电子邮件地址的通道。 系统将发送两种类型的警报电子邮件：“数据馈送不可用”警报和“事件报表”，其中包含一个或多个异常 。 

|参数 |说明  |
|---------|---------|
| 名称 | 电子邮件挂钩的名称 |
| 电子邮件收件人| 要向其发送警报的电子邮件地址|
| 外部链接 | 启用自定义重定向的可选字段，例如用于故障排除注释。 |
| 自定义异常警报标题 | 标题模板支持 `${severity}`、`${alertSettingName}`、`${datafeedName}`、`${metricName}`、`${detectConfigName}`、`${timestamp}`、`${topDimension}`、`${incidentCount}`、`${anomalyCount}`

单击“确定”后，将创建电子邮件挂钩。 可以在任何警报设置中使用它来接收异常警报。 

### <a name="web-hook"></a>Web 挂钩

> [!Note]
> * 使用 POST 请求方法。
> * 请求正文将类似于：  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * 创建或修改 Web 挂钩时，API 将作为具有空请求正文的测试被调用。 API 需要返回 200 HTTP 代码。

Web 挂钩是指标顾问服务中所有可用信息的入口点，并在触发警报时调用用户提供的 API。 所有警报都可以通过 Web 挂钩发送。

若要创建 Web 挂钩，需要添加以下信息：

|参数 |说明  |
|---------|---------|
|端点     | 触发警报时要调用的 API 地址。        |
|用户名/密码 | 用于对 API 地址进行身份验证。 如果不需要身份验证，则将其保留为黑色。         |
|标头     | API 调用中的自定义标头。        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="Web 挂钩创建窗口。":::

通过 Web 挂钩推送通知时，可以使用以下 API 获取警报的详细信息。 在 API 服务中设置要推送到的“时间戳”和“alertSettingGuid”，然后使用以下查询 ： 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

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

转到指标详细信息页，在指标详细信息页的左下角找到“警报设置”部分。 它列出了应用于所选检测配置的所有警报设置。 创建新的检测配置时，没有警报设置，并且不会发送任何警报。  
可以使用“添加”、“编辑”和“删除”图标来修改警报设置  。

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="“警报设置”菜单项。":::

单击“添加”或“编辑”按钮，获取用于添加或编辑警报设置的窗口 。

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="添加或编辑警报设置":::

警报设置名称：此警报设置的名称。 它将显示在警报电子邮件标题中。

挂钩：要向其发送警报的挂钩列表。

上面屏幕截图中标记的部分是一个检测配置的设置。 可以为不同的检测配置设置不同的警报设置。 使用此窗口中的第三个下拉列表选择目标配置。 

### <a name="filter-settings"></a>筛选器设置 

以下是一个检测配置的筛选器设置。

“警报项”有 4 个用于筛选异常的选项：

* 所有序列中的异常：警报中将包括所有异常。         
* 序列组中的异常：按维度值筛选序列。 为某些维度设置特定值。 只有当序列与指定值匹配时，警报中才包含异常。       
* 偏好序列中的异常：警报中仅包含标记为偏好的序列。        |
* 所有序列的前 N 个异常：此筛选器用于仅考虑其值位于前 N 的序列的情况。我们将回顾一些时间戳，并查看这些时间戳处的序列值是否处于前 N。如果“在前 n”计数大于指定数字，则异常将包含在警报中。        |

筛选器异常选项是包含以下选项的附加筛选器：

- 严重性：只有当异常严重性在指定范围内时，才包括异常。
- 推迟：触发警报时，在接下来的 N 个点（期间）中暂时停止对于异常的警报。
    - 推迟类型：如果设置为“序列”，则触发的异常将只推迟其序列。 对于“指标”，一个触发的异常将推迟此指标中的所有序列。
    - 推迟数量：要推迟的点（期间）的数量。
    - 非连续的重置：选择此选项后，触发的异常只会推迟接下来的 n 个连续异常。 如果以下数据点之一不是异常，则从该点重置推迟；如果不选择此选项，触发的异常将推迟接下来的 n 个点（周期），即使连续的数据点不是异常。
- 值（可选）：按值筛选。 只有满足条件的点值，将包含异常。 如果使用另一个指标的相应值，则两个指标的维度名称应一致。

未筛选掉的异常将在警报中发送。

### <a name="add-cross-metric-settings"></a>添加交叉指标设置

单击警报设置页中的“+ 添加交叉指标设置”以添加其他部分。

“运算符”选择器是每个部分的逻辑关系，用于确定它们是否发送警报。


|操作员  |说明  |
|---------|---------|
|AND     | 仅当序列与每个警报部分匹配且所有数据点是异常时发送警报。 如果指标具有不同的维度名称，则永远不会触发警报。         |
|OR     | 如果至少有一个部分包含异常，则发送警报。         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="多个警报设置部分的运算符":::

## <a name="next-steps"></a>后续步骤

- [使用反馈调整异常检测](anomaly-feedback.md)
- [诊断事件](diagnose-incident.md)。
- [配置指标并微调检测配置](configure-metrics.md)