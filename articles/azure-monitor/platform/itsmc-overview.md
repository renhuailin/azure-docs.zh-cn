---
title: Log Analytics 中的 IT 服务管理连接器
description: 本文概述了 IT 服务管理连接器 (ITSMC) ，以及如何使用它来监视和管理 Log Analytics 中的 ITSM 工作项并快速解决问题。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 917eb52a7255527b55aacd488f33125ad853e894
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932262"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>使用 IT 服务管理连接器将 Azure 连接到 ITSM 工具

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT 服务管理连接器 (ITSMC) 允许将 Azure 连接到受支持的 IT 服务管理 (ITSM) 产品或服务。

Azure 服务（例如 Azure Log Analytics 和 Azure Monitor 提供用于检测、分析和解决 Azure 和非 Azure 资源的问题的工具。 但与问题相关的工作项通常位于 ITSM 的产品或服务中。 ITSMC 在 Azure 与 ITSM 工具之间提供双向连接，有助于更快地解决问题。

ITSMC 支持使用以下 ITSM 工具建立的连接：

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> 我们建议 Cherwell 和 Provance 客户使用 [Webhook 操作](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) 来 Cherwell 和 Provance 终结点作为集成的另一个解决方案。

通过 ITSMC，你可以：

-  基于 Azure 警报在 ITSM 工具中创建工作项， (指标警报、活动日志警报和 Log Analytics 警报) 。
-  可以选择将 ITSM 工具中的事件和更改请求数据同步到 Azure Log Analytics 工作区。

有关法律条款和隐私策略的信息，请参阅 [Microsoft 隐私声明](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)。

可以通过完成以下步骤开始使用 ITSMC：

