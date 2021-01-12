---
title: Log Analytics 中的 IT 服务管理连接器
description: 本文概述了 IT 服务管理连接器 (ITSMC) ，以及如何使用它来监视和管理 Log Analytics 中的 ITSM 工作项并快速解决问题。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 717a1bc4361ba4a7366f4864c1fe44f93b6f4b5e
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127849"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>使用 IT 服务管理连接器将 Azure 连接到 ITSM 工具

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

本文提供了有关如何在 Log Analytics 中配置 IT 服务管理连接器 (ITSMC) 以集中管理工作项的信息。

## <a name="add-it-service-management-connector"></a>添加 IT 服务管理连接器

你需要添加 ITSMC，然后才能创建连接。

1. 在 Azure 门户中，选择 " **创建资源**"：

   ![显示 "创建资源" 菜单项的屏幕截图。](media/itsmc-overview/azure-add-new-resource.png)

2. 在 Azure Marketplace 中搜索 **IT 服务管理连接器** 。 选择 " **创建**"：

   ![屏幕截图，显示 Azure Marketplace 中的 "创建" 按钮。](media/itsmc-overview/add-itsmc-solution.png)

3. 在 " **LA 工作区** " 部分中，选择要在其中安装 ITSMC 的 Azure Log Analytics 工作区。
   >[!NOTE]
   >
   > * ITSMC 只能安装在以下区域 Log Analytics 工作区中：美国东部、美国西部2、美国中南部、美国西部、US Gov 亚利桑那州、US Gov 弗吉尼亚州、加拿大中部、西欧、东南亚、东南亚、日本东部、印度中部、东南亚、日本东部、印度中部、澳大利亚东南部和澳大利亚东南部。

