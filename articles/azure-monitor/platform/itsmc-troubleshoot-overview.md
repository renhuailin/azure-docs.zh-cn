---
title: 排查 ITSM 连接器中的问题
description: 解决 IT 服务管理连接器中的问题
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: e43c5fb36c5395e12fd0b9c2c67b787a1137f5d0
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761988"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>排查 ITSM 连接器中的问题

本文讨论 ITSM 连接器中的常见问题，以及如何解决这些问题。

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 有关警报的详细信息，请参阅 Microsoft Azure 中的警报概述。
无论是通过邮件、短信、Webhook 还是自动完成解决方案，客户都可以选择他们希望如何收到有关警报的通知。 要通知的另一种方法是使用 ITSM。
ITSM 提供将警报发送到外部票证系统（如 ServiceNow）的选项。

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>可视化和分析事件与更改请求数据

根据设置连接时的配置，ITSMC 可以同步最多120天的事件和更改请求数据。 本文的 " [其他信息" 部分](./itsmc-synced-data.md) 提供了此数据的日志记录架构。

可以使用 ITSMC 仪表板可视化事件和更改请求数据：

![显示 ITSMC 仪表板的屏幕截图。](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

该仪表板还提供有关连接器状态的信息，可将其用作分析连接问题的起点。

若要获取有关仪表板调查的详细信息，请参阅 [使用仪表板进行错误调查](./itsmc-dashboard.md)。

### <a name="service-map"></a>服务映射

你还可以在服务映射中将与受影响计算机同步的事件可视化。

服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 它允许你查看服务器，就像你想象的那样：作为提供重要服务的互连系统。 服务映射显示在任何 TCP 连接的体系结构中服务器、进程和端口之间的连接。 除了安装代理以外，无需进行任何配置。 有关详细信息，请参阅 [使用服务映射](../insights/service-map.md)。

如果你使用服务映射，则可以查看在 ITSM 解决方案中创建的服务台项，如下所示：

![显示 Log Analytics 屏幕的屏幕截图。](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="common-symptoms---how-should-it-be-resolved"></a>常见症状-应如何解决此问题？

下面的列表包含常见问题以及如何解决此问题：

* **症状**：如果连接无法连接到 ITSM 系统，但在 **保存连接消息时出现错误** 。

    **原因**：原因可以是以下选项之一：
    * 凭据不正确
     * 权限不足
     * 应正确部署 Web 应用

    **解决方法**：
    * 对于 ServiceNow、Cherwell 和 Provance 连接：
        * 确保为每个连接正确输入了用户名、密码、客户端 ID 和客户端密码。  
        * 对于 ServiceNow：确保在相应的 ITSM 产品中具有足够的权限，以便按 [指定](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)进行连接。
  * 对于 Service Manager 连接：  
      * 确保已成功部署 web 应用并创建了混合连接。 若要验证是否已成功与本地 Service Manager 计算机建立连接，请按照建立 [混合连接](./itsmc-connections-scsm.md#configure-the-hybrid-connection)的文档中所述，参阅 WEB 应用 URL。  
* **症状**：创建了重复的工作项

    **原因**：原因可以是以下两个选项之一：
    * 为警报定义了多个 ITSM 操作。
    * 已解决警报。

    **解决方法**：可以有两个解决方案：
    * 请确保每个警报都有一个 ITSM 操作组。
    * 解决警报后，ITSM 连接器不支持匹配的工作项状态更新。 将创建新的已解决的工作项。
* **症状**：未创建工作项

    **原因**：此问题可能有几个原因：
    * ServiceNow 端的代码修改
    * 权限配置错误
    * ServiceNow 速率限制太高/低
    * 刷新令牌已过期
    * 已删除 ITSM 连接器

    **解决方法**：可以在 "连接器状态" 部分中查看 [仪表板](itsmc-dashboard.md) 并查看错误。 查看 [常见错误](itsmc-dashboard-errors.md) ，了解如何解决此错误。

* **症状**：无法为操作组创建 ITSM 操作

    **原因**：新创建的 ITSM 连接器尚未完成初始同步。

    **解决方法**：你可以查看 [常见 UI 错误](itsmc-dashboard-errors.md#ui-common-errors) ，并查明如何解决此错误。