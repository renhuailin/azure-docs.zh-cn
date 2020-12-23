---
title: 支持 Azure VMware 解决方案部署或预配失败
description: 从 Azure VMware 解决方案私有云获取信息，以将服务请求用于 Azure VMware 解决方案部署或预配失败。
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 27b645f4ca225fdd74bca6499b6581b3803e41a4
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542399"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>针对 Azure VMware 解决方案部署或预配失败提出支持请求

本文介绍如何打开 [支持请求](https://rc.portal.azure.com/#create/Microsoft.Support) ，并提供 Azure VMware 解决方案部署或预配失败的关键信息。 

如果私有云发生故障，则需要在 Azure 门户中打开支持请求。 若要打开支持请求，请首先获取 Azure 门户中的一些关键信息：

- 相关性 ID
- Azure ExpressRoute 线路 ID
- 错误消息

## <a name="get-the-correlation-id"></a>获取相关 ID
 
在 Azure 中创建私有云或任何资源时，会自动为资源生成资源的相关 ID。 在支持请求中包含私有云相关性 ID，以更快地打开和解决请求。

在 Azure 门户中，可以通过两种方式获取资源的相关 ID：

* **概述** 窗格
* 部署日志
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>从资源概述获取相关 ID

下面是失败的私有云部署的操作详细信息示例，其中已选择相关 ID：

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="显示具有所选相关 ID 的失败私有云部署的屏幕截图。":::

若要访问私有云 **概述** 窗格中的部署结果，请执行以下操作：

1. 在 Azure 门户中，选择你的私有云。

1. 在左侧菜单中，选择 " **概述** "。

启动部署后，部署的结果将显示在 "私有云 **概述** " 窗格中。

复制并保存要包含在服务请求中的私有云部署相关 ID。

### <a name="get-the-correlation-id-from-the-deployment-log"></a>从部署日志获取相关 ID

可以通过在 Azure 门户中搜索 "部署" 活动日志获取失败的部署的相关 ID。

访问部署日志：

1. 在 Azure 门户中，选择私有云，然后选择 "通知" 图标。

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="显示 Azure 门户中的 &quot;通知&quot; 图标的屏幕截图。":::

1. 在 " **通知** " 窗格中，选择 **"活动日志" 中的 "更多事件"** ：

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="屏幕截图，显示在 &quot;通知&quot; 窗格中选择的 &quot;活动日志&quot; 链接中的更多事件。":::

1. 若要查找失败的部署及其相关 ID，请搜索资源的名称或用于创建资源的其他信息。 

    以下示例显示了名为 pc03 的私有云资源的搜索结果。
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="屏幕截图，显示示例私有云资源的搜索结果以及 &quot;创建或更新 PrivateCloud&quot; 窗格。":::
 
1. 在 " **活动日志** " 窗格的搜索结果中，选择失败的部署的操作名称。

1. 在 " **创建或更新 PrivateCloud** " 窗格中，选择 " **JSON** " 选项卡，然后 `correlationId` 在显示的日志中查找。 复制 `correlationId` 值以在支持请求中包含它。 
 
## <a name="copy-error-messages"></a>复制错误消息

若要帮助解决你的部署问题，请包括 Azure 门户中显示的任何错误消息。 选择一条警告消息以查看错误摘要：
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="屏幕截图，在 &quot;错误&quot; 窗格的 &quot;摘要&quot; 选项卡上显示错误详细信息，并选择 &quot;复制&quot; 图标。":::

若要复制错误消息，请选择 "复制" 图标。 保存要包含在支持请求中的已复制邮件。
 
## <a name="get-the-expressroute-id-uri"></a>获取 ExpressRoute ID (URI) 
 
您可能正在尝试使用私有云 ExpressRoute 线路来扩展或对现有私有云进行缩放，并且失败。 在这种情况下，需要在支持请求中包含 ExpressRoute ID。

复制 ExpressRoute ID：

1. 在 Azure 门户中，选择你的私有云。
1. 在左侧菜单中的 " **管理** " 下，选择 " **连接** "。 
1. 在右侧窗格中，选择 " **ExpressRoute** " 选项卡。
1. 选择 " **EXPRESSROUTE ID** " 的复制图标，并保存要在支持请求中使用的值。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="将 ExpressRoute ID 复制到剪贴板。"::: 
 
## <a name="pre-validation-failures"></a>预验证失败

如果在部署) 之前 (你的私有云预验证检查失败，则不会生成相关 ID。 在此方案中，你可以在支持请求中提供以下信息：

- 错误消息和失败消息。 此类消息在许多失败中都很有用，例如，对于与配额相关的问题。 如本文所述，复制这些消息并将其包括在支持请求中是非常重要的。
- 用于创建 Azure VMware 解决方案私有云的信息，包括：
  - 位置
  - 资源组
  - 资源名称

## <a name="create-your-support-request"></a>创建支持请求

有关创建支持请求的常规信息，请参阅 [如何创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 

若要创建 Azure VMware 解决方案部署或预配失败的支持请求：

1. 在 Azure 门户中，选择 " **帮助** " 图标，然后选择 " **新建支持请求** "。

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Azure 门户中的 &quot;新建支持请求&quot; 窗格的屏幕截图。":::

1. 输入或选择所需的信息：

   1. 在“基本信息”选项卡上：

      1. 对于 " **问题类型** "，请选择 " **配置" 和 "安装问题** "。

      1. 对于 **问题子类型** ，请选择 " **预配私有云** "。

   1. 在 " **详细信息** " 选项卡上：

      1. 输入或选择所需信息。

      1. 粘贴请求此信息的相关 ID 或 ExpressRoute ID。 如果看不到这些值的特定文本框，请将其粘贴到 " **提供有关问题的详细信息** " 文本框中。

    1. 在 " **提供有关问题的详细信息** " 文本框中，粘贴任何错误详细信息，包括复制的错误或失败消息。

1. 查看你的条目，然后选择 " **创建** " 以创建支持请求。