4. 在 " **Log Analytics 工作区** " 部分中，选择要在其中创建 ITSMC 资源的资源组：

   ![显示 "Log Analytics 工作区" 部分的屏幕截图。](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >作为 Microsoft Operations Management Suite (OMS) 到 Azure Monitor 的持续转换的一部分，OMS 工作区现在称为 *Log Analytics 工作区*。

5. 选择“确定”  。

部署 ITSMC 资源后，窗口右上角会出现一个通知。

## <a name="create-an-itsm-connection"></a>创建 ITSM 连接

安装 ITSMC 后，可以创建连接。

若要创建连接，需要准备 ITSM 工具以允许来自 ITSMC 的连接。  

根据你要连接到的 ITSM 产品，选择以下链接之一来了解相关说明：

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

准备好 ITSM 工具后，请完成以下步骤以创建连接：

1. 在 " **所有资源**" 中，查找 **ServiceDesk (*你的工作区名称*)**：

   ![显示 Azure 门户中最近的资源的屏幕截图。](media/itsmc-definition/create-new-connection-from-resource.png)

1. 在左侧窗格中的 " **工作区数据源** " 下，选择 " **ITSM 连接**：

   ![显示 ITSM 连接菜单项的屏幕截图。](media/itsmc-overview/add-new-itsm-connection.png)
1. 选择 " **添加连接**"。

1. 根据 ITSM 产品/服务的说明指定连接设置：

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   >
   > 默认情况下，ITSMC 每24小时刷新一次连接的配置数据。 若要立即刷新连接的数据以反映所做的任何编辑或模板更新，请在连接的边栏选项卡上选择 " **同步** " 按钮：
   >
   > ![显示连接边栏选项卡上的 "同步" 按钮的屏幕截图。](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>使用 ITSMC

   你可以使用 ITSMC 来创建从 Azure Monitor 警报到 ITSM 工具的警报。

## <a name="create-itsm-work-items-from-azure-alerts"></a>根据 Azure 警报创建 ITSM 工作项

创建 ITSM 连接后，可以基于 Azure 警报在 ITSM 工具中创建工作项。 若要创建工作项，请使用操作组中的 ITSM 操作。

操作组为你的 Azure 警报提供模块化且可重用的方法来触发操作。 可以在 Azure 门户中将操作组与指标警报、活动日志警报和 Azure Log Analytics 警报一起使用。

> [!NOTE]
> 创建 ITSM 连接后，需要等待30分钟，同步过程才能完成。

### <a name="template-definitions"></a>模板定义

   可以使用 ITSM 工具定义的模板的工作项类型。
通过使用模板，可以定义将根据定义为操作组一部分的固定值自动填充的字段。 可在 ITSM 工具中定义模板。
您可以定义要用作操作组定义的一部分的模板。

使用以下过程来创建操作组：

1. 在 Azure 门户中，选择 "  **警报**"。
2. 在屏幕顶部的菜单中，选择 " **管理操作**"：

    ![显示 "管理操作" 菜单项的屏幕截图。](media/itsmc-overview/action-groups-selection-big.png)

   此时将显示 " **创建操作组** " 窗口。

3. 选择要在其中创建操作组的 **订阅** 和 **资源组** 。 为操作组提供 " **操作组名称** " 和 " **显示名称** "。 选择 " **下一步：通知**"。

    ![显示 "创建操作组" 窗口的屏幕截图。](media/itsmc-overview/action-groups-details.png)

4. 在通知列表中，选择 " **下一步：操作**"。
5. 在 "操作" 列表中，选择 "**操作类型**" 列表中的 **ITSM** 。 提供操作的 **名称** 。 选择表示 **编辑详细信息** 的笔按钮。

    ![显示操作组定义的屏幕截图。](media/itsmc-definition/action-group-pen.png)

6. 在 " **订阅** " 列表中，选择 Log Analytics 工作区所在的订阅。 在 " **连接** " 列表中，选择 ITSM 连接器名称。 后跟工作区名称。 例如，MyITSMConnector (MyWorkspace) 。

7. 选择 **工作项** 类型。

8. 如果要使用固定值填写现成字段，请选择 " **使用自定义模板**"。 否则，请在 "**模板**" 列表中选择现有 [模板](#template-definitions)，然后在模板字段中输入固定值。

9. 在操作 ITSM 组定义的最后一部分中，你可以定义将为每个警报创建的工作项的数目。

    >[!NOTE]
    >
    > * 本部分仅适用于日志搜索警报。
    > * 对于所有其他警报类型，将为每个警报创建一个工作项。

    * 在 "工作项" 下拉 "事件" 或 "警报" 中选择的情况下，将 ![ 显示 "ITSM 事件" 窗口的屏幕截图。](media/itsmc-overview/itsm-action-configuration.png)
        * 如果选中 **"为每个配置项目创建单独的工作项"** 复选框，则每个警报中的每个配置项都将创建一个新的工作项。 由于相同配置项目的多个警报会受到影响，因此每个配置项目将有多个工作项。

             例如：
             1) 包含3个配置项目的警报1： A、B、C-将创建3个工作项。
             2) 警报2，其中包含1个配置项目： A-将创建1个工作项。

        * 如果清除 **"为每个配置项目创建单独的工作项"** 复选框，则 ITSM 连接器将为每个警报规则创建一个工作项，并将所有受影响的配置项追加到该工作项。 如果上一个工作项已关闭，则将创建一个新的工作项。

        >[!NOTE]
        > 在这种情况下，某些触发的警报不会在 ITSM 工具中生成新的工作项。

        例如：
         1) 包含3个配置项目的警报1： A、B、C-将创建1个工作项。
         2) 与1个配置项目中的步骤 a 相同的警报规则的警报2： D-D 将附加到步骤 a 中创建的工作项中受影响的配置项列表。
         3) 警报3对于具有1个配置项目的不同警报规则： E-将创建1个工作项。

    * 在 "工作项" 下拉列表中选择 "事件"： ![ 屏幕截图，显示 "ITSM" 事件窗口。](media/itsmc-overview/itsm-action-configuration-event.png)

        * 如果选择 **"为每个日志项创建单独的工作项 (则不会填充配置项目字段。可能会导致大量的工作项。 ) "** 在单选按钮选择中，将根据日志搜索警报查询的搜索结果中的每一行创建一个工作项。 工作项的负载中的 "说明" 属性将包含搜索结果中的行。
        * 如果在单选按钮选择中选择 **"为每个配置项目创建单独的工作项"** ，则每个警报中的每个配置项都将创建新的工作项。 在 ITSM 系统中，每个配置项目可以有多个工作项。 这与选中 "事件/警报" 一节中的复选框相同。

10. 选择“确定”  。

当你创建或编辑 Azure 警报规则时，使用具有 ITSM 操作的操作组。 当警报触发时，会在 ITSM 工具中创建或更新工作项。

> [!NOTE]
>
>- 有关 ITSM 操作定价的信息，请参阅操作组的 [定价页](https://azure.microsoft.com/pricing/details/monitor/) 。
>
>
>- 当使用 ITSM 操作发送警报规则时，警报规则定义中的简短说明字段限制为40个字符。

## <a name="next-steps"></a>后续步骤

* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)
