---
title: 使用仪表板进行错误调查
description: 本文档包含有关使用仪表板进行错误调查的信息
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9291689b362b5cbe651a72220196dd30b40745cf
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540515"
---
# <a name="error-investigation-using-the-dashboard"></a>使用仪表板进行错误调查

此页面包含有关 ITSM 连接器仪表板的信息。 此仪表板可帮助你调查 ITSM 连接器的状态。

## <a name="how-to-view-the-dashboard"></a>如何查看仪表板

若要查看仪表板中的错误，应遵循后续步骤：

1. 在 " **所有资源**" 中，查找 **ServiceDesk (*你的工作区名称*)**：

   ![显示 Azure 门户中最近的资源的屏幕截图。](media/itsmc-definition/create-new-connection-from-resource.png)

2. 在左侧窗格中的 " **工作区数据源** " 下，选择 " **ITSM 连接**：

   ![显示 ITSM 连接菜单项的屏幕截图。](media/itsmc-overview/add-new-itsm-connection.png)

3. 在 " **摘要** " 下 **IT 服务管理连接器** 中，选择 " **查看摘要**"：

    ![显示视图摘要的屏幕截图。](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. 在 " **摘要** " 下 **IT 服务管理连接器** 中，单击图形：

    ![显示图形单击的屏幕截图。](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. 使用此仪表板，你可以查看连接器中的状态和错误。
    ![显示连接器状态的屏幕截图。](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="dashboard-elements"></a>仪表板元素

该仪表板包含有关使用此连接器发送到 ITSM 工具的警报的信息。
仪表板分为四个部分：

1. 已创建工作项：图和下表包含每个类型的工作项的计数。 如果单击关系图或表上的，则可以查看有关工作项的更多详细信息。
    ![显示已创建工作项的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. 受影响的计算机：表包含有关创建配置项目的配置项目的详细信息。
    通过单击表中的行，可以获取有关配置项目的更多详细信息。
    表中包含的行数有限，若要查看所有列表，可单击 "查看全部"。
    ![显示受影响的计算机的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. 连接器状态：下图和下表包含有关连接器状态的消息。 通过单击表中的行，可以获取有关连接器状态的消息的更多详细信息。
    表中包含的行数有限，若要查看所有列表，可单击 "查看全部"。
    ![显示连接器状态的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. 警报规则：表包含有关检测到的警报规则数的信息。
    通过单击表中的行，可以获取有关检测到的规则的更多详细信息。
    表中包含的行数有限，若要查看所有列表，可单击 "查看全部"。
    ![显示警报规则的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)