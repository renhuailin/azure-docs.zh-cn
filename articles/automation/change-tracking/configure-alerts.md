---
title: 如何为 Azure 自动化更改跟踪和清单创建警报
description: 本文介绍了如何配置 Azure 警报，以通知更改跟踪和清单检测到的更改的状态。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 91be2f8641a061d009962cdcd03a8d56048594da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594490"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>如何为更改跟踪和清单创建警报

Azure 中的警报会主动通知你 runbook 作业、服务运行状况问题或与自动化帐户相关的其他情况的结果。 Azure 自动化不包括预配置的警报规则，但你可以基于其生成的数据创建自己的警报规则。 本文分步介绍了如何创建基于更改跟踪和清单识别到的更改的预警规则。

如果你不熟悉 Azure Monitor 警报，请在开始之前参阅 [Microsoft Azure 中的警报概述](../../azure-monitor/alerts/alerts-overview.md)。 若要详细了解使用日志查询的警报，请参阅 [Azure Monitor 中的日志警报](../../azure-monitor/alerts/alerts-unified-log.md)。

## <a name="create-alert"></a>创建警报

下面的示例展示了在一台虚拟机上修改了 c:\windows\system32\drivers\etc\hosts 文件。 此文件非常重要，因为 Windows 需要使用它将主机名解析为 IP 地址。 此操作优先于 DNS，可能导致连接问题。 此外，可能导致将流量重定向到恶意网站或危险网站。

![展示了 hosts 文件更改的图表](./media/configure-alerts/changes.png)

让我们使用此示例来探讨针对更改创建警报的步骤。

1. 在自动化帐户的“更改跟踪”页上，选择“Log Analytics”。

2. 在“日志搜索”中，使用查询 `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` 查找 **hosts** 文件的内容更改。 此查询查找完全限定的路径名称包含“`hosts`”一词的文件的内容更改。 还可将路径部分更改为完全限定的格式来请求特定的文件，例如，使用 `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` 来这样做。

3. 在此查询返回结果后，选择“日志搜索”中的“新建预警规则”，以打开“警报创建”页。 还可以通过 Azure 门户中的“Azure Monitor”导航到此页。

4. 再次检查查询，并修改警报逻辑。 在本示例中，你希望在检测到更改的情况下触发警报，即使只在环境中的所有计算机上检测到一个更改。

    ![对用于跟踪 hosts 文件更改的查询进行更改](./media/configure-alerts/change-query.png)

5. 在设置警报逻辑后，分配操作组，以执行响应警报触发的操作。 在本例中，我们将设置要发送的电子邮件，以及要创建的 IT 服务管理 (ITSM) 票证。

按照以下步骤设置警报，以获取更新部署的状态。 如果不熟悉 Azure 警报，请参阅 [Azure 警报概述](../../azure-monitor/alerts/alerts-overview.md)。

## <a name="configure-action-groups-for-your-alerts"></a>配置警报对应的操作组

配置警报后，可以设置操作组，即用于多个警报的一组操作。 这些操作可能包括电子邮件通知、Runbook、Webhook 等。 若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../../azure-monitor/alerts/action-groups.md)。

1. 选择警报，然后在“操作组”下选择“新建” 。

2. 输入操作组的全名和短名称。 使用指定的组发送通知时，更新管理将使用短名称。

3. 在“操作”下，输入指定操作的名称，例如“电子邮件通知” 。

4. 对于“操作类型”，选择适当的类型（例如“电子邮件/短信/推送/语音”）。

5. 选择铅笔图标来编辑操作详细信息。

6. 填写操作类型的窗格。 例如，如果使用“电子邮件/短信/推送/语音”发送电子邮件，请输入操作名称，选中“电子邮件”复选框，输入有效的电子邮件地址，然后选择“确定”。

    ![配置电子邮件操作组](./media/configure-alerts/configure-email-action-group.png)

7. 在“添加操作组”窗格中，选择“确定”。

8. 对于警报电子邮件，你可以自定义电子邮件主题。 在“创建规则”下选择“自定义操作”，然后选择“电子邮件主题”  。

9. 完成后，请选择“创建警报规则”。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure Monitor 中的警报](../../azure-monitor/alerts/alerts-overview.md)。

* 了解[日志查询](../../azure-monitor/logs/log-query-overview.md)，以从 Log Analytics 工作区检索和分析数据。

* [使用 Azure Monitor 日志管理使用情况和成本](../../azure-monitor/logs/manage-cost-storage.md)介绍了如何通过更改数据保留期来控制成本，以及如何分析数据使用情况并对其发出警报。