---
title: 在 Azure 数据工厂中创建自定义事件触发器
description: 了解如何在 Azure 数据工厂中创建运行管道的自定义触发器，以便响应发布到事件网格的自定义事件。
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 2d2f26b24e7fa10d9244de8f99d78c64a44b3d61
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785642"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>创建运行管道的触发器来响应自定义事件（预览版）

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍了可以在数据工厂管道中创建的自定义事件触发器。

事件驱动的体系结构 (EDA) 是一种常见数据集成模式，其中涉及到事件的生成、检测、消耗和响应。 数据集成方案通常要求数据工厂客户基于正在发生的某些事件触发管道。 数据工厂与 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)的原生集成现在涵盖[自定义事件](../event-grid/custom-topics.md)：客户向事件网格主题发送任意事件，数据工厂订阅和侦听主题并相应地触发管道。

> [!NOTE]
> 本文中所介绍的集成依赖于 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)。 请确保订阅已注册事件网格资源提供程序。 有关详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。 你必须能够执行 Microsoft.EventGrid/eventSubscriptions/* 操作。 此操作属于 EventGrid EventSubscription 参与者内置角色。

而且，通过组合管道参数和自定义事件触发器，客户可以在管道运行中分析和引用自定义数据有效负载。 自定义事件有效负载中的数据字段是一个自由格式的 json 键-值结构，允许客户最大程度地控制事件驱动的管道运行。

> [!IMPORTANT]
> 有时，自定义事件有效负载中可能会缺少参数化中引用的键。 触发器运行会失败并显示错误，指出无法评估表达式，因为属性 keyName 不存在。 事件不会触发管道运行。

## <a name="setup-event-grid-custom-topic"></a>设置事件网格自定义主题

若要在数据工厂中使用自定义事件触发器，需要先设置[事件网格中的自定义主题](../event-grid/custom-topics.md)。 此工作流不同于存储事件触发器，在后者中，数据工厂将替你设置主题。 而在此工作流中，你需要在 Azure 事件网格中导航并自行创建主题。 若要详细了解如何创建自定义主题，请参阅 Azure 事件网格[门户教程](../event-grid/custom-topics.md#azure-portal-tutorials)和 [CLI 教程](../event-grid/custom-topics.md#azure-cli-tutorials)

数据工厂预期事件会遵循[事件网格事件架构](../event-grid/event-schema.md)。 请确保事件有效负载具有以下字段。

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

## <a name="data-factory-ui"></a>数据工厂 UI

本部分介绍了如何在 Azure 数据工厂用户界面中创建存储事件触发器。

1. 切换到“编辑”选项卡（显示为铅笔符号）。

1. 在菜单上选择“触发器”，然后选择“新建/编辑” 。

1. 在“添加触发器”页上，选择“选择触发器...”，然后选择“+新建”  。

1. 选择“自定义事件”触发器类型

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="屏幕截图显示了用于在数据工厂 UI 中创建新的自定义事件触发器的“创作”页。" lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. 从 Azure 订阅下拉列表中选择你的自定义主题，或手动输入事件主题范围。

   > [!NOTE]
   > 若要创建新的自定义事件触发器或修改现有的自定义事件触发器，用于登录到数据工厂并发布存储事件触发器的 Azure 帐户必须对主题具有相应的基于角色的访问控制 (Azure RBAC) 权限。 不需要额外的权限：Azure 数据工厂的服务主体不需要对事件网格具有特殊权限。 有关访问控制的详细信息，请参阅[基于角色的访问控制](#role-based-access-control)部分。

1. “主题开头为”和“主题结尾为”属性可用于筛选你要为其触发管道的事件。 这两个属性都是可选的。

1. 使用“+ 新建”添加要筛选的“事件类型”。 自定义事件触发器对列表采用 OR 关系：如果自定义事件的 eventType 属性与此处列出的任何项匹配，则会触发管道运行。 事件类型不区分大小写。 例如，在下面的屏幕截图中，触发器将所有 copycompleted 或 copysucceeded 事件与开头为 factories 的主题进行匹配。

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="“编辑触发器”页面的屏幕截图，其中说明了数据工厂 UI 中的“事件类型”和“主题筛选”。":::

1. 自定义事件触发器可以分析自定义数据有效负载，并将其发送到管道。 首先创建管道参数，并在“参数”页上填写值。 使用 @triggerBody().event.data.keyName 格式分析数据有效负载，并将值传递给管道参数。 有关详细说明，请参阅[在管道中引用触发器元数据](how-to-use-trigger-parameterization.md)和[自定义事件触发器中的系统变量](control-flow-system-variables.md#custom-event-trigger-scope)

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="屏幕截图显示了管道参数设置。":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="屏幕截图显示了在自定义事件中引用数据有效负载的“参数”页。":::

1. 完成后，单击“确定”。

## <a name="json-schema"></a>JSON 架构

下表概述了与自定义事件触发器相关的架构元素：

| **JSON 元素** | **说明** | 类型 | **允许的值** | **必需** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **作用域** | 事件网格主题的 Azure 资源管理器资源 ID。 | String | Azure 资源管理器 ID | 是 |
| **events** | 导致此触发器触发的事件的类型。 | 字符串数组    |  | 是，至少需要一个值 |
| **subjectBeginsWith** | “主题”字段必须以所提供的模式开头，触发器才会触发。 例如，只有当事件主题以 `factories` 开头时，`factories` 才会触发触发器。 | String   | | 否 |
| **subjectEndsWith** | “主题”字段必须以所提供的模式结尾，触发器才会触发。 | String   | | 否 |

## <a name="role-based-access-control"></a>基于角色的访问控制

Azure 数据工厂使用 Azure 基于角色的访问控制 (Azure RBAC) 来确保严格禁止未经授权侦听自定义事件、订阅自定义事件中的更新，以及触发链接到自定义事件的管道。

* 若要成功创建新的或更新现有的自定义事件触发器，登录到数据工厂的 Azure 帐户需要拥有对相关存储帐户的适当访问权限。 否则，操作会失败，并出现“访问被拒绝”错误消息。
* 数据工厂不需要拥有对事件网格的特殊权限，你无需为操作的数据工厂服务主体分配特殊的 Azure RBAC 权限。

具体而言，客户需要对“/subscriptions/####/resourceGroups//####/providers/Microsoft.EventGrid/topics/someTopics”具有“Microsoft.EventGrid/EventSubscriptions/Write”权限。

## <a name="next-steps"></a>后续步骤

* 有关触发器的详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md#trigger-execution)。
* 了解如何引用管道中的触发器元数据，详见[引用管道运行中的触发器元数据](how-to-use-trigger-parameterization.md)
