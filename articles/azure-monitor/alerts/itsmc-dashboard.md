---
title: 使用 ITSMC 仪表板调查错误
description: 了解如何使用 IT 服务管理连接器面板来调查错误。
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5e1acf422abf487edda3e871fa99d07212c59b3a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039523"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>使用 ITSMC 仪表板调查错误

本文包含有关 IT 服务管理连接器 (ITSMC) 仪表板的信息。 仪表板可帮助你调查连接器的状态。

## <a name="view-errors"></a>查看错误

查看仪表板中的错误：

1. 选择 " **所有资源**"，然后查找 **ServiceDesk (*你的工作区名称*)**。

   ![显示 Azure 服务中的资源的屏幕截图。](media/itsmc-definition/create-new-connection-from-resource.png)

2. 在左侧窗格的 " **工作区数据源** " 下，选择 " **ITSM 连接**：

   ![显示在 "工作区数据源" 下选择 ITSM 连接的屏幕截图。](media/itsmc-overview/add-new-itsm-connection.png)

3. 在 " **摘要**" 下的 " **IT 服务管理连接器** " 区域中，选择 " **查看摘要**"：

   ![显示 "查看摘要" 按钮的屏幕截图。](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. 当关系图出现在 **IT 服务管理连接器** 区域中时，请选择它：

   ![显示图形选择的屏幕截图。](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. 此时将显示仪表板。 使用它查看连接器中的状态和错误。
   
   ![显示仪表板上的 "连接器状态" 的屏幕截图。](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>了解仪表板元素

该仪表板包含有关通过此连接器发送到 ITSM 工具的警报的信息。 仪表板分为四个部分。

### <a name="created-work-items"></a>已创建工作项 

在 "已 **创建工作项** " 区域中，关系图和下表包含每个类型的工作项计数。 如果选择关系图或表，则可以查看有关工作项的更多详细信息。

![显示已创建工作项的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>受影响的计算机 

在 " **受影响的计算机** " 区域中，该表列出了计算机及其关联的工作项。 通过选择表中的行，可以获取有关计算机的详细信息。

该表包含的行数有限。 若要查看所有行，请选择 " **查看全部**"。

![显示受影响计算机的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>连接器状态 

在 " **连接器状态** " 区域中，关系图和下表中的表包含有关连接器状态的消息。 通过选择表中的关系图或行，可以获取有关消息的更多详细信息。

该表包含的行数有限。 若要查看所有行，请选择 " **查看全部**"。

若要详细了解表中的消息，请参阅 [此文](itsmc-dashboard-errors.md)。

![显示连接器状态的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>警报规则 

在 " **警报规则** " 区域中，表包含有关检测到的警报规则数量的信息。 通过选择表中的行，可以获取有关检测到的规则的更多详细信息。
    
该表包含的行数有限。 若要查看所有行，请选择 " **查看全部**"。

![显示警报规则的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
