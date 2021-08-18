---
title: 从 Power Automate 和 Power Apps 调用逻辑应用
description: 通过将逻辑应用作为连接器导出，从 Microsoft Power Automate 流调用逻辑应用。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 0c66f4bd7e311bdaedefe68327fc34d373fc6fec
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2021
ms.locfileid: "113301647"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>从 Power Automate 和 Power Apps 调用逻辑应用

若要从 Microsoft Power Automate 和 Microsoft Power Apps 调用逻辑应用，可将逻辑应用导出为连接器。 在 Power Automate 或 Power Apps 环境中将逻辑应用公开为自定义连接器时，可从该环境的流中调用逻辑应用。

如果要将流从 Power Automate 或 Power 迁移到逻辑应用，请查看[从 Power Automate 导出流并将其部署到 Azure 逻辑应用](export-from-microsoft-flow-logic-app-template.md)。

> [!NOTE]
> 并非所有 Power Automate 连接器都可在 Azure 逻辑应用中使用。 只能迁移在 Azure 逻辑应用中具有等效连接器的 Power Automate 流。 例如，按钮触发器、审批连接器和通知连接器特定于 Power Automate。 目前，Power Automate 中基于 OpenAPI 的流不支持作为逻辑应用模板进行导出和部署。
>
> * 若要查找没有逻辑应用等效项的 Power Automate 连接器，请查看 [Power Automate 连接器](/connectors/connector-reference/connector-reference-powerautomate-connectors)。
>
> * 若要查找没有 Power Automate 等效项的逻辑应用连接器，请查看[逻辑应用连接器](/connectors/connector-reference/connector-reference-logicapps-connectors)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* Power Automate 或 Power Apps 许可证。

* 具有导出请求触发器的逻辑应用。

* Power Automate 或 Power Apps 中的流，你要从中调用逻辑应用。

## <a name="export-your-logic-app-as-a-custom-connector"></a>将逻辑应用导出为自定义连接器

必须先将逻辑应用导出为自定义连接器，然后才能从 Power Automate 或 Power Apps 调用逻辑应用。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户的搜索框中，输入 `Logic Apps`。 在结果中的“服务”下，选择“逻辑应用” 。

1. 选择要导出的逻辑应用。

1. 在逻辑应用的菜单中，选择“导出”。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Azure 门户中逻辑应用页的屏幕截图，显示选中菜单中的“导出”按钮。":::

1. 在“导出”窗格的“名称”中，为逻辑应用的自定义连接器输入一个名称 。 从“环境”列表中，选择要从其中调用逻辑应用的 Power Automate 或 Power Apps 环境。 完成后，请选择“确定”。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="逻辑应用导出窗格的屏幕截图，显示自定义连接器名称和环境的必填字段。":::

1. 若要确认是否已成功导出逻辑应用，请选中“通知”窗格。

### <a name="exporting-errors"></a>导出错误

以下是将逻辑应用导出为自定义连接器时可能发生的错误及其建议解决方案：

* 获取环境失败。确保已为 Power Automate 配置帐户，然后重试。：检查 Azure 帐户是否具有 Power Automate 计划。

* 无法导出当前逻辑应用。若要导出，请选择具有请求触发器的逻辑应用。：检查逻辑应用是否以[请求触发器](./logic-apps-workflow-actions-triggers.md#request-trigger)开头。

## <a name="connect-to-your-logic-app-from-power-automate"></a>从 Power Automate 连接到逻辑应用

若要连接到通过 Power Automate 流导出的逻辑应用：

1. 登录到 [Power Automate](https://flow.microsoft.com)。

1. 从“Power Automate”主页菜单中，选择“我的流” 。

1. 在“流”页上，选择要连接到逻辑应用的流。

1. 从流页的菜单中，选择“编辑”。

1. 在流编辑器中，选择“&#43; 新建步骤”。

1. 在“选择操作”下的搜索框中，输入逻辑应用连接器的名称。 （可选）若要仅显示环境中的自定义连接器，请选择“自定义”选项卡来筛选结果。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Power Automate 流编辑器的屏幕截图，显示为自定义连接器添加的新步骤以及可用操作。":::

1. 选择要对逻辑应用连接器执行的操作。 

1. 提供操作传递到逻辑应用连接器的信息。

1. 若要保存更改，请在 Power Automate 编辑器菜单中选择“保存”。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在逻辑应用服务中，找到已导出的逻辑应用。

1. 确认逻辑应用按照你在 Power Automate 流中预期的方式工作。

## <a name="delete-logic-app-connector-from-power-automate"></a>从 Power Automate 中删除逻辑应用连接器

1. 登录到 [Power Automate](https://flow.microsoft.com)。

1. 在“Power Automate”主页中，选择菜单中的“数据”&gt;“自定义连接器”  。

1. 在列表中，找到自定义连接器，然后选择省略号 (...) 按钮 &gt;“删除” 。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Power Automate 自定义连接器页的屏幕截图，其中显示逻辑应用的自定义连接器管理按钮。":::

1. 选择“确定”以确认删除。

## <a name="connect-to-your-logic-app-from-power-apps"></a>从 Power Apps 连接到逻辑应用

若要连接到通过 Power Apps 流导出的逻辑应用：

1. 登录到 [Power Apps](https://powerapps.microsoft.com/)。

1. 在Power Apps 主页中，选择菜单中的“流” 。

1. 在“流”页上，选择要连接到逻辑应用的流。

1. 在流页上，在流的菜单中选择“编辑”。

1. 在流编辑器中，选择“&#43; 新建步骤”按钮。

1. 在新步骤中的“选择操作”下，输入逻辑应用连接器的名称。 （可选）通过“自定义”选项卡筛选结果，仅查看环境中的自定义连接器。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Power Apps 流编辑器的屏幕截图，显示为自定义连接器添加的新步骤以及可用操作。":::

1. 选择要对连接器执行的操作。 

1. 配置操作传递到逻辑应用连接器的信息。

1. 在 Power Apps 编辑器菜单中，选择“保存”以保存更改。 

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在逻辑应用服务中，找到已导出的逻辑应用。

1. 确认逻辑应用按照你在 Power Apps 流中预期的方式工作。

## <a name="delete-logic-app-connector-from-power-apps"></a>从 Power Apps 中删除逻辑应用连接器

1. 登录到 [Power Apps](https://powerapps.microsoft.com)。

1. 在“Power Apps”主页中，选择菜单中的“数据”&gt;“自定义连接器”  。

1. 在列表中，找到自定义连接器，然后选择省略号 (...) 按钮 &gt;“删除” 。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Power Apps 自定义连接器页的屏幕截图，其中显示逻辑应用的自定义连接器管理按钮。":::

1. 选择“确定”以确认删除。

## <a name="next-steps"></a>后续步骤

* 详细了解[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)
* 详细了解[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)
