---
title: Log Analytics 中的 IT 服务管理连接器
description: 本文提供 IT 服务管理连接器 (ITSMC) 的概述，以及有关使用它来监视和管理 Log Analytics 中的 ITSM 工作项并快速解决问题的信息。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 3097023c4b9d19379f44529615a10c100e794700
ms.sourcegitcommit: 1ee13b62c094a550961498b7a52d0d9f0ae6d9c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109838658"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-solution"></a>使用 IT 服务管理解决方案将 Azure 连接到 ITSM 工具

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

本文介绍如何在 Log Analytics 中配置 IT 服务管理连接器 (ITSMC)，以集中管理 IT 服务管理 (ITSM) 工作项。

## <a name="add-it-service-management-connector"></a>添加 IT 服务管理连接器

在创建连接之前，需要先安装 ITSMC。

1. 在 Azure 门户中选择“创建资源”：

   ![显示用于创建资源的菜单项的屏幕截图。](media/itsmc-overview/azure-add-new-resource.png)

2. 在 Azure 市场中搜索“IT 服务管理连接器”。 然后选择“创建”：

   ![显示 Azure 市场中的“创建”按钮的屏幕截图。](media/itsmc-overview/add-itsmc-solution.png)

3. 在“LA 工作区”部分，选择要在其中安装 ITSMC 的 Log Analytics 工作区。
   > [!NOTE]
   > 只能在位于以下区域的 Log Analytics 工作区中安装 ITSMC：美国东部、美国西部 2、美国中南部、美国中西部、US Gov 亚利桑那州、US Gov 弗吉尼亚州、加拿大中部、西欧、英国南部、东南亚、日本东部、印度中部和澳大利亚东南部。

