---
title: 管理应用服务计划
description: 了解如何执行不同的任务来管理应用服务计划，如创建、移动、缩放和删除。
keywords: 应用服务, azure 应用服务, 缩放, 应用服务计划, 更改, 创建, 管理
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: bae05ac9f7b8287cf7e66a0c1a0e634e8d541489
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112678779"
---
# <a name="manage-an-app-service-plan-in-azure"></a>在 Azure 中管理应用服务计划

[Azure 应用服务计划](overview-hosting-plans.md)提供应用服务应用需要运行的资源。 本指南介绍如何管理应用服务计划。

## <a name="create-an-app-service-plan"></a>创建应用服务计划

> [!TIP]
> 如果已有应用服务环境，请参阅[在应用服务环境中创建应用服务计划](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)。

在创建应用时可以创建一个空的应用服务计划，也可以创建一个计划。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”。

   ![在 Azure 门户中创建资源。][createResource] 

1. 选择“新建” > “Web 应用”或其他类型的应用服务应用。

   ![在 Azure 门户中创建应用。][createWebApp] 

2. 在配置应用服务计划之前配置“实例详细信息”部分。 “发布”和“操作系统”之类的设置可以更改应用服务计划的可用定价层。  “区域”决定了在何处创建应用服务计划。 
   
3. 在“应用服务计划”部分选择一个现有的计划，或者通过选择“新建”创建一个计划。

   ![创建应用服务计划。][createASP] 

4. 创建计划时，可以选择新计划的定价层。 在“SKU 和大小”中选择“更改大小”，以便更改定价层。  

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>将应用移到另一个应用服务计划

只要源计划和目标计划在 _同一个资源组和地理区域_ 中，就可将应用移到另一个应用服务计划。

> [!NOTE]
> Azure 会将每个新的应用服务计划部署到部署单元（在内部称为 Web 空间）中。 每个区域都可以有许多 Web 空间，但应用只能在相同 Web 空间中创建的计划之间移动。 应用服务环境是一个独立的 Web 空间，因此可以在相同应用服务环境中的计划之间移动应用，但无法在不同应用服务环境中的计划之间移动应用。
>
> 无法在创建计划时指定所需的 Web 空间，但这可确保计划创建于与现有计划相同的 Web 空间中。 简而言之，使用同一资源组和区域组合创建的所有计划都部署到同一 Web 空间中。 例如，如果你在资源组 A 和区域 B 中创建了一个计划，那么你随后在资源组 A 和区域 B 中创建的任何计划都将部署到同一 Web 空间中。 请注意，计划创建之后便不能移动 Web 空间，所以无法通过将计划移至另一个资源组，将其移到与另一个计划“相同的 Web 空间”中。
> 

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“应用服务”，然后选择要移动的应用。

2. 从左菜单中选择“更改应用服务计划”。

3. 在“应用服务计划”下拉列表中，选择要将应用移到其中的现有计划。 下拉列表仅显示与当前应用服务计划位于同一资源组和地理区域的计划。 如果不存在此类计划，则默认情况下，你可以创建一个计划。 也可选择“新建”，以手动方式创建新计划。

4. 如果创建一项计划，则可选择新计划的定价层。 在“定价层”中选择现有层，对其进行更改。 
   
   > [!IMPORTANT]
   > 若要将应用从高层计划移到低层计划（例如，从 **D1** 移到 **F1**），应用可能会在目标计划中丢失某些功能。 例如，如果应用使用 TLS/SSL 证书，可能会出现以下错误消息：
   >
   > `Cannot update the site with hostname '<app_name>' because its current TLS/SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed TLS/SSL configuration is 'Disabled'.`

5. 完成后，选择“确定”。
   
   ![应用服务计划选择器。][change] 

## <a name="move-an-app-to-a-different-region"></a>将应用移到不同的区域

运行应用的区域是该应用的应用服务计划所在的区域。 但是，无法更改应用服务计划的区域。 如果想要在不同的区域中运行应用，替代方法是使用应用克隆。 克隆可以复制任何区域的新的或现有的应用服务计划中的应用。

可以在菜单的“开发工具”部分找到“克隆应用”。 

> [!IMPORTANT]
> 克隆具有一些限制。 可以在 [Azure 应用服务应用克隆](app-service-web-app-cloning.md)中查看相关信息。

## <a name="scale-an-app-service-plan"></a>缩放应用服务计划

若要提高应用服务计划的定价层，请参阅[在 Azure 中纵向扩展应用](manage-scale-up.md)。

若要增加应用的实例计数，请参阅[手动或自动缩放实例计数](../azure-monitor/autoscale/autoscale-get-started.md)。

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>删除应用服务计划

为了避免产生意外的费用，删除应用服务计划中的最后一个应用时，应用服务默认也会删除该计划。 如果改为选择保留该计划，应将该计划更改为“免费”层，以免产生费用。

> [!IMPORTANT]
> 未与任何应用关联的应用服务计划仍会产生费用，因为它们继续保留配置的 VM 实例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [纵向扩展 Azure 中的应用](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png