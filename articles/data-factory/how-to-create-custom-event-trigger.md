---
title: 在 Azure 数据工厂中创建自定义事件触发器
description: 了解如何在 Azure 数据工厂中创建运行管道的触发器，以便响应发布到事件网格的自定义事件。
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: dcad59c6434f6751cba8633868a9dc9969ffc0ac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610519"
---
# <a name="create-a-custom-event-trigger-to-run-a-pipeline-in-azure-data-factory"></a>创建自定义事件触发器以在 Azure 数据工厂中运行管道

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

事件驱动的体系结构 (EDA) 是一种常见数据集成模式，其中涉及到事件的生成、检测、消耗和响应。 数据集成方案通常要求 Azure 数据工厂客户在特定事件发生时触发管道。 数据工厂与 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)的原生集成现在涵盖[自定义主题](../event-grid/custom-topics.md)。 将事件发送到事件网格主题。 数据工厂订阅主题，侦听并相应地触发管道。

> [!NOTE]
> 本文中所介绍的集成依赖于 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)。 请确保订阅已注册事件网格资源提供程序。 有关详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。 你必须能够执行 `Microsoft.EventGrid/eventSubscriptions/` 操作。 此操作属于 [EventGrid EventSubscription 参与者](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor)内置角色。

如果将管道参数与自定义事件触发器组合，则可以在管道运行中分析和引用自定义的 `data` 有效负载。 由于自定义事件有效负载中的 `data` 字段是自由格式的 JSON 键值结构，因此你可以控制事件驱动的管道运行。

> [!IMPORTANT]
> 如果自定义事件有效负载中缺少以参数方式引用的密钥，`trigger run` 将失败。 你收到一个错误，指出由于 `keyName` 属性不存在，无法计算表达式的值。 在这种情况下，事件不会触发 `pipeline run`。

## <a name="set-up-a-custom-topic-in-event-grid"></a>在事件网格中设置自定义主题

若要在数据工厂中使用自定义事件触发器，需要先设置[事件网格中的自定义主题](../event-grid/custom-topics.md)。

