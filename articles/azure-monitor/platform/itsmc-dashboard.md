---
title: 使用仪表板调查错误
description: 本文档包含有关 ITSMC 仪表板上的错误的信息。
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: ebd59e637e498b8088fe9a302b1bb12efdf2c173
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089269"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>使用 ITSMC 仪表板调查错误

本文包含有关 IT 服务管理连接器 (ITSMC) 仪表板的信息。 该仪表板可帮助你调查 ITSMC 的状态。

## <a name="view-the-dashboard"></a>查看仪表板

按照以下步骤打开仪表板。

1. 选择 " **所有资源**"，然后查找 **ServiceDesk (*你的工作区名称*)**。

   ![显示 Azure 服务中的资源的屏幕截图。](media/itsmc-definition/create-new-connection-from-resource.png)

1. 在左窗格中，选择 " **工作区数据源**"，然后选择 " **ITSM 连接**"。

   ![显示在 "工作区数据源" 下选择 ITSM 连接的屏幕截图。](media/itsmc-overview/add-new-itsm-connection.png)

1. 在 " **摘要** " 部分中，选择 " **查看摘要** " 以查看摘要图表。

    ![显示 "摘要" 部分中的 "查看摘要" 选项的屏幕截图。](media/itsmc-resync-servicenow/dashboard-view-summary.png)

1. 选择 " **摘要** " 部分中的关系图以打开仪表板。

    ![显示选择摘要图的屏幕截图。](media/itsmc-resync-servicenow/dashboard-graph-click.png)

1. 查看仪表板中的状态和连接器中的任何错误。
    ![显示仪表板的屏幕截图。](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>了解仪表板元素

该仪表板包含有关通过使用此连接器发送到 ITSM 工具的警报的信息。

仪表板分为四个部分：

- **工作项已创建**：图形和表按类型显示工作项的数目。 选择关系图或表，了解有关工作项的详细信息。
      ![显示工作项创建部分的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
- **受影响的计算机**：该表包含有关创建工作项的配置项目的详细信息。
      选择表中的行，以了解有关配置项目的更多详细信息。
      该表包含的行数有限。 若要查看整个列表，请选择 " **查看全部**"。
      ![显示受影响计算机部分的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
- **连接器状态**：图形和表显示有关连接器状态的信息。 选择图表或表中的消息以了解更多详细信息。 该表显示的行数有限。 若要查看整个列表，请选择 " **查看全部**"。
      ![显示 "连接器状态" 部分的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
- **警报规则**：此部分显示有关检测到的警报规则数的信息。 选择表中的行，以了解有关检测到的规则的更多详细信息。 表的行数有限。 若要查看整个列表，请选择 " **查看全部**"。
      ![显示警报规则部分的屏幕截图。](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

## <a name="next-steps"></a>后续步骤

查看 [常见连接器状态错误](itsmc-dashboard-errors.md)。
