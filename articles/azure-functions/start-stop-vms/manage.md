---
title: 管理启动/停止 VM v2（预览版）
description: 本文介绍如何监视由启动/停止 VM v2（预览版）功能管理的 Azure VM 状态，并执行其他管理任务。
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 40c3d2dba3d41c7651846d09d01dd7afdce15af9
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112967054"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>如何管理启动/停止 VM v2（预览版）

## <a name="azure-dashboard"></a>Azure 仪表板

启动/停止 VM v2（预览版）包含一个[仪表板](../../azure-monitor/visualizations.md#azure-dashboards) ，可帮助你了解 VM 的管理范围和近期操作。 这是验证在 Azure VM 上执行的每个操作的状态的一种快速而简单的方法。 每个磁贴中的可视化效果都基于日志查询，若要查看查询结果，在磁贴的右上角选择“在日志中打开”选项。 这将在 Azure 门户中打开 [日志分析](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics)工具，在这里，你可以评估查询结果并进行修改以支持你的需求，例如自定义[日志警报](../../azure-monitor/alerts/alerts-log.md)、自定义[工作簿](../../azure-monitor/visualize/workbooks-overview.md)等。

每个仪表板中的磁贴做显示的日志数据每隔一小时刷新一次，还有一个即时的手动刷新选项，具体为单击给定可视化效果上的“刷新”图标或刷新整个仪表板。

若要了解如何使用基于日志的仪表板，请参阅以下[教程](../../azure-monitor/visualize/tutorial-logs-dashboards.md)。

> [!NOTE]
> 如果你在部署过程中遇到问题、在使用“启动/停止 VM v2 (预览版)”时遇到问题，或者有相关的问题，则可在 [GitHub](https://github.com/microsoft/startstopv2-deployments/issues) 上提交问题。 从 [Azure 支持站点](https://azure.microsoft.com/support/options/)提交 Azure 支持事件的功能不适用于此预览版本。 

## <a name="configure-email-notifications"></a>配置电子邮件通知

若要在部署“启动/停止 VM v2”之后更改电子邮件通知，可以修改在部署期间创建的操作组。

1. 在 Azure 门户中，依次导航到“监视”和“警报”。  选择“管理操作”。

1. 在“管理操作”页上，选择名为 StartStopV2_VM_Notication 的操作组。 

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="操作组页面的屏幕截图。":::

1. 在 StartStopV2_VM_Notification 页上，你可以修改电子邮件/短信/推送/语音通知选项。 添加其他操作或将现有配置更新到此操作组，然后单击“确定”保存更改。

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="示例电子邮件地址已更新的电子邮件/短信/推送/语音页的屏幕截图。":::

    若要详细了解操作组，请参阅：[操作组](../../azure-monitor/alerts/action-groups.md)

以下屏幕截图是该功能关闭虚拟机时发送的示例电子邮件。

:::image type="content" source="media/manage/email-notification-example.png" alt-text="该功能关闭虚拟机时发送的示例电子邮件的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

若要处理 VM 管理过程中出现的问题，请参阅[排查启动/停止 VM v2（预览版）的问题](troubleshoot.md)。