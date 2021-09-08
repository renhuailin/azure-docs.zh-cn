---
title: 排查 ITSMC 中的问题
description: 了解如何解决 IT 服务管理连接器中的常见问题。
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: d6e41cd80e27fe673d04592c61e590c09645619e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430353"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>排查 IT 服务管理连接器中的问题

本文讨论 IT 服务管理连接器 (ITSMC) 中的常见问题，以及如何排查这些问题。

Azure Monitor 在监视数据中发现重要条件时，会主动发出警报通知。 这些警报可帮助你在系统用户注意到问题之前识别并解决问题。

你可以选择警报接收的方式。 你可以选择邮件、短信或 Webhook，甚至也可以自动执行解决方案。 

或者，可以通过 ITSMC 通知。 通过 ITSMC，可以将警报发送到外部票证系统（例如 ServiceNow）。

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>使用仪表板来分析事件和更改请求数据

根据配置，在设置连接时，ITSM 可以同步最长 120 天的事件和更改请求数据。 若要获取数据的日志记录架构，请参阅[从 ITSM 产品同步的数据](./itsmc-synced-data.md)一文。

你可以通过使用 ITSMC 仪表板可视化事件和更改请求数据：

![显示 ITSMC 仪表板的屏幕截图。](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

该仪表板还提供有关连接器状态的信息。 您可以使用这些信息作为切入点来分析连接问题。 有关详细信息，请参阅[使用仪表板调查错误](./itsmc-dashboard.md)。

## <a name="use-service-map-to-visualize-incidents"></a>使用服务映射可视化事件

你还可以在服务映射中，将针对受影响计算机同步的事件可视化。

服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 借助它，你可以按照自己的想法，将服务器作为提供重要服务的互连系统。 

服务映射显示任何 TCP 连接的体系结构中服务器、进程和端口之间的连接。 只需安装代理，无需任何其他配置。 有关详细信息，请参阅[使用服务映射](../vm/service-map.md)。

如果使用了服务映射，则可以查看 IT 服务管理 (ITSM) 解决方案中创建的服务台工作项，如以下示例中所示：

![显示 Log Analytics 屏幕的屏幕截图。](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>解决问题

以下各部分介绍了常见症状、可能原因和解决方法。 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>与 ITSM 系统的连接失败，并收到“保存连接时出错”消息

**原因**：可以是以下原因之一：

* 凭据不正确。
* 特权不足。
* 对于 Service Manager 连接：未正确部署 Web 应用。

**解决方法**：

* 对于 ServiceNow、Cherwell 和 Provance 连接：
  * 请确保正确输入每个连接的用户名、密码、客户端 ID 和客户端密码。  
  * 对于 ServiceNow，请确保在相应 ITSM 产品中具有[足够特权](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)。

* 对于 Service Manager 连接：  
  * 请确保成功部署 Web 应用并创建混合连接。 若要验证是否已成功建立与本地 Service Manager 计算机的连接，请转到[建立混合连接文档](./itsmc-connections-scsm.md#configure-the-hybrid-connection)中介绍的 Web 应用 URL。  

### <a name="duplicate-work-items-are-created"></a>创建了重复工作项

**原因**：可以是以下两个原因之一：

* 为警报定义了多个 ITSM 操作。
* 已解决警报。

**解决方法**：提供两个解决方案：

* 请确保每个警报只有一个 ITSM 操作组。
* ITSMC 不支持警报解决后匹配工作项的状态更新。 创建新的已解决工作项。

### <a name="work-items-are-not-created"></a>未创建工作项

**原因**：可能有多种原因导致此症状：

* 在 ServiceNow 端修改了代码。
* 权限配置错误。
* ServiceNow 速率限制太大或过低。
* 刷新令牌已过期。
* 已删除 ITSMC。

**解决方法**：检查 [仪表板](itsmc-dashboard.md)并查看连接器状态部分中的错误。 接下来请查看[常见错误及其解决方法](itsmc-dashboard-errors.md)。

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>无法为操作组创建 ITSM 操作

**原因**：新创建的 ITSM 实例尚未完成初始同步。

**解决方法**：请查看 [常见错误及其解决方法](itsmc-dashboard-errors.md)。

### <a name="sync-connection"></a>同步连接 

**原因**：可能有多种原因导致此症状：

* 模板未显示为“操作定义”下拉列表的一部分，但会显示一条错误消息：“无法检索模板配置。有关详细信息，请参阅连接器日志。”
* 值未作为操作定义的一部分显示在默认字段的下拉列表中，但会显示一条错误消息：“找不到以下字段的值: \<field names\>”。
* ServiceNow 中未创建事件。

**解决方法**： 
* [同步连接器](itsmc-resync-servicenow.md)。
* 检查[仪表板](itsmc-dashboard.md)，查看连接器状态部分的错误。 接下来请查看[常见错误及其解决方法](itsmc-dashboard-errors.md)
