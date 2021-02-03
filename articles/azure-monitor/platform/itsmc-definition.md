---
title: Log Analytics 中的 IT 服务管理连接器
description: 本文概述了 IT 服务管理连接器 (ITSMC) ，以及如何使用它来监视和管理 Log Analytics 中的 ITSM 工作项并快速解决问题。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: ba32cfa4bc5cd0b41a210cf88fb598afc3064495
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492546"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>使用 IT 服务管理连接器将 Azure 连接到 ITSM 工具

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

本文介绍如何在 Log Analytics 中配置 IT 服务管理连接器 (ITSMC) ，以便集中管理 IT 服务管理 (ITSM) 工作项。

## <a name="add-it-service-management-connector"></a>添加 IT 服务管理连接器

你需要安装 ITSMC，然后才能创建连接。

1. 在 Azure 门户中，选择 " **创建资源**"：

   ![显示用于创建资源的菜单项的屏幕截图。](media/itsmc-overview/azure-add-new-resource.png)

2. 在 Azure Marketplace 中搜索 **IT 服务管理连接器** 。 然后选择“创建”：

   ![屏幕截图，显示 Azure Marketplace 中的 "创建" 按钮。](media/itsmc-overview/add-itsmc-solution.png)

3. 在 " **LA 工作区** " 部分中，选择要在其中安装 ITSMC 的 Log Analytics 工作区。
   > [!NOTE]
   > 只能在以下区域 Log Analytics 工作区中安装 ITSMC：美国东部、美国西部2、美国中南部、美国西部、US Gov 亚利桑那州、US Gov 弗吉尼亚州、加拿大中部、西欧、东南亚、东南亚、日本东部、印度中部、东南亚、日本东部、印度中部、澳大利亚东南部和澳大利亚东南部。

