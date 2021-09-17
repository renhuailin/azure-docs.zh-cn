---
title: Azure Monitor 中的可用性区域
description: Azure Monitor 中的可用性区域
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/18/2021
ms.custom: references_regions
ms.openlocfilehash: c01e83fbbf117c5491e9e12ac24aa0d5d2fa8b67
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446613"
---
# <a name="availability-zones-in-azure-monitor"></a>Azure Monitor 中的可用性区域

[Azure 可用性区域](../../availability-zones/az-overview.md)可在数据中心发生故障时保护应用程序和数据，并可为 Application Insights 等 Azure Monitor 功能及任何其他依赖于 Log Analytics 工作区的功能提供复原能力。 当某个工作区链接到某个可用性区域时，即使特定数据中心发生故障或完全关闭，其亦会通过依赖区域中的其他可用性区域，保持有效状态及可操作性。 无需执行任何操作即可切换到备用区域，甚至无需注意事件。 


## <a name="regions"></a>区域
如需了解有关具有可用性区域的 Azure 区域，请参阅 [Azure 中的区域和可用性区域](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)。 Azure Monitor 目前可在以下区域使用。 

- 美国东部 2
- 美国西部 2

## <a name="dedicated-clusters"></a>专用群集
Azure Monitor 支持需要 Log Analytics 工作区的可用性区域，同时此工作区已链接到 [Azure Monitor 专用群集](logs-dedicated-clusters.md)。 专用群集作为部署选项，可为包含可用性区域的 Azure Monitor 日志启用高级功能。

并非所有专用群集都可使用可用性区域。 自 2020 年 10 月中旬之后创建的专用群集可在创建时设置为支持可用性区域。 在该日期后创建的新群集默认为在支持 Azure Monitor 的区域中启用可用性区域。


> [!NOTE]
> 仅当 Application Insights 资源为基于工作区型资源且工作区可使用专用群集时，其才能使用可用性区域。 经典 Application Insights 资源无法使用可用性区域。


## <a name="determine-current-cluster-for-your-workspace"></a>确定工作区的当前群集
若要确定工作区的当前工作区链接状态，请使用 [CLI、PowerShell 或 REST](logs-dedicated-clusters.md#check-workspace-link-status) 检索[群集详细信息](logs-dedicated-clusters.md#check-cluster-provisioning-status)。 如果群集使用可用性区域，则其将具有一个名为 `isAvailabilityZonesEnabled` 的属性，且值为 `true`。 创建群集后便不能再更改此属性。

## <a name="create-dedicated-cluster-with-availability-zone"></a>创建可使用可用性区域的专用群集
通过在支持可用性区域的区域中[创建新的专用群集](logs-dedicated-clusters.md#create-a-dedicated-cluster)，将工作区移动到可用性区域。 系统将自动为可用性区域启用此群集， 并[将工作区链接到新群集](logs-dedicated-clusters.md#link-a-workspace-to-a-cluster)。

> [!IMPORTANT]
> 可用性区域在创建群集时即需定义，且无法修改。

向新群集转换会是个渐进式的过程。 在未清除群集内的数据之前，请勿删除先前的群集。 例如，如果已将工作区的保留期设置为 60 天，则在删除工作区之前，你可能需要保留旧群集在该段时间内的运行数据。

针对工作区的任何查询都会根据需要查询这两个群集，以提供单个统一的结果集。 这意味着，所有依赖于工作区（例如工作簿和仪表板）的 Azure Monitor 功能都将根据这两个群集中的数据持续获取完整且统一的结果集。

## <a name="billing"></a>计费
[使用专用群集需要支付相应的费用](logs-dedicated-clusters.md#create-a-dedicated-cluster)。 专用群集每日所需的容量预留为 1000 GB， 且此容量预留将于 2021 年 8 月 4 日减少到 500 GB。 

如果你已有一个专用群集，并选择保留其以访问其中数据，则你需要支付这两个群集的费用。 从 2021 年 8 月 4 日起，专用群集每日所需的最小容量预留将减少到 500GB。因此，建议将该最小值应用到旧群集，以减少支出。

新群集在迁移的第一天并不计费，以避免在配置期间重复计费。 只有在迁移完成前引入的数据仍会在迁移日期计费。 


## <a name="next-steps"></a>后续步骤

- 请参阅[在 Azure Monitor Log Analytics 中使用查询](queries.md)，了解用户如何在 Log Analytics 中与查询包交互。