4. 在“Log Analytics 工作区”部分，选择要在其中创建 ITSMC 资源的资源组：

   ![显示“Log Analytics 工作区”部分的屏幕截图。](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > 随着我们持续从 Microsoft Operations Management Suite (OMS) 过渡到 Azure Monitor，OMS 工作区现在称为“Log Analytics 工作区”。

5. 选择“确定”。

部署 ITSMC 资源后，窗口右上角会显示一条通知。

## <a name="create-an-itsm-connection"></a>创建 ITSM 连接

安装 ITSMC 后，必须准备好 ITSM 工具，以允许从 ITSMC 建立连接。 根据所要连接到的 ITSM 产品，选择以下链接之一获取相关说明：

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

准备好 ITSM 工具之后，完成以下步骤以创建连接：

1. 在“所有资源”中，找到“ServiceDesk(你的工作区名称)”： 

   ![显示 Azure 门户中最近创建的资源的屏幕截图。](media/itsmc-definition/create-new-connection-from-resource.png)

1. 在左窗格中的“工作区数据源”下，选择“ITSM 连接”： 

   ![显示“ITSM 连接”菜单项的屏幕截图。](media/itsmc-overview/add-new-itsm-connection.png)

1. 选择“添加连接”。

1. 根据所用的 ITSM 产品指定连接设置：

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > 默认情况下，ITSMC 每隔 24 小时刷新连接配置数据一次。 若要即时刷新连接数据以反映所做的任何编辑或模板更新，请在连接窗格中选择“同步”按钮：
   >
   > ![显示连接窗格中的“同步”按钮的屏幕截图。](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>根据 Azure 警报创建 ITSM 工作项

创建 ITSM 连接后，可以使用 ITMC 在 ITSM 工具中基于 Azure 警报创建工作项。 若要创建工作项，请使用操作组中的 ITSM 操作。

操作组提供模块化且可重用的方式来针对 Azure 警报触发操作。 可对 Azure 门户中的指标警报、活动日志警报和 Log Analytics 警报使用操作组。

> [!NOTE]
> 创建 ITSM 连接后，需要等待 30 分钟让同步过程完成。

### <a name="define-a-template"></a>定义模板

某些工作项类型可以使用 ITSM 工具中定义的模板。 使用模板可以定义根据操作组的固定值自动填充的字段。 可以定义要将哪个模板用作操作组定义的一部分。 可在 ServiceNow 文档中查找有关如何创建模板的信息 -（此处）[https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/form-administration/task/t_CreateATemplateUsingTheTmplForm.html ]。

若要创建操作组：

1. 在 Azure 门户中选择“警报”。
2. 在屏幕顶部的菜单中选择“管理操作”：

    ![显示“管理操作”菜单项的屏幕截图。](media/itsmc-overview/action-groups-selection-big.png)

   此时会显示“创建操作组”窗口。

3. 选择要在其中创建操作组的“订阅”和“资源组”。  在操作组的“操作组名称”和“显示名称”中提供值。  然后选择“下一步: 通知”。

    ![显示“创建操作组”窗口的屏幕截图。](media/itsmc-overview/action-groups-details.png)

4. 在“通知”选项卡上，选择“下一步: 操作”。 
5. 在“操作”选项卡上的“操作类型”列表中，选择“ITSM”。   对于“名称”，请提供操作的名称。 然后选择表示“编辑详细信息”的铅笔按钮。

    ![显示用于创建操作组的选项的屏幕截图。](media/itsmc-definition/action-group-pen.png)

6. 在“订阅”列表中，选择包含你的 Log Analytics 工作区的订阅。 在“连接”列表中，选择你的 ITSM 连接器名称。 该名称后接工作区名称。 例如 *MyITSMConnector(MyWorkspace)* 。

7. 选择 **工作项** 类型

8. 在用于创建 ITSM 操作组的界面的最后一个部分，可以定义要针对每个警报创建多少个工作项。

   > [!NOTE]
   > 此部分仅与日志搜索警报相关。 对于所有其他警报类型，请针对每个警报创建一个工作项。

   * 如果在“工作项”下拉列表中选择了“事故”或“警报”，则可以选择为每个配置项创建单独的工作项。  
    
     ![显示“ITSM 票证”区域的屏幕截图，其中已将“事故”选作工作项。](media/itsmc-overview/itsm-action-configuration.png)
    
     * 如果选中“为每个配置项创建单独的工作项”复选框，则每条警报中的每个配置项都将创建新的工作项。 由于会针对相同的受影响配置项生成多条警报，因此每个配置项将有多个工作项。

       例如，具有三个配置项的警报将创建三个工作项。 具有一个配置项的警报将创建一个工作项。
        
     * 如果清除“为每个配置项创建单独的工作项”复选框，则 ITSMC 将为每个警报规则创建一个工作项，并将所有受影响的配置项追加到该工作项。 如果关闭了前一个工作项，将创建一个新工作项。

       >[!NOTE]
       > 在这种情况下，某些已触发的警报不会在 ITSM 工具中生成新的工作项。

       例如，具有三个配置项的警报将创建一个工作项。 如果针对上一示例中所述的同一警报规则的警报具有一个配置项，则该配置项将附加到所创建的工作项中的受影响配置项列表。 针对不同警报规则的具有一个配置项的警报将创建一个工作项。

   * 如果在“工作项”下拉列表中选择了“事件”，则可以选择为每个日志条目或者为每个配置项创建单独的工作项。 
    
     ![显示“ITSM 票证”区域的屏幕截图，其中已将“事件”选作工作项。](media/itsmc-overview/itsm-action-configuration-event.png)

     * 如果选择“为每个日志条目创建单独的工作项(不填充‘配置项’字段。可能生成大量工作项。)”，将为日志搜索警报查询的搜索结果中的每一行创建一个工作项。 工作项有效负载中的说明属性将包含搜索结果中的行。
      
     * 如果选择“为每个配置项创建单独的工作项”，则每条警报中的每个配置项都将创建新的工作项。 每个配置项在 ITSM 系统中可以有多个工作项。 此选项等同于选中在将“事故”选作工作项类型后出现的复选框。
9. 在进行操作定义时，你可以将会包含常数值的预定义字段定义为有效负载的一部分。 可以根据工作项类型将下述 3 个选项用作有效负载的一部分：
    * 无：使用将要发送到 ServiceNow 的常规有效负载，无需任何额外的预定义字段和值。
    * 使用默认字段：使用一组将会作为有效负载的一部分自动发送到 ServiceNow 的字段和值。 这些字段不灵活，并且值是在 ServiceNow 列表中定义的。
    * 从 Servicenow 使用保存的模板：使用一组预定义的字段和值，这些字段和值已在 ServiceNow 中作为模板定义的一部分进行定义。 如果已在 ServiceNow 中定义了模板，则可从“模板”列表中使用它，否则请在 ServiceNow 中定义它。有关详细信息，请参阅[此文](#define-a-template)。

10. 选择“确定”。

创建或编辑 Azure 警报规则时，请使用包含 ITSM 操作的操作组。 警报触发时，将在 ITSM 工具中创建或更新工作项。

> [!NOTE]
> 有关 ITSM 操作定价的信息，请参阅操作组的[定价页](https://azure.microsoft.com/pricing/details/monitor/)。
>
> 使用 ITSM 操作发送警报规则定义中的简短说明字段时，该字段限制为 40 个字符。

## <a name="next-steps"></a>后续步骤

* [排查 ITSMC 中的问题](./itsmc-resync-servicenow.md)
