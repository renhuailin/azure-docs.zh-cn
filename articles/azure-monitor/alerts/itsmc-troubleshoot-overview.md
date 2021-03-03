---
title: 解决 ITSMC 中的问题
description: 了解如何解决 IT 服务管理连接器中的常见问题。
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a01005231b5d775f79555ec10dedeb3f30b3426a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737199"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>解决 IT 服务管理连接器中的问题

本文讨论 IT 服务管理连接器 (ITSMC) 的常见问题，以及如何解决这些问题。

Azure Monitor 在监视数据中发现重要条件时，主动通知你警报。 这些警报可帮助你在系统用户通知他们之前识别并解决问题。

您可以选择希望接收警报的方式。 你可以选择邮件、短信或 webhook，甚至自动执行解决方案。 

一种替代方法是通过 ITSMC 通知。 ITSMC 提供了将警报发送到外部票证系统（如 ServiceNow）的选项。

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>使用仪表板来分析事件和更改请求数据

根据设置连接时的配置，ITSMC 可以同步最多120天的事件和更改请求数据。 若要获取此数据的日志记录架构，请参阅 [从 ITSM 产品同步的数据](./itsmc-synced-data.md) 一文。

可以使用 ITSMC 仪表板可视化事件和更改请求数据：

![显示 ITSMC 仪表板的屏幕截图。](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

该仪表板还提供有关连接器状态的信息。 您可以使用这些信息来分析连接问题。 有关详细信息，请参阅 [使用仪表板进行错误调查](./itsmc-dashboard.md)。

## <a name="use-service-map-to-visualize-incidents"></a>使用服务映射直观显示事件

你还可以在服务映射中将与受影响计算机同步的事件可视化。

服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 它允许你查看服务器，就像你想象的那样：作为提供重要服务的互连系统。 

服务映射显示在任何 TCP 连接的体系结构中服务器、进程和端口之间的连接。 除了安装代理以外，无需进行任何配置。 有关详细信息，请参阅 [使用服务映射](../vm/service-map.md)。

如果使用服务映射，则可以查看在 IT Service Management (ITSM) 解决方案中创建的服务台项，如以下示例中所示：

![显示 Log Analytics 屏幕的屏幕截图。](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>解决问题

以下各节介绍常见症状、可能的原因和解决方法。 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>与 ITSM 系统的连接失败，并且收到 "保存连接错误" 消息

**原因**：原因可以是以下选项之一：

* 凭据不正确。
* 权限不足。
* 对于 Service Manager 连接： web 应用未正确部署。

**解决方法**：

* 对于 ServiceNow、Cherwell 和 Provance 连接：
  * 确保正确输入每个连接的用户名、密码、客户端 ID 和客户端密码。  
  * 对于 ServiceNow，请确保在相应的 ITSM 产品中具有 [足够的权限](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) 。

* 对于 Service Manager 连接：  
  * 确保已成功部署 web 应用并创建了混合连接。 若要验证是否已成功与本地 Service Manager 计算机建立连接，请按照 [建立混合连接的文档](./itsmc-connections-scsm.md#configure-the-hybrid-connection)中所述，参阅 WEB 应用 URL。  

### <a name="duplicate-work-items-are-created"></a>创建了重复的工作项

**原因**：原因可以是以下两个选项之一：

* 为警报定义了多个 ITSM 操作。
* 已解决警报。

**解决方法**：可以有两个解决方案：

* 请确保每个警报都有一个 ITSM 操作组。
* 当警报解决时，ITSMC 不支持匹配工作项的状态更新。 创建新的已解决工作项。

### <a name="work-items-are-not-created"></a>未创建工作项

**原因**：可能有多种原因导致此症状：

* 在 ServiceNow 端修改了代码。
* 权限配置错误。
* ServiceNow 速率限制太大或过低。
* 刷新令牌已过期。
* ITSMC 已删除。

**解决方法**：检查 [仪表板](itsmc-dashboard.md) ，查看 "连接器状态" 部分中的错误。 然后查看 [常见错误及其解决方法](itsmc-dashboard-errors.md)。

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>不能为操作组创建 ITSM 操作

**原因**：新创建的 ITSMC 实例尚未完成初始同步。

**解决方法**：查看 [常见错误及其解决方法](itsmc-dashboard-errors.md)。

### <a name="sync-connection"></a>同步连接 

**原因**：可能有多种原因导致此症状：

* 模板不会显示为操作定义的一部分。
* 不会在 ServiceNow 中创建 Incedents/事件。

**解决方法**： [同步连接器](itsmc-resync-servicenow.md)。
