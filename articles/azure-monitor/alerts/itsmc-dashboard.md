---
title: 使用 ITSMC 仪表板调查错误
description: 了解如何使用 IT 服务管理连接器仪表板来调查错误。
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5e1acf422abf487edda3e871fa99d07212c59b3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039523"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>使用 ITSMC 仪表板调查错误

本文包含有关 IT 服务管理连接器 (ITSMC) 仪表板的信息。 该仪表板有助于调查连接器的状态。

## <a name="view-errors"></a>查看错误

若要在该仪表板中查看错误，请执行以下操作：

1. 选择“所有资源”，然后查找“ServiceDesk (你的工作区名称)” 。

   ![显示 Azure 服务中的资源的屏幕截图。](media/itsmc-definition/create-new-connection-from-resource.png)

2. 在左窗格中的“工作区数据源”下，选择“ITSM 连接” ：

   ![屏幕截图，显示在“工作区数据源”下选择“ITSM 连接”。](media/itsmc-overview/add-new-itsm-connection.png)

3. 在“摘要”下的“IT 服务管理连接器”区域中，选择“查看摘要”  ：

   ![显示“查看摘要”按钮的屏幕截图。](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. 在“IT 服务管理连接器”区域中出现图时，请选择该图：

   ![显示选择了图的屏幕截图。](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. 此时会出现仪表板。 请使用它来查看连接器中的状态和错误。
   
   ![仪表板上显示连接器状态的屏幕截图。](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>了解仪表板元素

该仪表板包含已通过此连接器发送到 ITSM 工具的警报的相关信息。 该仪表板分为四个部分。

### <a name="created-work-items"></a>已创建的工作项 

在“已创建的工作项”区域中，图和它下方的表包含每个类型的工作项计数。 如果选择图或表，则可查看有关这些工作项的更多详细信息。

![显示已创建的工作项的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>受影响的计算机 

在“受影响的计算机”区域，表中列出了计算机及其关联的工作项。 通过选择表中的行，可以获取有关这些计算机的更多详细信息。

该表包含的行数量有限。 若要查看所有行，请选择“全部查看”。

![显示受影响的计算机的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>连接器状态 

在“连接器状态”区域中，图和它下面的表包含有关连接器状态的消息。 通过选择图或选择表中的行，可以获取有关这些消息的更多详细信息。

该表包含的行数量有限。 若要查看所有行，请选择“全部查看”。

若要详细了解表中的消息，请参阅[此文](itsmc-dashboard-errors.md)。

![显示连接器状态的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>警报规则 

在“警报规则”区域，表中包含有关检测到的警报规则数量的信息。 通过选择表中的行，可以获取有关这些检测到的规则的更多详细信息。
    
该表包含的行数量有限。 若要查看所有行，请选择“全部查看”。

![显示警报规则的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
