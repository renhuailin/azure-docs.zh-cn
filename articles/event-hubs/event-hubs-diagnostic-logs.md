---
title: 设置诊断日志 - Azure 事件中心 | Microsoft Docs
description: 了解如何为 Azure 中的事件中心设置活动日志和诊断日志。
ms.topic: article
ms.date: 06/13/2021
ms.openlocfilehash: ecdd6d9bda9f468bf5ad1510971437838fb75ff8
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112417192"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>设置 Azure 事件中心的诊断日志

可以查看两种类型的 Azure 事件中心日志：

* **[活动日志](../azure-monitor/essentials/platform-logs-overview.md)** ：此类日志提供对作业执行的操作的相关信息。 这些日志始终启用。 要查看活动日志条目，可以在 Azure 门户的事件中心命名空间的左窗格中选择“活动日志”。 例如：“创建或更新命名空间”、“创建或更新事件中心”。

    ![事件中心命名空间的活动日志](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[诊断日志](../azure-monitor/essentials/platform-logs-overview.md)** ：通过使用 API 或语言 SDK 上的管理客户端，诊断日志可提供有关对命名空间所执行操作的更丰富的信息。 
    
    下面的部分演示如何对事件中心命名空间启用诊断日志。

## <a name="enable-diagnostic-logs"></a>启用诊断日志
诊断日志默认已禁用。 若要启用诊断日志，请执行以下步骤：

1.  在 [Azure 门户](https://portal.azure.com)中，导航到你的事件中心命名空间。 
2. 在左窗格中，选择“监视”下的“诊断设置”，然后选择“+ 添加诊断设置”  。 

    ![诊断设置页面 - 添加诊断设置](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. 在“类别详细信息”部分中，选择要启用的诊断日志类型 。 本文的稍后部分将介绍有关这些类别的详细信息。 
5. 在“目标位置详细信息”部分中，设置所需的存档目标（目标位置）；例如，存储帐户、事件中心或 Log Analytics 工作区。

    ![“添加诊断设置”页面](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  选择工具栏上的“保存”以保存诊断设置。

    新设置在大约 10 分钟后生效。 在此之后，日志将出现在“诊断日志”窗格上配置的存档目标中。

    有关配置诊断的详细信息，请参阅 [Azure 诊断日志概述](../azure-monitor/essentials/platform-logs-overview.md)。

## <a name="diagnostic-logs-categories"></a>诊断日志类别
[!INCLUDE [event-hubs-diagnostic-log-schema](./includes/event-hubs-diagnostic-log-schema.md)]



## <a name="next-steps"></a>后续步骤
- [事件中心简介](./event-hubs-about.md)
- [事件中心示例](sdks.md)
- 事件中心入门
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
