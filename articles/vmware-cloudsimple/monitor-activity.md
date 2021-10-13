---
title: 监视私有云活动
titleSuffix: Azure VMware Solution by CloudSimple
description: 介绍 Azure VMware Solution by CloudSimple 环境下活动的可用信息，包括警报、事件、任务和审核。
author: suzizuber
ms.author: v-szuber
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bc83c19e18913b08411807c7b60dc6a47572cba9
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617466"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>监视 VMware Solution by CloudSimple 活动

CloudSimple 活动日志提供对于在 CloudSimple 门户完成的操作的见解。  列表包括警报、事件、任务和审核。  使用活动日志来确定执行人员、执行时间和执行操作。  活动日志不包括用户完成的任何读取操作。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

访问 [CloudSimple 门户](access-cloudsimple-portal.md)。

## <a name="activity-information"></a>活动信息

若要访问活动页，请在侧边菜单选择“活动”。

![活动页概述](media/activity-page-overview.png)

若要查看活动页上的任何活动的详细信息，请选择该活动。 右侧将打开一个详细信息面板。 面板中的操作取决于活动类型。 单击“X”关闭此面板。

单击列标题对显示内容进行排序。  可以筛选特定值的列进行查看。  单击“以 CSV 格式下载”图标下载活动报告。

## <a name="alerts"></a>警报

警报是指 CloudSimple 环境中任何重要活动的通知。  警报包括影响计费或用户访问的事件。

若要确认警报并将其从列表中删除，请从列表中选择一个或多个警报，然后单击“确认”。

以下是可用于警报的信息列。 单击“编辑列”，然后选择要查看的列。

| 列 | 说明 |
------------ | ------------- |
| 警报类型 | 警报类别。|
| 时间 | 出现警报的时间。 |
| 严重性 | 警报的重要性。|
| 资源名称 | 分配给资源的名称，如私有云名称。 |
| 资源类型 | 资源类别：私有云、云机架。 |
| 资源 ID | 资源的标识符。 |
| 说明 | 触发警报的说明。 |
| 已确认 | 指示是否确认了警报。 |

## <a name="events"></a>事件

事件显示 CloudSimple 门户上的用户和系统活动。 事件页列出与特定资源关联的活动以及影响的严重性。

以下是可用于警报的信息列。 单击“编辑列”，然后选择要查看的列。

| 列 | 说明 |
------------ | ------------- |
| 时间 | 事件发生的日期和时间。 |
| 事件类型 | 定义事件的数值代码。 |
| 严重性 | 事件严重级别。|
| 资源名称 | 分配给资源的名称，如私有云名称。 |
| 资源类型 | 资源类别：私有云、云机架。 |
| 说明 | 触发警报的说明。 |

## <a name="tasks"></a>任务

任务是预计需要 30 秒及更长时间才能完成的私有云活动。 （预计耗时少于 30 秒的活动仅报告为事件。）打开“任务”页面以跟踪私有云的任务进度。

以下是可用于警报的信息列。 单击“编辑列”，然后选择要查看的列。

| 列 | 说明 |
------------ | ------------- |
| 任务 ID | 任务的唯一标识符。 |
| 操作 | 任务执行的操作。 |
| 用户 | 被分配的要完成任务的用户。 |
| 资源名称 | 分配给资源的名称。 |
| 资源类型 | 资源类别：私有云、云机架。 |
| 资源 ID | 资源的标识符。 |
| 开始 | 任务的开始时间。 |
| 结束 | 任务的结束时间。 |
| 状态 | 当前任务状态。 |
| 已用时间 | 完成此任务所需的时间（如果任务已完成），或当前所用时间（如果任务仍在进行）。 |
| 说明 | 任务说明。 |

## <a name="audit"></a>审核

审核日志可跟踪用户活动。 可以使用审核日志来监视所有用户的用户活动。

以下是可用于警报的信息列。 单击“编辑列”，然后选择要查看的列。

| 列 | 说明 |
------------ | ------------- |
| 时间 | 审核条目的时间。 |
| 操作 | 任务执行的操作。 |
| 用户 | 被分配任务的人员。 |
| 资源名称 | 分配给资源的名称。 |
| 资源类型 | 资源类别：私有云、云机架。 |
| 资源 ID | 资源的标识符。 |
| 结果 | 活动结果，如“成功”。 |
| 所用时间 | 完成任务所用时间。 |
| 说明 | 操作的说明。 |

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 了解有关[私有云](cloudsimple-private-cloud.md)的详细信息
