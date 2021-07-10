---
title: 创建 Elastic 应用程序 - Azure 合作伙伴解决方案
description: 本文介绍如何使用 Azure 门户创建 Elastic 的实例。
ms.service: partner-services
ms.topic: quickstart
ms.date: 05/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: ebf2fd078f5492537455c5819235d359367613cb
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482261"
---
# <a name="quickstart-get-started-with-elastic"></a>快速入门：Elastic 入门

在本快速入门中，使用 Azure 门户将一个 Elastic 实例与 Azure 解决方案集成。

## <a name="prerequisites"></a>先决条件

- 订阅所有者 - 与 Azure 的 Elastic 集成只能由对 Azure 订阅拥有“所有者”访问权限的用户创建。 在开始设置之前，[请确认拥有适当的访问权限](../../role-based-access-control/check-access.md)。
- 单一登录应用 - 通过单一登录 (SSO) 启用了在 Azure 门户和 Elastic Cloud 之间自动导航的功能。 此选项自动对所有 Azure 用户启用并打开。 

## <a name="find-offer"></a>查找产品/服务

使用 Azure 门户找到 Elastic 应用程序。

1. 在 Web 浏览器中转到 [Azure 门户](https://portal.azure.com/)，然后登录。

1. 如果最近访问过“市场”，请从可用选项中选择该图标。 否则，请搜索“市场”。

    :::image type="content" source="media/create/marketplace.png" alt-text="市场图标。":::

1. 搜索“Elastic”，并从可用的产品/服务中选择“Elasticsearch (Elastic Cloud)”。

1. 选择“设置 + 订阅”。

   :::image type="content" source="media/create/set-up.png" alt-text="选择产品/服务。":::

## <a name="create-resource"></a>创建资源

选择 Elastic 的产品/服务后，即可设置应用程序。

1. 在“创建 Elastic 资源”基础信息页上，输入以下值。

    :::image type="content" source="media/create/create-resource.png" alt-text="设置 Elastic 资源的窗体。":::

    | 属性 | 说明 |
    | ---- | ---- |
    | **订阅** | 从下拉菜单，选择你对其具有所有者访问权限的 Azure 订阅。 |
    | **资源组** | 指定是要创建新的资源组还是使用现有资源组。 资源组是用于保存 Azure 解决方案相关资源的容器。 有关详细信息，请参阅 [Azure 资源组概述](../../azure-resource-manager/management/overview.md)。 |
    | Elastic 帐户名称 | 为要创建的 Elastic 帐户提供名称 |
    | 区域 | 选择“美国西部 2”或“英国南部” 。 在预览期间，Elastic 仅支持这些区域。 |
    | 定价计划 | 即用即付。 |
    | **价格** | 基于选定的 Elastic 计划指定。 |

   完成后，选择“下一步: 日志和指标”。

1. 在“日志和指标”中，指定要发送到 Elastic 的日志。

    :::image type="content" source="media/create/configure-logs.png" alt-text="选择要发送的日志。":::

   可将两种类型的日志从 Azure 发送到 Elastic。

   订阅日志提供[管理平台](../../azure-resource-manager/management/control-plane-and-data-plane.md)的订阅中针对各个 Azure 资源操作的见解。 这些日志还提供有关服务运行状况事件的更新。 使用活动日志，可确定订阅中针对资源的任何写入操作（PUT、POST、DELETE）的“什么操作”、“谁操作”和“操作时间”等信息。 每个 Azure 订阅都具有单个活动日志。

   Azure 资源日志提供在[数据平面](../../azure-resource-manager/management/control-plane-and-data-plane.md)内发生的操作的见解。 例如，从密钥保管库获取密钥或向数据库发出请求都是数据平面活动。 资源日志的内容因 Azure 服务和资源类型而异。 Azure 资源日志的类型在 [Azure Monitor 资源日志类别](../../azure-monitor/platform/resource-logs-categories.md)中列出。

   要筛选发送日志到 Elastic 的 Azure 资源，请使用资源标记。 发送日志的标记规则是：

   * 默认情况下，将收集所有资源的日志。 
   * 带有 Include 标记的资源会将日志发送到 Elastic。 
   * 带有 Exclude 标记的资源不会将日志发送到 Elastic。 
   * 如果包含规则与排除规则之间存在冲突，排除规则优先。
 
   选择“下一步: 标记”，为新的 Elastic 资源设置标记。

1. 在“标记”中，为新的 Elastic 资源添加自定义标记。 每个标记包含一个名称和值。 添加完标记后，选择“下一步: 审阅 + 创建”，导航到资源创建的最后一步。 

   :::image type="content" source="media/create/add-tags.png" alt-text="向 Elastic 资源添加标记":::

1. 在“审阅 + 创建”上，配置将得到验证。 可审阅你在之前的窗体中所做的选择。 还可以审阅此产品/服务的条款。

   :::image type="content" source="media/create/review-validation.png" alt-text="审阅和验证选择":::

   验证成功并审阅条款后，选择“创建”。

1. Azure 启动部署。

   :::image type="content" source="media/create/deployment-in-progress.png" alt-text="部署状态":::

1. 部署完成后，选择“转到资源”，查看已部署的资源。

    :::image type="content" source="media/create/deployment-complete.png" alt-text="查看部署的状态":::


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [管理 Elastic 资源](manage.md)
