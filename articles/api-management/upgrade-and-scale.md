---
title: 升级和缩放 Azure API 管理实例 | Microsoft Docs
description: 本主题介绍如何升级和缩放 Azure API 管理实例。
services: api-management
documentationcenter: ''
author: dlepow
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 04/20/2020
ms.author: danlep
ms.openlocfilehash: 9cec5717c6c25faafc7349a4c18b60cc30e97f26
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659959"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>升级和缩放 Azure API 管理实例  

客户可通过添加和删除单位来缩放 Azure API 管理实例。 “单位”由专用的 Azure 资源构成，具有以每月 API 调用次数表示的承载容量。 此数字不表示调用限制，而是粗略容量规划允许的最大吞吐量值。 实际吞吐量和延迟因多种因素而异，例如并发连接的数量和速率、配置策略的类型和数量、请求和响应大小以及后端延迟。

每个单位的容量和价格取决于该单位所在的“层”。 可以在以下四个层中进行选择：**开发人员**、**基本**、**标准**、**高级**。 如果需要提高层中服务的容量，应添加单位。 如果当前在 API 管理实例中选择的层不允许添加更多单位，则需要升级到更高级别的层。

每个单位的价格和可用的功能（例如多区域部署）取决于为 API 管理实例选择的层。 [定价详细信息](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)一文介绍了每个单位的价格，以及每个层提供的功能。 

>[!NOTE]
>[定价详细信息](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)一文说明了每个层中单位容量的大致数字。 若要获取更准确的数字，需要分析所用 API 的实际方案。 请参阅 [Azure API 管理实例的容量](api-management-capacity.md)一文。

## <a name="prerequisites"></a>先决条件

若要执行本文中的步骤，必须：

+ 拥有一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ 具有 API 管理实例。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。

+ 了解 [Azure API 管理实例容量](api-management-capacity.md)的概念。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>升级和缩放  

可以在以下四个层中进行选择：“开发人员”、“基本”、“标准”和“高级” 。 应将“开发人员”层用于评估服务；不应将其用于生产。 “开发人员”层不附带 SLA，无法缩放此层（添加/删除单位）。 

“基本”、“标准”和“高级”是附带 SLA 的生产层，可以缩放  。 “基本”层是附带 SLA 的最便宜层，最多可以扩展 2 个单位，“标准”层最多可以扩展到四个单位 。 可将任意数目的单位添加到“高级”层。

使用“高级”层可在任意数目的所需 Azure 区域中分配单个 Azure API 管理实例。 最初创建 Azure API 管理服务时，实例仅包含一个单位，并驻留在单个 Azure 区域中。 初始区域被指定为“主要”区域。 可以轻松添加更多区域。 添加区域时，需指定想要分配的单位数。 例如，可在“主要”区域中包含一个单位，在另外某个区域中包含五个单位。 可以定制用于处理每个区域中的流量的单位数。 有关详细信息，请参阅[如何将 Azure API 管理服务实例部署到多个 Azure 区域](api-management-howto-deploy-multi-region.md)。

可在任意两个层之间来回升级和降级。 升级或降级可能会删除某些功能，例如，从“高级”层降级到“标准”或“基本”层时，会删除 VNET 或多区域部署。

> [!NOTE]
> 升级或缩放过程可能需要 15 到 45 分钟才能完成。 完成时会收到通知。

> [!NOTE]
> “消费”层中的 API 管理服务会根据流量自动缩放。

## <a name="scale-your-api-management-service"></a>缩放 API 管理服务

![在 Azure 门户中缩放 API 管理服务](./media/upgrade-and-scale/portal-scale.png)

1. 在 [Azure 门户](https://portal.azure.com/)导航到 API 管理服务。
2. 从菜单中选择“位置”。
3. 单击要缩放的位置所在的行。
4. 指定新的“单位数” - 使用滑块或键入数字。
5. 单击“应用” 。

## <a name="change-your-api-management-service-tier"></a>更改 API 管理服务层

1. 在 [Azure 门户](https://portal.azure.com/)导航到 API 管理服务。
2. 单击菜单中的“定价层”。
3. 从下拉列表中选择所需服务层。 更改后，使用滑块指定 API 管理服务的规模。
4. 单击“保存” 。

## <a name="downtime-during-scaling-up-and-down"></a>纵向扩展和纵向缩减过程中的停机时间
若要从开发人员层缩放或缩放到开发人员层，将会出现停机时间。 否则，不会出现停机时间。 

## <a name="compute-isolation"></a>计算隔离
如果安全要求包括[计算隔离](../azure-government/azure-secure-isolation-guidance.md#compute-isolation)，则可以使用“隔离”定价层。 该层可确保 API 管理服务实例的计算资源使用整个物理主机，并提供支持工作负载（例如美国国防部影响级别 5 (IL5) 工作负载）所需的必要隔离级别。 要获取隔离层访问权限，请[创建支持票证](../azure-portal/supportability/how-to-create-azure-support-request.md)。 



## <a name="next-steps"></a>后续步骤

- [如何将 Azure API 管理服务实例部署到多个 Azure 区域](api-management-howto-deploy-multi-region.md)
- [如何自动缩放 Azure API 管理服务实例](api-management-howto-autoscale.md)
- [规划和管理 API 管理成本](plan-manage-costs.md)
- [API 管理限制](../azure-resource-manager/management/azure-subscription-service-limits.md#api-management-limits)