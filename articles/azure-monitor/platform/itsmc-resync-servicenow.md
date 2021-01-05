---
title: 如何手动修复 ServiceNow 同步问题
description: 重置到 ServiceNow 的连接，以便 Microsoft Azure 中的警报可以再次调用 ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 01e492072bd75af9f80656b71d2cc1c473d64263
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803793"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>排查 ITSM 连接器中的问题

本文讨论 ITSM 连接器中的常见问题，以及如何解决这些问题。

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 有关警报的详细信息，请参阅 Microsoft Azure 中的警报概述。
无论是通过邮件、短信、Webhook 还是自动完成解决方案，客户都可以选择他们希望如何收到有关警报的通知。 要通知的另一种方法是使用 ITSM。
ITSM 提供将警报发送到外部票证系统（如 ServiceNow）的选项。

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>可视化和分析事件与更改请求数据

根据设置连接时的配置，ITSMC 可以同步最多120天的事件和更改请求数据。 本文的 " [其他信息" 部分](./itsmc-overview.md) 提供了此数据的日志记录架构。

可以使用 ITSMC 仪表板可视化事件和更改请求数据：

![显示 ITSMC 仪表板的屏幕截图。](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

该仪表板还提供有关连接器状态的信息，可将其用作分析连接问题的起点。

你还可以在服务映射中将与受影响计算机同步的事件可视化。

服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 它允许你查看服务器，就像你想象的那样：作为提供重要服务的互连系统。 服务映射显示在任何 TCP 连接的体系结构中服务器、进程和端口之间的连接。 除了安装代理以外，无需进行任何配置。 有关详细信息，请参阅 [使用服务映射](../insights/service-map.md)。

如果你使用服务映射，则可以查看在 ITSM 解决方案中创建的服务台项，如下所示：

![显示 Log Analytics 屏幕的屏幕截图。](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="troubleshoot-itsm-connections"></a>排查 ITSM 连接问题

- 如果连接未能连接到 ITSM 系统，但 **在保存连接消息时出现错误** ，请执行以下步骤：
   - 对于 ServiceNow、Cherwell 和 Provance 连接：  
     - 确保为每个连接正确输入了用户名、密码、客户端 ID 和客户端密码。  
     - 确保在相应的 ITSM 产品中具有足够的权限来建立连接。  
   - 对于 Service Manager 连接：  
     - 确保已成功部署 web 应用并创建了混合连接。 若要验证是否已成功与本地 Service Manager 计算机建立连接，请按照建立 [混合连接](./itsmc-connections-scsm.md#configure-the-hybrid-connection)的文档中所述，参阅 WEB 应用 URL。  

- 如果 ServiceNow 中的数据未同步到 Log Analytics，请确保 ServiceNow 实例未处于睡眠状态。 当 ServiceNow 开发实例长时间处于空闲状态时，有时会进入睡眠状态。 如果不是这样，请报告问题。
- 如果 Log Analytics 警报触发但未在 ITSM 产品中创建工作项，如果不创建或链接到工作项的配置项目或其他信息，请参阅以下资源：
   -  ITSMC：此解决方案显示连接、工作项和计算机等的摘要。 选择具有 **连接器状态** 标签的磁贴。 这样做会使您使用相关查询来 **记录搜索** 。 `LogType_S`有关详细信息，请查看的日志记录 `ERROR` 。
   - **日志搜索** 页：使用查询直接查看错误和相关信息 `*ServiceDeskLog_CL*` 。

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Web 应用部署 Service Manager 疑难解答

-   如果你在使用 web 应用部署时遇到问题，请确保你有权在订阅中创建/部署资源。
-   如果在运行 [脚本](itsmc-service-manager-script.md)时，**未将对象引用设置为对象** 错误的实例，请确保在 "**用户配置**" 部分中输入了有效值。
-   如果无法创建服务总线中继命名空间，请确保在订阅中注册所需的资源提供程序。 如果未注册，请从 Azure 门户中手动创建 service bus 中继命名空间。 在 Azure 门户中 [创建混合连接](./itsmc-connections-scsm.md#configure-the-hybrid-connection) 时，还可以创建它。

### <a name="how-to-manually-fix-sync-problems"></a>如何手动修复同步问题

Azure Monitor 可以 (ITSM) 提供程序连接到第三方 IT 服务管理。 ServiceNow 是这些提供商之一。

出于安全原因，可能需要刷新用于与 ServiceNow 连接的身份验证令牌。
使用以下同步过程重新激活连接并刷新令牌：


1. 在顶部搜索横幅中搜索解决方案，然后选择相关解决方案

    ![屏幕截图，显示顶部搜索横幅和选择相关解决方案的位置。](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. 在 "解决方案" 屏幕中，依次选择 "订阅" 筛选器中的 "全选" 和 "ServiceDesk"

    ![屏幕截图，显示选择 "全选" 和 "ServiceDesk 筛选位置" 的位置。](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. 选择 ITSM 连接的解决方案。
1. 在左侧的横幅中选择 "ITSM 连接"。

    ![显示在何处选择 ITSM 连接的屏幕截图。](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. 从列表中选择每个连接器。 
    1. 单击连接器名称以对其进行配置
    1. 删除不再使用的任何连接器

    1. 根据你的合作伙伴类型根据 [这些定义](./itsmc-connections.md) 更新字段

    1. 单击 "同步"

       ![突出显示 "同步" 按钮的屏幕截图。](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. 单击 "保存"

        ![新建连接](media/itsmc-resync-servicenow/save-8bit.png)

f.    查看通知以查看进程是否已启动。
