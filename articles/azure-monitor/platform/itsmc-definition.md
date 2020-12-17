---
title: Log Analytics 中的 IT 服务管理连接器
description: 本文概述了 IT 服务管理连接器 (ITSMC) ，以及如何使用它来监视和管理 Log Analytics 中的 ITSM 工作项并快速解决问题。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 568243c6fecf26510f6e9988907d1ccad103cdc2
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618741"
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

3. 在 " **OMS 工作区** " 部分中，选择要在其中安装 ITSMC 的 Azure Log Analytics 工作区。
   >[!NOTE]
   >
   > * 作为 Microsoft Operations Management Suite (OMS) 到 Azure Monitor 的持续转换的一部分，OMS 工作区现在称为 *Log Analytics 工作区*。
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

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

准备好 ITSM 工具后，请完成以下步骤以创建连接：

1. 在 " **所有资源**" 中，查找 **ServiceDesk (*你的工作区名称*)**：

   ![显示 Azure 门户中最近的资源的屏幕截图。](media/itsmc-overview/itsm-connections.png)

1. 在左侧窗格中的 " **工作区数据源** " 下，选择 " **ITSM 连接**：

   ![显示 ITSM 连接菜单项的屏幕截图。](media/itsmc-overview/add-new-itsm-connection.png)
   此页显示连接列表。
1. 选择 " **添加连接**"。