1.  [添加 ITSMC。](#add-it-service-management-connector)
2.  [创建 ITSM 连接。](#create-an-itsm-connection)
3.  [使用连接。](#use-itsmc)


##  <a name="add-it-service-management-connector"></a>添加 IT 服务管理连接器

你需要添加 ITSMC，然后才能创建连接。

1. 在 Azure 门户中，选择 " **创建资源**"：

   ![显示 "创建资源" 菜单项的屏幕截图。](media/itsmc-overview/azure-add-new-resource.png)

2. 在 Azure Marketplace 中搜索 **IT 服务管理连接器** 。 选择 " **创建**"：

   ![屏幕截图，显示 Azure Marketplace 中的 "创建" 按钮。](media/itsmc-overview/add-itsmc-solution.png)

3. 在 " **OMS 工作区** " 部分中，选择要在其中安装 ITSMC 的 Azure Log Analytics 工作区。
   >[!NOTE]
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
通过使用模板，可以定义将根据定义为操作组一部分的固定值自动填充的字段。 可在 ITSM 工具中定义模板。 您可以定义要将哪个模板用作操作组定义的一部分。
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>根据 Azure 警报创建 ITSM 工作项

创建 ITSM 连接后，可以基于 Azure 警报在 ITSM 工具中创建工作项。 若要创建工作项，请使用操作组中的 ITSM 操作。

操作组为你的 Azure 警报提供模块化且可重用的方法来触发操作。 可以在 Azure 门户中将操作组与指标警报、活动日志警报和 Azure Log Analytics 警报一起使用。

> [!NOTE]
> 创建 ITSM 连接后，需要等待30分钟，同步过程才能完成。
> 

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

   ![显示 ITSM 票证窗口的屏幕截图。](media/itsmc-overview/itsm-action-configuration.png)
   
   * 在 "工作项" 下拉列表中选择 "事件" 时：如果为单选按钮选择中的 **每个日志项选择 "创建单独的工作项** "，则每个警报都将创建一个新的工作项。 如果为单选按钮选择中的 **每个配置项目选择 "创建单独的工作项** "，则每个配置项都将有自己的工作项。
   ![显示 ITSM 票证窗口的屏幕截图。](media/itsmc-overview/itsm-action-configuration-event.png)

10. 选择“确定”  。

当你创建或编辑 Azure 警报规则时，使用具有 ITSM 操作的操作组。 当警报触发时，会在 ITSM 工具中创建或更新工作项。

> [!NOTE]
>
>- 有关 ITSM 操作定价的信息，请参阅操作组的 [定价页](https://azure.microsoft.com/pricing/details/monitor/) 。
>
>
>- 当使用 ITSM 操作发送警报规则时，警报规则定义中的简短说明字段限制为40个字符。


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>可视化和分析事件与更改请求数据

根据设置连接时的配置，ITSMC 可以同步最多120天的事件和更改请求数据。 本文的 [下一部分](#additional-information) 提供了此数据的日志记录架构。

可以使用 ITSMC 仪表板可视化事件和更改请求数据：

![显示 ITSMC 仪表板的屏幕截图。](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

该仪表板还提供有关连接器状态的信息，可将其用作分析连接问题的起点。

你还可以在服务映射中将与受影响计算机同步的事件可视化。

服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 它允许你查看服务器，就像你想象的那样：作为提供重要服务的互连系统。 服务映射显示在任何 TCP 连接的体系结构中服务器、进程和端口之间的连接。 除了安装代理以外，无需进行任何配置。 有关详细信息，请参阅 [使用服务映射](../insights/service-map.md)。

如果你使用服务映射，则可以查看在 ITSM 解决方案中创建的服务台项，如下所示：

![显示 Log Analytics 屏幕的屏幕截图。](media/itsmc-overview/itsmc-overview-integrated-solutions.png)


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
| PlannedStartDate_t  |     计划开始日期 |
| PlannedEndDate_t  |   计划结束日期 |
| WorkStartDate_t  | 实际开始日期 |
| WorkEndDate_t | 实际结束日期|
| Description_s | 说明 |
| Computer  | 配置项 |


## <a name="troubleshoot-itsm-connections"></a>排查 ITSM 连接问题
- 如果连接源的 UI 连接失败，并且在 **保存连接消息时出现错误** ，请执行以下步骤：
   - 对于 ServiceNow、Cherwell 和 Provance 连接：  
     - 确保为每个连接正确输入了用户名、密码、客户端 ID 和客户端密码。  
     - 确保在相应的 ITSM 产品中具有足够的权限来建立连接。  
   - 对于 Service Manager 连接：  
     - 确保已成功部署 web 应用并创建了混合连接。 若要验证是否已成功与本地 Service Manager 计算机建立连接，请按照建立 [混合连接](./itsmc-connections.md#configure-the-hybrid-connection)的文档中所述，参阅 WEB 应用 URL。  

- 如果 ServiceNow 中的数据未同步到 Log Analytics，请确保 ServiceNow 实例未处于睡眠状态。 当 ServiceNow 开发实例长时间处于空闲状态时，有时会进入睡眠状态。 如果不是这样，请报告问题。
- 如果 Log Analytics 警报触发但未在 ITSM 产品中创建工作项，如果不创建或链接到工作项的配置项目或其他信息，请参阅以下资源：
   -  ITSMC：此解决方案显示连接、工作项和计算机等的摘要。 选择具有 **连接器状态** 标签的磁贴。 这样做会使您使用相关查询来 **记录搜索** 。 `LogType_S`有关详细信息，请查看的日志记录 `ERROR` 。
   - **日志搜索** 页：使用查询直接查看错误和相关信息 `*ServiceDeskLog_CL*` 。

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Web 应用部署 Service Manager 疑难解答
-   如果你在使用 web 应用部署时遇到问题，请确保你有权在订阅中创建/部署资源。
-   如果在运行 [脚本](itsmc-service-manager-script.md)时，**未将对象引用设置为对象** 错误的实例，请确保在 "**用户配置**" 部分中输入了有效值。
-   如果无法创建服务总线中继命名空间，请确保在订阅中注册所需的资源提供程序。 如果未注册，请从 Azure 门户中手动创建 service bus 中继命名空间。 在 Azure 门户中 [创建混合连接](./itsmc-connections.md#configure-the-hybrid-connection) 时，还可以创建它。


## <a name="contact-us"></a>联系我们

如果你有关于 IT 服务管理连接器的查询或反馈，请联系我们 [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) 。

## <a name="next-steps"></a>后续步骤
[将 ITSM 产品/服务添加到 IT 服务管理连接器](./itsmc-connections.md)
