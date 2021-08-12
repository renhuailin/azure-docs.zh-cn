---
title: 教程：使用警报和操作组监视 Azure Spring Cloud 资源 | Microsoft Docs
description: 了解如何使用 Spring Cloud 警报。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.custom: devx-track-java
ms.openlocfilehash: 25ff0361c960cac45b4bbeef089ff97fee07da8f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114470268"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>教程：使用警报和操作组监视 Spring Cloud 资源

本文适用于：✔️ Java ✔️ C#

Azure Spring Cloud 警报支持根据可用存储、请求速率或数据用量等条件监视资源。 当速率或条件符合定义的规范时，警报将发送通知。

设置警报管道的过程包括两个步骤： 
1. 设置一个操作组，其中包含触发警报时要执行的操作，例如发送电子邮件、短信或者运行 Runbook 或 Webhook。 可以在不同的警报中重复使用操作组。
2. 设置警报规则。 规则根据目标资源、指标、条件、时间聚合等因素将指标模式与操作组绑定在一起。

## <a name="prerequisites"></a>先决条件

除了 Azure Spring 要求外，本教程中的过程还适用于已部署的 Azure Spring Cloud 实例。  按[快速入门](./quickstart.md)的说明开始操作。

以下过程初始化 **操作组**，以及从 Spring Cloud 实例左侧导航窗格中的“警报”选项启动的 **警报**。 （该过程也可以从 Azure 门户的“监视器概述”页启动。） 

从资源组导航到 Spring Cloud 实例。 在左窗格中选择“警报”，然后选择“管理操作”： 

![门户中资源组页的屏幕截图](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>设置操作组

若要开始初始化新 **操作组** 的过程，请选择“+ 添加操作组”。

![门户中“添加操作组”的屏幕截图](media/alerts-action-groups/action-1.png)

在“添加操作组”页上：

 1. 指定一个 **操作组名称** 和 **短名称**。

 1. 指定“订阅”和“资源组”。 

 1. 指定“操作名称”。

 1. 选择 **操作类型**。  右侧随后会打开另一个窗格，用于定义激活时要执行的操作。

 1. 使用右窗格中的选项定义操作。  本例使用电子邮件通知。

 1. 在右侧操作窗格中单击“确定”。

 1. 在“添加操作组”对话框中单击“确定”。  

  ![在门户中定义操作的屏幕截图](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>设置警报 

前面的步骤创建了一个使用电子邮件的 **操作组**。 也可以使用电话通知、Webhook、Azure Functions 等。 以下步骤配置警报。

1. 导航回到“警报”页，单击“管理警报规则” 。

   ![在门户中定义警报的屏幕截图](media/alerts-action-groups/alerts-2.png)

1. 选择警报针对的 **资源**。

1. 单击“+ 新建警报规则”。

   ![门户中“新建警报规则”的屏幕截图](media/alerts-action-groups/alerts-3.png)

1. 在“创建规则”页上，指定“资源” 。

1. “条件”设置提供了许多用于监视 Spring Cloud 资源的选项 。  单击“添加”以打开“配置信号逻辑”窗格 。

1. 选择条件。 此示例采用“系统 CPU 使用率百分比”。

   ![门户中“新建警报规则”的屏幕截图 2](media/alerts-action-groups/alerts-3-1.png)

1. 向下滚动“配置信号逻辑”窗格，然后设置“阈值”以进行监视 。

   ![门户中“新建警报规则”的屏幕截图 3](media/alerts-action-groups/alerts-3-2.png)

1. 单击“完成”。

   若要详细了解可用于监视的条件，请参阅[用户门户指标选项](./concept-metrics.md#user-metrics-options)。

1. 在“操作”下，单击“选择操作组” 。 在“操作”窗格中，选择前面定义的“操作组” 。

   ![门户中“新建警报规则”的屏幕截图 4](media/alerts-action-groups/alerts-3-3.png) 

1. 向下滚动，然后在“警报详细信息”中为警报规则命名。

1. 设置“严重性”。

1. 单击“创建警报规则”。

   ![门户中“新建警报规则”的屏幕截图 5](media/alerts-action-groups/alerts-3-4.png)

1. 确认是否已启用新警报规则。

   ![门户中“新建警报规则”的屏幕截图 6](media/alerts-action-groups/alerts-4.png)

还可以使用“指标”页创建规则：

![门户中“新建警报规则”的屏幕截图 7](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何为 Azure Spring Cloud 应用程序设置警报和操作组。 若要详细了解操作组，请参阅：

> [!div class="nextstepaction"]
> [在 Azure 门户中创建和管理器操作组](../azure-monitor/alerts/action-groups.md)

> [!div class="nextstepaction"]
> [操作组中的短信警报行为](../azure-monitor/alerts/alerts-sms-behavior.md)