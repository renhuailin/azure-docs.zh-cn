---
title: 在 Azure Sentinel playbook 中使用触发器和操作 | Microsoft Docs
description: 深入了解如何授予 playbook 对 Azure Sentinel 警报和事件中信息的访问权限，以及如何使用该信息采取修正操作。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: 045178a30088ccfd8b76d70d210e29c5a9253ac1
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294385"
---
# <a name="use-triggers-and-actions-in-azure-sentinel-playbooks"></a>在 Azure Sentinel playbook 中使用触发器和操作

本文档介绍 playbook 可以用来与 Azure Sentinel 和工作区表中的信息进行交互的[逻辑应用 Azure sentinel 连接器](/connectors/azuresentinel/)中的触发器和操作的类型。 本文档进一步说明了如何访问可能需要的特定类型的 Azure Sentinel 信息。

本文档和[向 Azure Sentinel 验证 playbook](authenticate-playbooks-to-sentinel.md) 的指南一起与另一 playbook 文档配套 - [教程：在 Azure Sentinel 中结合自动化规则使用 playbook](tutorial-respond-threats-playbook.md)。 这三个文档将相互引用。

有关 playbook 的简介，请参阅[在 Azure Sentinel 中使用 playbook 实现威胁响应自动化](automate-responses-with-playbooks.md)。

有关 Azure Sentinel 连接器的完整规范，请参阅[逻辑应用连接器文档](/connectors/azuresentinel/)。

## <a name="permissions-required"></a>所需的权限