转到 Azure 事件网格并自行创建主题。 若要详细了解如何创建自定义主题，请参阅 Azure 事件网格[门户教程](../event-grid/custom-topics.md#azure-portal-tutorials)和 [CLI 教程](../event-grid/custom-topics.md#azure-cli-tutorials)。

> [!NOTE]
> 工作流不同于存储事件触发器。 此处，数据工厂不会设置主题。

数据工厂预期事件会遵循[事件网格事件架构](../event-grid/event-schema.md)。 请确保事件有效负载具有以下字段：

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="use-data-factory-to-create-a-custom-event-trigger"></a>使用数据工厂创建自定义事件触发器

1. 转到 Azure 数据工厂并登录。

1. 切换到“编辑”选项卡。查找铅笔图标。

1. 在菜单上选择“触发器”，然后选择“新建/编辑”。 

1. 在“添加触发器”页中选择“选择触发器”，然后选择“+新建”。  

1. 为“类型”选择“自定义事件”。 

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="数据工厂 UI 中用于新建自定义事件触发器的“创作”页的屏幕截图。" lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. 从 Azure 订阅下拉列表中选择你的自定义主题，或手动输入事件主题范围。

   > [!NOTE]
   > 要在数据工厂中创建或修改自定义事件触发器，需要使用具有适当的基于角色的访问控制 (Azure RBAC) 的 Azure 帐户。 不需要其他权限。 数据工厂服务主体不需要对事件网格的特殊权限。 有关访问控制的详细信息，请参阅[基于角色的访问控制](#role-based-access-control)部分。

1. “主题开头为”和“主题结尾为”属性可用于筛选触发器事件。 这两个属性都是可选的。

1. 使用“+ 新建”添加要筛选的“事件类型”。 自定义事件触发器列表使用 OR 关系。 如果某个自定义事件的 `eventType` 属性与列表中的属性匹配，则将触发管道运行。 事件类型不区分大小写。 例如，在下面的屏幕截图中，触发器匹配主题以“factories”开头的所有 `copycompleted` 或 `copysucceeded` 事件。

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="“编辑触发器”页的屏幕截图，用于说明数据工厂 UI 中的“事件类型”和“主题”筛选。":::

1. 自定义事件触发器可以分析自定义 `data` 数据有效负载，并将其发送到管道。 创建管道参数，然后在“参数”页上填写值。 使用 `@triggerBody().event.data._keyName_` 格式分析数据有效负载，并将值传递给管道参数。
 
   有关详细说明，请参阅以下文章：
   - [管道中的引用触发器元数据](how-to-use-trigger-parameterization.md)
   - [自定义事件触发器中的系统变量](control-flow-system-variables.md#custom-event-trigger-scope)

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="屏幕截图显示了管道参数设置。":::

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="屏幕截图显示了在自定义事件中引用数据有效负载的“参数”页。":::

1. 输入参数后，选择“确定”。

## <a name="advanced-filtering"></a>高级筛选

自定义事件触发器支持高级筛选功能，类似于[事件网格高级筛选](../event-grid/event-filtering.md#advanced-filtering)。 这些条件筛选器允许管道基于事件有效负载的值进行触发。 例如，事件有效负载中可能有一个名为“Department”的字段，管道仅应在“Department”等于“Finance”时触发。   你还可以指定复杂的逻辑，例如，date 字段在列表 [1, 2, 3, 4, 5] 中，month 字段不在列表 [11, 12] 中，tag 字段包含 ['Fiscal Year 2021', 'FiscalYear2021', 'FY2021'] 中的任意一项。

 :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-5-advanced-filters.png" alt-text="屏幕截图显示了如何为客户事件触发器设置高级筛选器":::

截至目前，自定义事件触发器仅支持事件网格中的一部分[高级筛选运算符](../event-grid/event-filtering.md#advanced-filtering)。 支持以下筛选条件：

* NumberIn
* NumberNotIn
* NumberLessThan
* NumberGreaterThan
* NumberLessThanOrEquals
* NumberGreaterThanOrEquals
* BoolEquals
* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

单击“+新建”以添加新的筛选条件。 

此外，自定义事件触发器遵循[与事件网格相同的限制](../event-grid/event-filtering.md#limitations)，包括：

* 每个自定义事件触发器有 5 个高级筛选器，所有筛选器有 25 个筛选器值
* 每个字符串值有 512 个字符
* “in”和“not in”运算符有 5 个值
* 键中不能含有 `.`（点）字符，例如 `john.doe@contoso.com`。 目前不支持键中使用转义字符。
* 可以在多个筛选器中使用相同的键。

数据工厂依赖于[事件网格 API](../event-grid/whats-new.md) 的最新正式发布版本。 随着新的 API 版本不断正式发布，数据工厂将扩展其对更多高级筛选运算符的支持。

## <a name="json-schema"></a>JSON 架构

下表概述了与自定义事件触发器相关的架构元素：

| JSON 元素 | 说明 | 类型 | 允许的值 | 必需 |
|---|----------------------------|---|---|---|
| `scope` | 事件网格主题的 Azure 资源管理器资源 ID。 | 字符串 | Azure 资源管理器 ID | 是 |
| `events` | 导致此触发器触发的事件的类型。 | 字符串数组    |  | 是，至少需要一个值。 |
| `subjectBeginsWith` | `subject` 字段必须以所提供的模式开头，触发器才会触发。 例如，factories 仅会针对以 factories 开头的事件主题引发触发器。 | 字符串   | | 否 |
| `subjectEndsWith` | `subject` 字段必须以所提供的模式结尾，触发器才会触发。 | 字符串   | | 否 |
| `advancedFilters` | JSON blob 的列表，每个指定一个筛选条件。 每个 blob 都指定 `key`、`operatorType` 和 `values`。 | JSON blob 的列表 | | 否 |

## <a name="role-based-access-control"></a>基于角色的访问控制

Azure 数据工厂使用 Azure 基于角色的访问控制 (RBAC) 来禁止未经授权的访问。 若要正常工作，数据工厂需要访问权限以执行以下操作：
- 侦听事件。
- 订阅事件更新。
- 触发链接到自定义事件的管道。

若要成功创建或更新自定义事件触发器，需要使用具有相应访问权限的 Azure 帐户登录到数据工厂。 否则，操作将失败并且出现“拒绝访问”错误。

数据工厂不需要对事件网格具有特殊权限。 也不需要为操作的数据工厂服务主体分配特殊 Azure RBAC 权限。

具体来说，您需要具有 `/subscriptions/####/resourceGroups//####/providers/Microsoft.EventGrid/topics/someTopics` 的 `Microsoft.EventGrid/EventSubscriptions/Write` 权限。

## <a name="next-steps"></a>后续步骤

* 获取有关[触发器执行](concepts-pipeline-execution-triggers.md#trigger-execution)的详细信息。
* 了解如何[在管道中引用触发器元数据](how-to-use-trigger-parameterization.md)。