4. 按照为 [ITSM 产品/服务配置 ITSMC 连接](./itsmc-connections.md)中所述指定连接设置。

   > [!NOTE]
   >
   > 默认情况下，ITSMC 每24小时刷新一次连接的配置数据。 若要立即刷新连接的数据以反映所做的任何编辑或模板更新，请在连接的边栏选项卡上选择 " **同步** " 按钮：
   >
   > ![显示连接边栏选项卡上的 "同步" 按钮的屏幕截图。](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>使用 ITSMC

   可以使用 ITSMC 从 Azure 警报创建工作项，Log Analytics 警报和 Log Analytics 日志记录。

## <a name="template-definitions"></a>模板定义

   可以使用 ITSM 工具定义的模板的工作项类型。
通过使用模板，可以定义将根据定义为操作组一部分的固定值自动填充的字段。 可在 ITSM 工具中定义模板。
您可以定义要将哪个模板用作操作组定义的一部分。

## <a name="create-itsm-work-items-from-azure-alerts"></a>根据 Azure 警报创建 ITSM 工作项

创建 ITSM 连接后，可以基于 Azure 警报在 ITSM 工具中创建工作项。 若要创建工作项，请使用操作组中的 ITSM 操作。

操作组为你的 Azure 警报提供模块化且可重用的方法来触发操作。 可以在 Azure 门户中将操作组与指标警报、活动日志警报和 Azure Log Analytics 警报一起使用。

> [!NOTE]
> 创建 ITSM 连接后，需要等待30分钟，同步过程才能完成。

使用以下过程来创建工作项：

1. 在 Azure 门户中，选择 "  **警报**"。
2. 在屏幕顶部的菜单中，选择 " **管理操作**"：

    ![显示 "管理操作" 菜单项的屏幕截图。](media/itsmc-overview/action-groups-selection-big.png)

   此时将显示 " **创建操作组** " 窗口。

3. 选择要在其中创建操作组的 **订阅** 和 **资源组** 。 为操作组提供 " **操作组名称** " 和 " **显示名称** "。 选择 " **下一步：通知**"。

    ![显示 "创建操作组" 窗口的屏幕截图。](media/itsmc-overview/action-groups-details.png)

4. 在通知列表中，选择 " **下一步：操作**"。
5. 在 "操作" 列表中，选择 "**操作类型**" 列表中的 **ITSM** 。 提供操作的 **名称** 。 选择表示 **编辑详细信息** 的笔按钮。
6. 在 " **订阅** " 列表中，选择 Log Analytics 工作区所在的订阅。 在 " **连接** " 列表中，选择 ITSM 连接器名称。 后跟工作区名称。 例如，MyITSMConnector (MyWorkspace) 。

7. 选择 **工作项** 类型。

8. 如果要使用固定值填写现成字段，请选择 " **使用自定义模板**"。 否则，请在 "**模板**" 列表中选择现有 [模板](#template-definitions)，然后在模板字段中输入固定值。

9. 如果 **为每个配置项目选择 "创建单独的工作项**"，则每个配置项都将有自己的工作项。 每个配置项目都有一个工作项。 它将根据将要创建的警报进行更新。

    * 在工作项下拉列表中选择 "事件" 或 "警报"：如果清除了 " **为每个配置项目创建单独的工作项** " 复选框，则每个警报都将创建一个新的工作项。 每个配置项目可能有多个警报。

       ![显示 ITSM 事件窗口的屏幕截图。](media/itsmc-overview/itsm-action-configuration.png)

    * 在 "工作项" 下拉列表中选择 "事件" 时：如果为单选按钮选择中的 **每个日志项选择 "创建单独的工作项** "，则每个警报都将创建一个新的工作项。 如果为单选按钮选择中的 **每个配置项目选择 "创建单独的工作项** "，则每个配置项都将有自己的工作项。
   ![显示 ITSM 事件窗口的屏幕截图。](media/itsmc-overview/itsm-action-configuration-event.png)

10. 选择“确定”  。

当你创建或编辑 Azure 警报规则时，使用具有 ITSM 操作的操作组。 当警报触发时，会在 ITSM 工具中创建或更新工作项。

> [!NOTE]
>
>- 有关 ITSM 操作定价的信息，请参阅操作组的 [定价页](https://azure.microsoft.com/pricing/details/monitor/) 。
>
>
>- 当使用 ITSM 操作发送警报规则时，警报规则定义中的简短说明字段限制为40个字符。

## <a name="additional-information"></a>其他信息

### <a name="data-synced-from-your-itsm-product"></a>从 ITSM 产品同步的数据

事件和更改请求根据连接的配置从 ITSM 产品同步到 Log Analytics 工作区。

本部分介绍 ITSMC 收集的数据的一些示例。

**ServiceDesk_CL** 中的字段因导入到 Log Analytics 中的工作项类型而异。 下面是两个工作项类型的字段列表：

**工作项：** **事件**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- 服务台连接名称
- 服务台 ID
- 状态
- 紧急性
- 影响
- 优先度
- 升级
- 创建者
- 解决者
- 关闭者
- 源
- 分配给
- 类别
- 标题
- 说明
- 创建日期
- 关闭日期
- 解决日期
- 上次修改日期
- Computer

**工作项：** **更改请求**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- 服务台连接名称
- 服务台 ID
- 创建者
- 关闭者
- 源
- 分配给
- 标题
- 类型
- 类别
- 状态
- 升级
- 冲突状态
- 紧急性
- 优先度
- 风险
- 影响
- 分配给
- 创建日期
- 关闭日期
- 上次修改日期
- 请求日期
- 计划开始日期
- 计划结束日期
- 工作开始日期
- 工作结束日期
- 说明
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow 事件的输出数据

| Log Analytics 字段 | ServiceNow 字段 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | 状态 |
| Urgency_s |紧急性 |
| Impact_s |影响|
| Priority_s | 优先度 |
| CreatedBy_s | 打开者 |
| ResolvedBy_s | 解决者|
| ClosedBy_s  | 关闭者 |
| Source_s| 联系类型 |
| AssignedTo_s | 已分配到  |
| Category_s | 类别 |
| Title_s|  简短说明 |
| Description_s|  说明 |
| CreatedDate_t|  已打开 |
| ClosedDate_t| 已关闭|
| ResolvedDate_t|已解决|
| Computer  | 配置项 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 更改请求的输出数据

| Log Analytics | ServiceNow 字段 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 请求者 |
| ClosedBy_s | 关闭者 |
| AssignedTo_s | 已分配到  |
| Title_s|  简短说明 |
| Type_s|  类型 |
| Category_s|  类别 |
| CRState_s|  状态|
| Urgency_s|  紧急性 |
| Priority_s| 优先度|
| Risk_s| 风险|
| Impact_s| 影响|
| RequestedDate_t  | 请求日期 |
| ClosedDate_t | 关闭日期 |
| PlannedStartDate_t  | 计划开始日期 |
| PlannedEndDate_t  | 计划结束日期 |
| WorkStartDate_t  | 实际开始日期 |
| WorkEndDate_t | 实际结束日期|
| Description_s | 说明 |
| Computer  | 配置项 |

## <a name="next-steps"></a>后续步骤

[将 ITSM 产品/服务添加到 IT 服务管理连接器](./itsmc-connections.md)