| 角色\连接器组件 | 触发器 | “获取”操作 | 更新事件，<br>添加注释 |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Azure Sentinel 读取者](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| “Azure Sentinel [响应者](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)/[参与者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)” | &#10003; | &#10003; | &#10003; |
| 

[详细了解 Azure Sentinel 中的权限](./roles.md)。

## <a name="azure-sentinel-triggers-summary"></a>Azure Sentinel 触发器摘要

尽管可以通过多种方式使用 Azure Sentinel 连接器，但是可以将连接器的组件分为 2 个流，每个流由不同的 Azure Sentinel 事件触发：

| 触发器 | 完整触发器名称<br>逻辑应用设计器 | 何时使用 | 已知限制 
| --------- | ------------ | -------------- | -------------- | 
| “事件触发器” | “触发 Azure Sentinel 事件创建规则时（预览版）” | 建议用于大多数事件自动化情况。<br><br>playbook 接收事件对象，包括实体和警报。 使用此触发器允许将 playbook 附加到“自动化规则”，因此在 Azure Sentinel 中创建事件时，可以触发 playbook，并且[自动化规则的优点](./automate-incident-handling-with-automation-rules.md)全部都可以应用到事件。 | 使用此触发器的 playbook 不能从 Azure Sentinel 手动运行。<br><br>使用此触发器的 playbook 不支持警报分组，这意味着它们将仅接收每个事件发送的第一个警报。
| “警报触发器” | “触发对 Azure Sentinel 警报的响应时” | 建议用于需要在 Azure Sentinel 门户上针对警报手动运行的 playbook，或用于不生成警报事件的“计划”分析规则。 | 此触发器不能用于自动响应由“Microsoft 安全”分析规则生成的警报。<br><br>“自动化规则”无法调用使用此触发器的 playbook。 |
|

这两个流使用的架构并不相同。
建议的做法是使用适用于大多数情况的“Azure Sentinel 事件触发器”流。

### <a name="incident-dynamic-fields"></a>事件动态字段

从“触发 Azure Sentinel 事件创建规则时”接收的“事件”对象包括以下动态字段： 

- 事件属性（显示为“Incident: 字段名称”）

- 警报（数组）

  - 警报属性（显示为“Alert: 字段名称”）

    选择警报属性（如“Alert: \<property name>”）时，将自动生成一个“for each”循环，因为事件可能包含多个警报。

- 实体（所有警报实体的数组）

- 工作区信息字段（适用于在其中创建事件的 Sentinel 工作区）
  - 订阅 ID
  - 工作区名称
  - 工作区 ID
  - 资源组名称

## <a name="azure-sentinel-actions-summary"></a>Azure Sentinel 操作摘要

| 组件 | 何时使用 |
| --------- | -------------- |
| “警报 - 获取事件” | 在以警报触发器开头的 playbook 中。 可用于获取事件属性，或检索“事件 ARM ID”以与“更新事件”或“向事件添加注释”操作一起使用。   |
| **获取事件** | 从外部源或使用非 Sentinel 触发器触发 playbook 时。 使用“事件 ARM ID”标识。 检索事件属性和注释。 |
| **更新事件** | 更改事件的“状态”（例如，关闭事件时），分配“所有者”，添加或删除标记，或更改其“严重性”、“标题”或“说明”。    
| “向事件添加注释” | 用从外部源收集的信息扩充事件；审核 playbook 对实体执行的操作；提供对事件调查有用的其他信息。 |
| “实体-获取 \<*entity type*\>” | 在处理创建 playbook 时已知的特定实体类型（“IP”、“帐户”、“主机”、“URL”或“FileHash”）的 playbook 中，并且需要能够对其进行分析和处理其唯一字段。     |
|

## <a name="work-with-incidents---usage-examples"></a>处理事件 - “用法”示例

> [!TIP] 
> 操作“更新事件”和操作“向事件添加注释”需要“事件 ARM ID”。   <br>
事先使用“警报-获取事件”操作来获取“事件 ARM ID”。 

### <a name="update-an-incident"></a>更新事件
-  “创建事件时”触发 playbook

    ![事件触发器简单“更新”流示例](media/playbook-triggers-actions/incident-simple-flow.png)

-  “生成警报时”触发 playbook

    ![警报触发器简单“更新事件”流示例](media/playbook-triggers-actions/alert-update-flow.png)
      
### <a name="use-incident-information"></a>使用事件信息

用于通过邮件发送事件详细信息的基本 playbook：
-  “创建事件时”触发 playbook

    ![事件触发器简单“获取”流示例](media/playbook-triggers-actions/incident-simple-mail-flow.png)

-  “生成警报时”触发 playbook

    ![警报触发器简单“获取事件”流示例](media/playbook-triggers-actions/alert-simple-mail-flow.png)

### <a name="add-a-comment-to-the-incident"></a>向事件添加注释

-  “创建事件时”触发 playbook

    ![事件触发器简单添加注释示例](media/playbook-triggers-actions/incident-comment.png)

-  “生成警报时”触发 playbook

    ![“事件触发器简单添加注释示例”](media/playbook-triggers-actions/alert-comment.png)

## <a name="work-with-specific-entity-types"></a>处理特定实体类型

“实体”动态字段是 JSON 对象的数组，其中每个对象都表示一个实体。 每个实体类型都有自己的架构，具体取决于它的唯一属性。

“实体 - 获取 \<entity name>”操作可执行以下操作：

- 按请求的类型筛选实体数组。
- 分析此类型的特定字段，使其可以在进一步操作中用作动态字段。

输入是“实体”动态字段。

响应是实体的数组，其中特殊属性经过分析，并可直接在“For each”循环中使用。

当前支持的实体类型包括：

- [IP](/connectors/azuresentinel/#entities---get-ips)
- [主机](/connectors/azuresentinel/#entities---get-hosts)
- [帐户](/connectors/azuresentinel/#entities---get-accounts)
- [URL](/connectors/azuresentinel/#entities---get-urls)
- [FileHash](/connectors/azuresentinel/#entities---get-filehashes)

    :::image type="content" source="media/playbook-triggers-actions/entities-actions.png" alt-text="实体操作列表":::

对于其他实体类型，可以使用逻辑应用的内置操作来实现类似的功能：

- 使用[“筛选数组”](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)，按请求的类型筛选实体数组。

- 使用[“分析 JSON”](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)，分析此类型的特定字段，使其可以在进一步操作中用作动态字段。

## <a name="work-with-custom-details"></a>处理自定义详细信息

“事件触发器”中提供的“警报自定义详细信息”动态字段是 JSON 对象的数组，其中每个对象表示一个警报的自定义详细信息。  应该知道，[自定义详细信息](surface-custom-details-in-alerts.md)是键值对，可用于从警报中的事件呈现信息，以便可以作为事件的一部分来表示、跟踪和分析。

由于警报中的此字段是可自定义的，因此其架构取决于要呈现的事件的类型。 必须提供此事件实例的数据，以生成用于确定如何分析自定义详细信息字段的架构。

请参阅以下示例：

![分析规则中定义的自定义详细信息。](./media/playbook-triggers-actions/custom-details-values.png)

在这些键值对中，键（左侧列）表示创建的自定义字段，值（右侧列）表示填充自定义字段的事件数据中的字段。

可以提供以下 JSON 代码来生成架构。 该代码将键名称显示为数组，将值（显示为实际值，而不是包含值的列）显示为数组中的项。

```json
{ "FirstCustomField": [ "1", "2" ], "SecondCustomField": [ "a", "b" ] }
```

1. 使用“分析 JSON”内置操作添加新步骤。 可以在“搜索”字段中输入“parse json”来查找。

1. 在事件触发器下的“动态内容”列表中查找并选择“警报自定义详细信息”。 

    ![从“动态内容”中选择“警报自定义详细信息”。](./media/playbook-triggers-actions/custom-details-dynamic-field.png)

    这将创建“For each”循环，因为事件包含警报数组。

1. 单击“使用示例有效负载生成架构”链接。

    ![选择“使用示例有效负载生成架构”链接](./media/playbook-triggers-actions/generate-schema-link.png)

1. 提供示例有效负载。 可以通过在日志分析（“日志”边栏选项卡）中查找此警报的另一个实例并复制自定义详细信息对象（在“扩展属性”下），来查找示例有效负载。  在下面的屏幕截图中，我们使用了上面所示的 JSON 代码。

    ![输入示例 JSON 有效负载。](./media/playbook-triggers-actions/sample-payload.png)

1. 自定义字段是可供使用的“数组”类型的动态字段。 可以在此处查看数组及其项，包括架构中和显示在"动态内容"下的列表中，如上所述。

    ![架构中的字段可供使用。](./media/playbook-triggers-actions/fields-ready-to-use.png)
    
## <a name="next-steps"></a>后续步骤

本文介绍了在 Azure Sentinel playbook 中使用触发器和操作来应对威胁。 
- 了解如何使用 Azure Sentinel 来[主动搜寻威胁](hunting.md)。