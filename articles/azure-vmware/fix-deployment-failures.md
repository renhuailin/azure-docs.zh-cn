---
title: 支持 Azure VMware 解决方案部署或预配失败
description: 从 Azure VMware 解决方案私有云获取信息，以针对 Azure VMware 解决方案部署或预配失败提出服务请求。
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 58c7277d19a1b8a512eca0233548ebc388a2c459
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713085"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>针对 Azure VMware 解决方案部署或预配失败提出支持请求

本文介绍如何提交[支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)，并提供 Azure VMware 解决方案部署或预配失败的关键信息。 

如果私有云发生故障，则需要在 Azure 门户中提交支持请求。 要提交支持请求，请首先获取 Azure 门户中的一些关键信息：

- 相关性 ID
- Azure ExpressRoute 线路 ID
- Error messages

## <a name="get-the-correlation-id"></a>获取相关 ID
 
在 Azure 中创建私有云或任何资源时，会自动为资源生成资源的相关 ID。 在支持请求中包含私有云相关 ID，以更快地提交和解决请求。

在 Azure 门户中，可以通过两种方式获取资源的相关 ID：

* “概述”窗格
* 部署日志
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>从资源概述中获取相关 ID

下面是一个失败的私有云部署的操作详细信息示例，其中已选中相关 ID：

:::image type="content" source="media/fix-deployment-failures/failed-private-cloud-deployment.png" alt-text="显示失败的私有云部署且选中了相关 ID 的屏幕截图。":::

要在私有云的“概述”窗格中访问部署结果，请执行以下操作：

1. 在 Azure 门户中，选择私有云。

1. 在左侧菜单上选择“概述”。

启动部署后，部署结果将显示在私有云的“概述”窗格中。

复制并保存要包含在服务请求中的私有云部署相关 ID。

### <a name="get-the-correlation-id-from-the-deployment-log"></a>从部署日志获取相关 ID

可以通过在 Azure 门户中搜索部署活动日志获取失败部署的相关 ID。

要访问部署日志，请执行以下操作：

1. 在 Azure 门户中，选择你的私有云，然后选择通知图标。

   :::image type="content" source="media/fix-deployment-failures/open-notifications.png" alt-text="在 Azure 门户中显示通知图标的屏幕截图。":::

1. 在“通知”窗格中，选择“活动日志中的更多事件”：

    :::image type="content" source="media/fix-deployment-failures/more-events-in-activity-log.png" alt-text="显示通知”窗格中选中的“活动日志中的更多事件”的屏幕截图。":::

1. 要查找失败部署及其相关 ID，请搜索资源的名称或用于创建资源的其他信息。 

    以下示例显示了名为 pc03 的私有云资源的搜索结果。
 
    :::image type="content" source="media/fix-deployment-failures/find-past-deployments.png" alt-text="显示示例私有云资源的搜索结果以及“创建或更新私有云”窗格的屏幕截图。":::
 
1. 在“活动日志”窗格的搜索结果中，选择失败部署的操作名称。

1. 在“创建或更新私有云”窗格中，选择“JSON”选项卡，然后在显示的日志中查找 `correlationId`。  复制 `correlationId` 值以在将其包含在你的支持请求中。 
 
## <a name="copy-error-messages"></a>复制错误消息

为帮助解决你的部署问题，请包括 Azure 门户中显示的任何错误消息。 选择一条警告消息以查看错误摘要：
 
:::image type="content" source="media/fix-deployment-failures/summary-of-errors.png" alt-text="显示了“错误”窗格的“摘要”选项卡上的错误详细信息并选中了“复制”图标的屏幕截图。":::

要复制错误消息，请选择复制图标。 保存复制的消息以便包含在你的支持请求中。
 
## <a name="get-the-expressroute-id-uri"></a>获取 ExpressRoute ID (URI)
 
你可能正在尝试使用私有云 ExpressRoute 线路对现有私有云进行扩展或对等化，并且失败。 在这种情况下，需要在支持请求中包含 ExpressRoute ID。

要复制 ExpressRoute ID，请执行以下操作：

1. 在 Azure 门户中，选择私有云。
1. 在左侧菜单中的“管理”下，选择“连接” 。 
1. 在右窗格中选择“ExpressRoute”选项卡。
1. 选择 ExpressRoute ID 对应的复制图标，并保存要在支持请求中使用的值。
 
:::image type="content" source="media/expressroute-global-reach/expressroute-id.png" alt-text="显示要复制到剪贴板的 ExpressRoute ID 的屏幕截图。"::: 
 
## <a name="pre-validation-failures"></a>预验证失败

如果你的私有云预验证检查失败（在部署之前），则不会生成相关 ID。 在这种情况下，你可以在支持请求中提供以下信息：

- 错误消息和失败消息。 此类消息在许多失败中都很有用，例如，对于与配额相关的问题。 如本文所述，复制这些消息并将其包括在支持请求中是非常重要的。
- 用于创建 Azure VMware 解决方案私有云的信息，包括：
  - 位置
  - 资源组
  - 资源名称

## <a name="create-your-support-request"></a>创建支持请求

有关创建支持请求的常规信息，请参阅 [如何创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 

要针对 Azure VMware 解决方案部署或预配失败创建支持请求，请执行以下操作：

1. 在 Azure 门户中，选择“帮助”图标，然后选择“新建支持请求”。

    :::image type="content" source="media/fix-deployment-failures/open-support-request.png" alt-text="Azure 门户中“新建支持请求”窗格的屏幕截图。":::

1. 输入或选择所需信息：

   1. 在“基本信息”选项卡上：

      1. 对于“问题类型”，请选择“配置和设置问题”。 

      1. 对于“问题子类型”，请选择“预配私有云”。 

   1. 在“详细信息”选项卡上：

      1. 输入或选择所需信息。

      1. 将 ID 或 ExpressRoute ID 粘贴到需要该信息的位置。 如果看不到这些值的特定文本框，请将其粘贴到“提供有关问题的详细信息”文本框中。

    1. 在“提供有关问题的详细信息”文本框中，粘贴任何错误详细信息，包括复制的错误或失败消息。

1. 查看你的输入，然后选择“创建”以创建支持请求。