4. 在 " **Log Analytics 工作区** " 部分中，选择要在其中创建 ITSMC 资源的资源组：

   ![显示 "Log Analytics 工作区" 部分的屏幕截图。](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > 作为 Microsoft Operations Management Suite (OMS) 到 Azure Monitor 的持续转换的一部分，OMS 工作区现在称为 *Log Analytics 工作* 区。

5. 选择“确定”。

部署 ITSMC 资源后，窗口右上角会出现一个通知。

## <a name="create-an-itsm-connection"></a>创建 ITSM 连接

安装 ITSMC 后，必须准备 ITSM 工具以允许来自 ITSMC 的连接。 根据你要连接到的 ITSM 产品，选择以下链接之一来获取说明：

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

准备好 ITSM 工具后，请完成以下步骤以创建连接：

1. 在 " **所有资源**" 中，查找 **ServiceDesk (*你的工作区名称*)**：

   ![显示 Azure 门户中最近的资源的屏幕截图。](media/itsmc-definition/create-new-connection-from-resource.png)

1. 在左侧窗格的 " **工作区数据源** " 下，选择 " **ITSM 连接**：

   ![显示 ITSM 连接菜单项的屏幕截图。](media/itsmc-overview/add-new-itsm-connection.png)

1. 选择 " **添加连接**"。

1. 根据所使用的 ITSM 产品指定连接设置：

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > 默认情况下，ITSMC 每24小时刷新一次连接的配置数据。 若要立即刷新连接的数据以反映所做的任何编辑或模板更新，请在连接的窗格上选择 " **同步** " 按钮：
   >
   > ![显示连接窗格上的 "同步" 按钮的屏幕截图。](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>根据 Azure 警报创建 ITSM 工作项

创建 ITSM 连接后，可以使用 ITMC 基于 Azure 警报在 ITSM 工具中创建工作项。 若要创建工作项，请使用操作组中的 ITSM 操作。

操作组为你的 Azure 警报提供模块化且可重用的方法来触发操作。 可以在 Azure 门户中使用操作组和指标警报、活动日志警报和 Log Analytics 警报。

> [!NOTE]
> 创建 ITSM 连接后，需要等待30分钟，同步过程才能完成。

## <a name="define-a-template"></a>定义模板

某些工作项类型可以使用您在 ITSM 工具中定义的模板。 通过使用模板，可以定义将根据操作组的固定值自动填充的字段。 您可以定义要用作操作组定义的一部分的模板。

创建操作组：

1. 在 Azure 门户中，选择 "  **警报**"。
2. 在屏幕顶部的菜单中，选择 " **管理操作**"：

    ![显示 "管理操作" 菜单项的屏幕截图。](media/itsmc-overview/action-groups-selection-big.png)

   此时将显示 " **创建操作组** " 窗口。

3. 选择要在其中创建操作组的 **订阅** 和 **资源组** 。 为操作组提供 " **操作组名称** " 和 " **显示名称** " 中的值。 然后选择 " **下一步：通知**"。

    ![显示 "创建操作组" 窗口的屏幕截图。](media/itsmc-overview/action-groups-details.png)

4. 在 " **通知** " 选项卡上，选择 " **下一步：操作**"。
5. 在 "**操作**" 选项卡上的 "**操作类型**" 列表中，选择 " **ITSM** "。 对于 " **名称**"，请提供操作的名称。 然后选择表示 **编辑详细信息** 的笔按钮。

    ![显示用于创建操作组的选项的屏幕截图。](media/itsmc-definition/action-group-pen.png)

6. 在 " **订阅** " 列表中，选择包含 Log Analytics 工作区的订阅。 在 " **连接** " 列表中，选择 ITSM 连接器名称。 后跟工作区名称。 例如， *MyITSMConnector (MyWorkspace)*。

7. 选择 **工作项** 类型。

8. 如果要使用固定值填写现成字段，请选择 " **使用自定义模板**"。 否则，请在 "**模板**" 列表中选择现有 [模板](#define-a-template)，然后在模板字段中输入固定值。

9. 在用于创建 ITSM 操作组的接口的最后一个部分中，可以定义为每个警报创建的工作项的数目。

   > [!NOTE]
   > 本部分仅适用于日志搜索警报。 对于所有其他警报类型，将为每个警报创建一个工作项。

   * 如果在 "**工作项**" 下拉列表中选择了 "**事件**" 或 "**警报**"，则可以选择为每个配置项创建单独的工作项。
    
     ![显示选定为工作项的事件的 I T S M 票证区域的屏幕截图。](media/itsmc-overview/itsm-action-configuration.png)
    
     * 如果选中 " **为每个配置项目创建单独的工作项** " 复选框，则每个警报中的每个配置项都将创建新的工作项。 由于相同的受影响的配置项目会出现多个警报，因此每个配置项目都有多个工作项。

       例如，具有三个配置项目的警报将创建三个工作项。 具有一个配置项目的警报将创建一个工作项。
        
     * 如果清除了 " **为每个配置项目创建单独的工作项** " 复选框，则 ITSMC 将为每个警报规则创建一个工作项，并将所有受影响的配置项追加到该工作项。 如果上一个工作项已关闭，则将创建一个新的工作项。

       >[!NOTE]
       > 在这种情况下，某些触发的警报不会在 ITSM 工具中生成新的工作项。

       例如，具有三个配置项目的警报将创建一个工作项。 如果与上一示例相同的警报规则的警报有一个配置项目，则该配置项目将附加到所创建的工作项中受影响的配置项目列表中。 具有一个配置项目的不同警报规则的警报会创建一个工作项。

   * 如果在 "**工作项**" 下拉列表中选择了 "**事件**"，则可以选择为每个日志项或每个配置项创建单独的工作项。
    
     ![显示选定为工作项的事件的 I T S M 票证区域的屏幕截图。](media/itsmc-overview/itsm-action-configuration-event.png)

     * 如果 **为每个日志条目选择 "创建单个工作项" (则不会填充 "配置项目" 字段。可能会导致大量的工作项。 )**，将为日志搜索警报查询的搜索结果中的每一行创建一个工作项。 工作项的负载中的 "说明" 属性将包含搜索结果中的行。
      
     * 如果 **为每个配置项目选择 "创建单独的工作项**"，则每个警报中的每个配置项都将创建一个新的工作项。 每个配置项目在 ITSM 系统中可以有多个工作项。 此选项等同于选择 " **事件** " 作为工作项类型后出现的复选框。

10. 选择“确定”。

当你创建或编辑 Azure 警报规则时，使用具有 ITSM 操作的操作组。 当警报触发时，会在 ITSM 工具中创建或更新工作项。

> [!NOTE]
> 有关 ITSM 操作定价的信息，请参阅操作组的 [定价页](https://azure.microsoft.com/pricing/details/monitor/) 。
>
> 当使用 ITSM 操作发送警报规则时，警报规则定义中的简短说明字段限制为40个字符。

## <a name="next-steps"></a>后续步骤

* [解决 ITSMC 中的问题](./itsmc-resync-servicenow.md)
