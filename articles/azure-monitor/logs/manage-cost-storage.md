---
title: 管理 Azure Monitor 日志的使用情况和成本
description: 了解如何在 Azure Monitor 中更改定价计划和管理 Log Analytics 工作区的数据量与保留策略。
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: bwren
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: febbfc5a1a3381affac50a75a29cb502c7872d69
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129657212"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>使用 Azure Monitor 日志管理使用情况和成本    

> [!NOTE]
> 本文介绍如何了解和控制 Azure Monitor 日志的成本。 [监视使用情况和估算成本](../usage-estimated-costs.md)这一相关文章介绍了如何针对不同的定价模型查看多个 Azure 监视功能的使用情况及估算成本。 本文中的所有价格和成本仅用于举例。 

Azure Monitor 日志用于调整和支持来自任何源的巨量数据的每日收集、索引和存储，这些源部署在企业或 Azure 中。  尽管这可能是组织的主要驱动力，但成本效益最终是基本驱动力。 为此，必须了解 Log Analytics 工作区的成本不仅仅是基于收集的数据量，而且也取决于所选的计划，以及连接源生成的数据的存储时间长短。  

本文介绍如何主动监视引入的数据量和存储增长情况， 以及如何定义限制来控制这些关联的成本。 

## <a name="pricing-model"></a>定价模型

Log Analytics 的默认定价采用即用即付模型，该模型基于数据引入量，有时还基于长期数据保留策略。 数据量是以 GB（10^9 字节）为单位存储的数据大小来度量的。 每个 Log Analytics 工作区作为独立服务计费，并在 Azure 订阅的账单中产生相应费用。 数据引入量可能相当大，具体取决于以下因素： 

  - 已启用的管理解决方案集及其配置
  - 受监视资源的数量和类型
  - 从每个受监视资源收集的数据类型
  
除了即用即付模型外，Log Analytics 还具有承诺层级，与即用即付价格相比，使你能够节省多达 30% 的成本。 采用承诺层级定价时，你能够以低于即用即付定价的价格购买起始量为 100 GB/天的数据引入量。 对于超出承诺级别的任何使用量（超额），将按当前承诺层级提供的每 GB 价格计费。 承诺层级的承诺期为 31 天。 在承诺期，可以更改为更高级别的承诺层级（这将重启 31 天的承诺期），但在承诺期结束之前，无法返回到即用即付或更低的承诺层级。 承诺层级按天计费。 [详细了解](https://azure.microsoft.com/pricing/details/monitor/) Log Analytics 即用即付和承诺层级定价。 

> [!NOTE]
> 从 2021 年 6 月 2 日开始，“容量预留”称作“承诺层级” 。 现在，如果收集的数据量超过承诺层级的级别（超额），将按照与当前承诺层级级别相同的每 GB 价格计费，与采用即用即付费率的旧计费方式相比，这将降低成本，并减少了数据量较大的用户微调其承诺级别的需求。 还添加了三个新的承诺层级：1000、2000 和 5000 GB/天。 

在所有定价层中，事件的数据大小均根据存储在此事件 Log Analytics 中的属性的字符串表示形式进行计算，无论数据是从代理发送的还是在引入过程中添加的。 这包括在收集数据并随后将数据存储在 Log Analytics 中时添加的任何[自定义字段](custom-fields.md)。 一些对于所有数据类型都通用的属性（包括一些 [Log Analytics 标准属性](./log-standard-columns.md)）均未包括在事件大小的计算中。 这包括 `_ResourceId`、`_SubscriptionId`、`_ItemId`、`_IsBillable`、`_BilledSize` 和 `Type`。 存储在 Log Analytics 中的所有其他属性均包括在事件大小的计算中。 某些数据类型完全不收取数据引入费用，例如 [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity)、[检测信号](/azure/azure-monitor/reference/tables/heartbeat)、[使用情况](/azure/azure-monitor/reference/tables/usage)和[操作](/azure/azure-monitor/reference/tables/operation)类型。 一些解决方案具有更多特定于解决方案的有关免费引入数据的策略，例如，[Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate/) 在服务器评估的前 180 天内免费提供依赖项可视化数据。 若要确定某个事件是否不收取数据引入计费，可使用 [_IsBillable](log-standard-columns.md#_isbillable) 属性，[如下](#data-volume-for-specific-events)所示。 以 GB（10^9 字节）为单位报告使用情况。 

另外，有些解决方案（如 [Azure Defender（安全中心）](https://azure.microsoft.com/pricing/details/azure-defender/)、[Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) 和[配置管理](https://azure.microsoft.com/pricing/details/automation/)）有其自己的定价模型。 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics 专用群集

[Log Analytics 专用群集](logs-dedicated-clusters.md)是单个托管 Azure 数据资源管理器群集中的工作区集合，用于支持高级方案，例如[客户管理的密钥](customer-managed-keys.md)。 Log Analytics 专用群集使用与工作区相同的承诺层级定价模型，只不过群集的承诺级别必须至少为 500 GB/天。 群集没有即用即付选项。 在提高承诺级别后，群集承诺层级有 31 天的承诺期。 在承诺期，无法降低承诺层级的级别，但可以随时提高。 当工作区与群集关联时，将使用配置的承诺层级级别在群集级别进行对这些工作区的数据引入计费。 详细了解[创建 Log Analytics 群集](customer-managed-keys.md#create-cluster)并[将工作区与其关联](customer-managed-keys.md#link-workspace-to-cluster)。 有关承诺层级定价的信息，请参阅 [Azure Monitor 定价页]( https://azure.microsoft.com/pricing/details/monitor/)。

群集承诺层级的级别是使用 `Sku` 下的 `Capacity` 参数以编程方式通过 Azure 资源管理器配置的。 以 GB 为单位指定 `Capacity`，值可为 500、1000、2000 或 5000 GB/天。 对于超出承诺级别的任何使用量（超额），将按当前承诺层级提供的每 GB 价格计费。  有关详细信息，请参阅 [Azure Monitor 客户管理的密钥](customer-managed-keys.md)。

对于群集上的使用情况，有两种计费模式。 可以在[创建群集](logs-dedicated-clusters.md#create-a-dedicated-cluster)时通过 `billingType` 参数指定这些计费模式，也可以在创建后设置。 这两种模式是： 

- **群集**：在此情况下（其为默认情况），引入数据的计费在群集级别完成。 每个与群集关联的工作区中的引入数据数量将进行聚合，以计算该群集的每日帐单。 在跨群集中的所有工作区对此聚合数据进行聚合之前，将在工作区级别应用来自 [Azure Defender（安全中心）](../../security-center/index.yml)的按节点分配。 

- 工作区：群集的承诺层级成本按照每个工作区的数据引入量，按比例分配到群集中的工作区（在考虑了为每个工作区从 [Azure Defender（安全中心）](../../security-center/index.yml)进行每节点分配之后）。如果某一天引入到群集中的总数据量低于承诺层级，则每个工作区都按有效的每 GB 承诺层级费率对其引入数据计费（方法是对引入数据按承诺层级的比例进行计费），承诺层级的未使用部分将计费到群集资源。 如果某一天引入到群集中的总数据量高于承诺层级，则每个工作区将基于其当天的引入数据比例按承诺层级的比例进行计费，且每个工作区都将按高于承诺层级的引入数据比例进行计费。 如果某一天引入到工作区中的总数据量超出承诺层级，则不会计费到群集资源。

在群集计费选项中，数据保留针对每个工作区计费。 在创建群集时会开始群集计费，无论工作区是否已关联到群集。 与群集关联的工作区不再有自身的定价层。

## <a name="estimating-the-costs-to-manage-your-environment"></a>估算环境的管理成本 

如果尚未使用 Azure Monitor 日志，可以使用 [Azure Monitor 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=monitor)来估计使用 Log Analytics 的成本。 在“搜索”框中输入“Azure Monitor”，然后选择生成的“Azure Monitor”磁贴。 将页面向下滚动到“Azure Monitor”，然后展开“Log Analytics”部分。 可以在此处输入你希望收集的数据量 (GB)。 如果你已评估 Azure Monitor 日志，则可以使用自己环境中的数据统计信息。 请参阅下文来了解如何确定[受监视 VM 数](#understanding-nodes-sending-data)和[工作区引入的数据量](#understanding-ingested-data-volume)。 如果尚未运行 Log Analytics，可以了解下面有关估算数据量的一些指导：

1. 监视 VM：启用典型监视后，每个受监视 VM 每月引入 1 GB 到 3 GB的数据。 
2. 监视 Azure Kubernetes 服务 (AKS) 群集：[此处](../containers/container-insights-cost.md#estimating-costs-to-monitor-your-aks-cluster)提供了有关监视典型 AKS 群集预期会产生的数据量的详细信息。 请遵循这些[最佳做法](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost)来控制 AKS 群集监视成本。 
3. 应用程序监视：Azure Monitor 定价计算器包括一个数据量估算器，该估算器基于应用程序的使用情况并基于 Application Insights 数据量的统计分析进行估算。 在定价计算器的“Application Insights”部分，切换“基于应用程序活动估算数据量”旁边的开关即可使用此估算器。 

## <a name="understand-your-usage-and-estimate-costs"></a>了解自己的使用情况和估算成本

如果现在正在使用 Azure Monitor 日志，就很容易了解根据最近的使用模式可能会产生什么样的成本。 若要执行此操作，请使用“Log Analytics 使用情况和估算成本”查看和分析数据使用情况。 此项显示每个解决方案收集的数据量、保留的数据量，以及基于引入的数据量和已包含量之外的其他保留量估算的成本。

:::image type="content" source="media/manage-cost-storage/usage-estimated-cost-dashboard-01.png" alt-text="使用情况和预估成本":::

若要浏览更详细的数据，请选择“使用情况和估算成本”页上任一图表右上侧的图标。 现在可以使用此查询来探索有关使用情况的更多详细信息。  

:::image type="content" source="media/manage-cost-storage/logs.png" alt-text="日志视图":::

在“使用情况和估算成本”页面中，可以查看当月的数据量。 这包括 Log Analytics 工作区中收到和保留的所有可计费数据。  
 
Log Analytics 费用将添加到 Azure 帐单。 可以在 Azure 门户的“计费”部分或在 [Azure 计费门户](https://account.windowsazure.com/Subscriptions)中查看 Azure 账单详细信息。  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>查看 Azure 账单上的 Log Analytics 使用情况 

Azure 在 [Azure 成本管理和计费](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json)中心提供了大量实用功能。 例如，可以使用“成本分析”功能查看 Azure 资源支出。 若要跟踪 Log Analytics 支出，可按“资源类型”添加筛选器（对于 Log Analytics，将其添加到 microsoft.operationalinsights/workspace；对于Log Analytics 群集，将其添加到 microsoft.operationalinsights/cluster）。 对于“分组依据”，选择“计量类别”或“计量”  。 Azure Defender（安全中心）和 Azure Sentinel 等其他服务还会根据其对 Log Analytics 工作区资源的使用情况进行计费。 若要查看服务名称映射，可以选择表视图而不是图表。 

若要深入了解使用情况，可以[在 Azure 门户中下载使用情况信息](../../cost-management-billing/understand/download-azure-daily-usage.md)。 在下载的电子表格中，可以查看每个 Azure 资源（例如 Log Analytics 工作区）每天的使用情况。 在此 Excel 电子表格中，你可以先在“计量类别”列进行筛选以显示“Log Analytics”、“Insight and Analytics”（由某些旧定价层使用）和“Azure Monitor”（由承诺层级定价层使用），并据此查找 Log Analytics 工作区的使用情况，然后在“实例 ID”列上添加一个筛选器：“包含工作区”或“包含群集”（后者包括 Log Analytics 群集的使用情况）。 使用情况显示在“已使用数量”列中，每个条目的单位显示在“度量单位”列中。 有关详细信息，请参阅[查看个人的 Azure 订阅帐单](../../cost-management-billing/understand/review-individual-bill.md)。 

## <a name="changing-pricing-tier"></a>更改定价层

若要更改工作区的 Log Analytics 定价层，请执行以下操作：

1. 在 Azure 门户中，从你的工作区打开“使用情况和估算成本”，此时会显示此工作区可用的每个定价层的列表。

2. 查看每个定价层的估算成本。 此估算基于最近 31 天的使用情况，因此，此成本估算依赖于最近 31 天有代表性的典型使用情况。 在以下示例中可以看到，基于过去 31 天的数据模式，与 100 GB/天承诺层级 (#2) 相比，此工作区采用即用即付层 (#1) 时的成本更低。  

:::image type="content" source="media/manage-cost-storage/pricing-tier-estimated-costs.png" alt-text="定价层":::
    
3. 查看基于过去 31 天使用情况的估算成本后，如果决定更改定价层，请选择“选择”。  

### <a name="changing-pricing-tier-via-arm"></a>通过 ARM 更改定价层

还可以[通过 Azure 资源管理器设置定价层](./resource-manager-workspace.md)，方法是使用 `sku` 对象设置定价层和 `capacityReservationLevel` 参数（如果定价层为 `capacityresrvation`）。 （详细了解如何[通过 ARM 设置工作区属性](/azure/templates/microsoft.operationalinsights/2020-08-01/workspaces?tabs=json#workspacesku-object)。）下面是一个示例 Azure 资源管理器模板，用于将工作区设置为 300 GB/天承诺层级（在资源管理器中称为 `capacityreservation`）。 

```
{
  "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "YourWorkspaceName",
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "location": "yourWorkspaceRegion",
      "properties": {
                    "sku": {
                      "name": "capacityreservation",
                      "capacityReservationLevel": 300
                    }
      }
    }
  ]
}
```

若要在 PowerShell 中使用此模板，请在[安装 Azure Az PowerShell 模块](/powershell/azure/install-az-ps)后，使用 `Connect-AzAccount` 登录到 Azure，使用 `Select-AzSubscription -SubscriptionId YourSubscriptionId` 选择包含你的工作区的订阅，然后应用该模板（保存在名为 template.json 的文件中）：

```
New-AzResourceGroupDeployment -ResourceGroupName "YourResourceGroupName" -TemplateFile "template.json"
```

若要将定价层设置为其他值，例如“即用即付”（称为 SKU 的 `pergb2018`），请省略 `capacityReservationLevel` 属性。 详细了解如何[创建 ARM 模板](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)、[将资源添加到模板](../../azure-resource-manager/templates/template-tutorial-add-resource.md)和[应用模板](../resource-manager-samples.md)。 

## <a name="legacy-pricing-tiers"></a>旧版定价层

在 2018 年 4 月 2 日之前已包含 Log Analytics 工作区或 Application Insights 资源的订阅，或者与 2019 年 2 月 1 日之前开始的企业协议链接的订阅，将继续有权使用旧版定价层：“免费试用”、“独立（按 GB）”和“按节点 (OMS)”  。 对于“免费试用”定价层中的工作区，其每日数据引入量限制为 500 MB（由 [Azure Defender（安全中心）](../../security-center/index.yml)收集的安全数据类型除外），并且其数据保留期限制为 7 天。 “免费试用”定价层仅用于评估目的。 没有为免费层提供 SLA。  独立定价层或按节点定价层中的工作区具有用户可配置的 30 至 730 天的保留期。

独立定价层上的使用情况按引入数据量计费。 该使用情况在 Log Analytics 服务中进行报告，计量名为“分析的数据”。 

按节点定价层按小时粒度对每个受监视的 VM（节点）收费。 对于每个受监视的节点，每日会向工作区分配 500 MB 未计费的数据。 此分配按小时粒度计算，并且每天在工作区级别进行汇总。 超出每日数据分配聚合的引入数据在数据超额时按 GB 计费。 如果工作区位于“按节点”定价层，则该服务在账单上为“Insight and Analytics”，用于提供 Log Analytics 使用情况。 使用情况按三个计量进行报告：

- 节点：这是以节点月为单位的受监视节点 (VM) 数量的使用情况。
- 每个节点的数据超额：这是超出聚合数据分配的所引入数据的 GB 数。
- 每个节点包含的数据：这是聚合数据分配所涵盖的引入数据量。 当工作区在所有定价层中时，也使用此计量来显示 Azure Defender（安全中心）涵盖的数据量。

> [!TIP]
> 如果工作区可访问“按节点”定价层，但你想知道在即用即付层中是否成本更低，则可以[使用以下查询](#evaluating-the-legacy-per-node-pricing-tier)来轻松获取建议。 

2016 年 4 月之前创建的工作区可以继续使用“标准”和“高级”定价层（分别有 30 天和 365 天的固定数据保留期）。 无法在“标准”或“高级”定价层中创建新的工作区，并且如果将工作区移出这些层，则无法将其移回 。 Azure 账单上针对这些旧层的数据引入计量称为“分析的数据”。

### <a name="legacy-pricing-tiers-and-azure-defender-security-center"></a>旧版定价层和 Azure Defender（安全中心）

在使用旧 Log Analytics 层和 [Azure Defender（安全中心）](../../security-center/index.yml)的使用情况计费方式之间还有一些行为。 

- 如果工作区位于旧版“标准”或“高级”层中，则仅针对 Log Analytics 数据引入而不是按节点对 Azure Defender 进行计费。
- 如果工作区位于旧版“按节点”层，将使用当前 [Azure Defender 基于节点的定价模型](https://azure.microsoft.com/pricing/details/security-center/)对 Azure Defender 进行计费。 
- 在其他定价层（包括承诺层级）中，如果 Azure Defender 在 2017 年 6 月 19 日之前已启用，将仅针对 Log Analytics 数据引入对 Azure Defender 进行计费。 否则将使用当前 Azure Defender 基于节点的定价模型对 Azure Defender 进行计费。

有关定价层限制的更多详细信息，请参阅 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)。

旧版定价层均没有基于区域的定价。  

> [!NOTE]
> 若要使用通过购买用于 System Center 的 OMS E1 套件、OMS E2 套件或 OMS 附加产品所获得的权利，请选择 Log Analytics 的“按节点”定价层。

## <a name="log-analytics-and-azure-defender-security-center"></a>Log Analytics 和 Azure Defender（安全中心）

[适用于服务器的 Azure Defender（安全中心）](../../security-center/index.yml)计费与 Log Analytics 计费密切相关。 当更新管理解决方案未在工作区上运行或解决方案目标功能已启用时，Azure Defender 会[按受监视服务的数目计费](https://azure.microsoft.com/pricing/details/azure-defender/)并针对下面的一部分[安全数据类型](/azure/azure-monitor/reference/tables/tables-category#security)（WindowsEvent、SecurityAlert、SecurityBaseline、SecurityBaselineSummary、SecurityDetection、SecurityEvent、WindowsFirewall、MaliciousIPCommunication、LinuxAuditLog、SysmonEvent、ProtectionStatus）以及 Update 和 UpdateSummary 数据类型提供 500 MB/服务器/天的数据分配（[了解详细信息](../../security-center/security-center-pricing.md#what-data-types-are-included-in-the-500-mb-data-daily-allowance)）。 受监视服务器的数目按小时粒度计算。 每个受监视服务器的每日数据分配贡献在工作区级别聚合。 如果工作区位于旧版按节点定价层中，则将合并 Azure Defender 和 Log Analytics 分配，并将其共同应用于所有可计费的引入数据。  

## <a name="change-the-data-retention-period"></a>更改数据保留期

以下步骤说明如何配置日志数据在工作区中的保留期限。 对于所有工作区，工作区级别的数据保留期可配置为 30 到 730 天（2 年），除非其使用的是旧版“免费试用”定价层。 单个数据类型的保留期可以设置为低至 4 天。 [详细了解](https://azure.microsoft.com/pricing/details/monitor/)针对更长数据保留期的定价。 若要将数据保留 730 天以上，请考虑使用 [Log Analytics 工作区数据导出](logs-data-export.md)。

### <a name="workspace-level-default-retention"></a>工作区级别的默认保留期

若要设置工作区的默认保留期，请执行以下操作：
 
1. 在 Azure 门户中，在你的工作区的左侧窗格中选择“使用情况和估算成本”。
2. 在“使用情况和估算成本”页面上，选择页面顶部的“数据保留期” 。
3. 在窗格中，移动滑块以增加或减少天数，然后选择“确定”。  如果位于免费层，则无法修改数据保留期，需要升级到付费层才能控制此设置。

:::image type="content" source="media/manage-cost-storage/manage-cost-change-retention-01.png" alt-text="更改工作区数据保留设置":::

如果保留期缩短，则在旧数据（早于新保留期设置的数据）删除之前会有几天宽限期。 

可在“数据保留期”页面中将保留期设置为 30、31、60、90、120、180、270、365、550 和 730 天。 如果需要其他设置，可以使用 `retentionInDays` 参数通过 [Azure 资源管理器](./resource-manager-workspace.md)进行配置。 如果将数据保留期设置为 30 天，则可以使用 `immediatePurgeDataOn30Days` 参数对旧数据触发“立即清除”操作（消除宽限期）。 这对于合规性相关场景可能很有用，在此类场景中，必须立即删除数据。 此立即清除功能仅通过 Azure 资源管理器公开。 

保留期为 30 天的工作区实际上可能会保留 31 天的数据。 如果要求只保留 30 天的数据，请使用 Azure 资源管理器将保留期设置为 30 天，并使用 `immediatePurgeDataOn30Days` 参数。  

默认情况下，两种数据类型（`Usage` 和 `AzureActivity`）将免费保留至少 90 天。 如果工作区的保留期超过 90 天，则这些数据类型的保留期也将增加。 这些数据类型也不收取数据引入费用。 

默认情况下，基于工作区的 Application Insights 资源（`AppAvailabilityResults`、`AppBrowserTimings`、`AppDependencies`、`AppExceptions`、`AppEvents`、`AppMetrics`、`AppPageViews`、`AppPerformanceCounters`、`AppRequests`、`AppSystemEvents` 和 `AppTraces`）中的数据类型也会免费保留 90 天。 可以使用按数据类型分类的保留期功能调整上述保留期。 

Log Analytics [清除 API](/rest/api/loganalytics/workspacepurge/purge) 不会影响保留费用，并且适用于非常有限的情况。 若要减少保留费用，必须为工作区或特定数据类型减少保持期。 

### <a name="retention-by-data-type"></a>按数据类型保留

还可以为单个数据类型指定不同的保留期设置 - 从 4 天到 730 天（旧版“免费试用”定价层中的工作区除外），以替代工作区级别的默认保留期。 每个数据类型都是工作区的子资源。 例如，SecurityEvent 表可以在 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)中寻址，如下所示：

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

请注意，数据类型（表）区分大小写。 若要获取特定数据类型（在此示例中为 SecurityEvent）的当前每数据类型保留期设置，请使用：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> 仅当针对某数据类型显式设置了保留期时，才会返回该数据类型的保留期。 如果数据类型尚未显式设置保留期（因而继承工作区保留期），则此调用将不会返回任何内容。 

若要获取工作区中所有数据类型（已设置每个数据类型保留期）的当前每个数据类型保留期设置，只需省略特定的数据类型，例如：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

若要将特定数据类型（在此示例中为 SecurityEvent）的保留期设置为 730 天，请使用：

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

`retentionInDays` 的有效值为 4 到 730。

`Usage` 和 `AzureActivity` 数据类型不能使用自定义保留期进行设置。 它们使用最大的默认工作区保留期（或 90 天）。 

用于直接连接到 Azure 资源管理器以按数据类型设置保留期的极佳工具是 OSS 工具 [ARMclient](https://github.com/projectkudu/ARMClient)。  请在 [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) 和 Daniel Bowbyes 的文章中详细了解 ARMclient。  以下示例演示如何使用 ARMClient 将 SecurityEvent 数据的保留期设置为 730 天：

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> 设置各个数据类型的保留期可用于降低数据保留成本。 如果数据是从 2019 年 10 月（此时发布了该功能）开始收集的，则缩短某些数据类型的保留期可以降低一段时间的保留成本。 如果数据是更早之前收集的，则为单个类型设置较短的保留期不会影响保留成本。  

## <a name="manage-your-maximum-daily-data-volume"></a>管理每日最大数据量

可以配置工作区的每日上限并限制每日引入量，但请谨慎设置，因为目标是避免达到每日限制。 否则，会丢失该天剩余时间的数据，这可能会影响其功能依赖于工作区中提供的最新数据的其他 Azure 服务和解决方案。 因此，需要具有在支持 IT 服务的资源的运行状况受到影响时监视和接收警报的能力。 每日上限旨在用作一种调控受管理资源数据量意外增长并使其保留在限制范围内，或者限制工作区产生计划外费用的方式。 设置的每日上限不应是工作区每日可达到的量。

每个工作区在一天的不同小时均应用其每日上限。 重置时间显示在“每日上限”页中（见下文）。 无法配置此重置时间。 

达到每日限制后，在当天的剩余时间会停止收集应计费数据类型。 应用每日上限时的固有延迟意味着应用上限不会精确到指定的每日上限级别。 选定 Log Analytics 工作区的页面顶部会显示警告横幅，同时会将一个操作事件发送到“LogManagement”类别下的“操作”表。 在“每日限制设置时间”定义的重置时间过后，数据收集将会恢复。 我们建议基于此操作事件定义一个警报规则，并将其配置为在达到每日数据限制时发出通知。 有关详细信息，请参阅[达到每日上限时发出警报](#alert-when-daily-cap-is-reached)部分。 

> [!NOTE]
> 每日上限无法在精确指定的上限级别停止数据收集，且可能出现某些多余的数据，尤其是在工作区接收大量数据的情况下。 如果收集的数据超过上限，仍会纳入计费。 请参阅本文中的[查看每日上限的效果](#view-the-effect-of-the-daily-cap)部分，了解有助于研究每日上限行为的查询。 

> [!WARNING]
> 每日上限不会导致以下数据类型的收集停止：WindowsEvent、SecurityAlert、SecurityBaseline、SecurityBaselineSummary、SecurityDetection、SecurityEvent、WindowsFirewall、MaliciousIPCommunication、LinuxAuditLog、SysmonEvent、ProtectionStatus、Update 和 UpdateSummary，但 2017 年 6 月 19 日之前已安装 Azure Defender（安全中心）的工作区除外             。 

### <a name="identify-what-daily-data-limit-to-define"></a>确定要定义的每日数据限制

若要了解数据引入趋势，以及要定义的每日数据量上限，请查看 [Log Analytics 使用情况和估算成本](../usage-estimated-costs.md)。 请慎重考虑此上限，因为在达到限制后，将无法监视资源。 

### <a name="set-the-daily-cap"></a>设置每日上限

以下步骤说明如何配置一个限制来管理 Log Analytics 工作区每日引入的数据量。  

1. 在工作区的左侧窗格中，选择“使用情况和估算成本”。
2. 在所选工作区的“使用情况和估算成本”页面上，选择页面顶部的“数据上限” 。 
3. “每日上限”默认设置为“关闭” 。 若要启用，请选择“打开”，然后设置数据量限制（以 GB/天为单位）。

:::image type="content" source="media/manage-cost-storage/set-daily-volume-cap-01.png" alt-text="Log Analytics 配置数据限制":::
    
可以使用 Azure 资源管理器配置每日上限。 若要进行配置，请根据[工作区 - 创建或更新](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping)中所述，在 `WorkspaceCapping` 下设置 `dailyQuotaGb` 参数。 

可以使用此查询跟踪对每日上限所做的更改：

```kusto
_LogOperation | where Operation == "Workspace Configuration" | where Detail contains "Daily quota"
```

详细了解 [_LogOperation](./monitor-workspace.md) 函数。 

### <a name="view-the-effect-of-the-daily-cap"></a>查看每日上限的效果

要查看每日上限的效果，请务必考虑每日上限中未包含的安全数据类型，以及工作区的重置时间。 每日上限重置时间在“每日上限”页上可见。 可使用以下查询跟踪在每日上限重置之间受每日上限限制的数据量。 在此示例中，工作区的重置时间为 14:00。 需要为工作区更新此时间。

```kusto
let DailyCapResetHour=14;
Usage
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(Quantity)/1000. by day=bin(TimeGenerated, 1d) | render areachart  
```

（在使用情况数据类型中，`Quantity` 的单位为 MB。）

### <a name="alert-when-daily-cap-is-reached"></a>达到每日上限时发出警报

在达到数据限制阈值时，Azure 门户中会显示视觉提示，但此行为不一定符合需要立即关注的操作问题的处理方式。 若要接收警报通知，可以在 Azure Monitor 中创建一个新的警报规则。 有关详细信息，请参阅[如何创建、查看和管理警报](../alerts/alerts-metric.md)。

若要开始操作，请参考以下针对使用 `_LogOperation` 函数查询 `Operation` 表的警报的推荐设置（[了解详细信息](./monitor-workspace.md)）。 

- 目标：选择 Log Analytics 资源
- 条件： 
   - 信号名称：自定义日志搜索
   - 搜索查询：`_LogOperation | where Operation =~ "Data collection stopped" | where Detail contains "OverQuota"`
   - 依据：结果数
   - 条件：大于
   - 阈值：0
   - 时间段：5（分钟）
   - 频率：5（分钟）
- 警报规则名称：达到每日数据限制
- 严重性：警告（严重性 1）

定义警报并达到限制后，警报将会触发，并执行操作组中定义的响应。 它可以通过以下方式通知你的团队：

- 电子邮件和短信
- 使用 Webhook 自动执行操作
- Azure 自动化 Runbook
- [与外部 ITSM 解决方案集成](../alerts/itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>排查使用量超出预期的原因

使用量较高是由以下一个或两个原因引起的：
- 将数据发送到 Log Analytics 工作区的节点数超出预期。 有关信息，请参阅本文的[了解发送数据的节点](#understanding-nodes-sending-data)部分。
- 发送到 Log Analytics 工作区的数据量超出预期（可能是由于开始使用新解决方案或对现有解决方案进行配置更改）。 有关信息，请参阅本文的[了解引入的数据量](#understanding-ingested-data-volume)部分。

如果你观察到使用 `Usage` 记录报告了较高的数据引入量（参阅[按解决方案统计的数据量](#data-volume-by-solution)部分），但在直接根据[数据类型](#data-volume-for-specific-events)对 `_BilledSize` 求和后未观察到相同的结果，则可能是数据延迟到达的情况较为严重。 有关如何诊断此问题的信息，请参阅本文的[延迟到达的数据](#late-arriving-data)部分。 

### <a name="log-analytics-workspace-insights"></a>Log Analytics 工作区见解

在 [Log Analytics 工作区见解工作簿](log-analytics-workspace-insights-overview.md)的“使用情况”选项卡中开始了解你的数据量。 在“使用情况”仪表板上，可以轻松查看：
- 哪些数据表在主表中引入的数据量最大，  
- 哪些资源贡献的数据最多，以及 
- 数据引入趋势是什么。

可以透视到“其他查询”，以轻松执行其他可用于了解数据模式的查询。 

详细了解[“使用情况”选项卡的功能](log-analytics-workspace-insights-overview.md#usage-tab)。 

尽管此工作簿无需运行查询就能解答许多问题，但若要解答更具体的问题或执行更深入的分析，可以借助接下来两个部分中的查询实现这些目的。 

## <a name="understanding-nodes-sending-data"></a>了解发送数据的节点

若要了解上个月每天通过代理报告检测信号的节点数，请使用以下查询：

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
要获取最近 24 小时内发送数据的节点计数，请使用以下查询： 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

若要获取发送任何数据的节点列表（以及每个节点发送的数据量），请使用以下查询：

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>按旧版按节点定价层计费的节点

[旧版“按节点”定价层](#legacy-pricing-tiers)针对具有小时粒度的节点计费，并且不会将仅发送一组安全数据类型的节点计算在内。 其每日节点计数将接近于以下查询：

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

账单上的单位数以节点月为单位，在查询中用 `billableNodeMonthsPerDay` 表示。 如果工作区安装了更新管理解决方案，请将 Update 和 UpdateSummary 数据类型添加到上述查询的 where 子句中的列表中 。 最后，当使用解决方案目标设定时，实际的计费算法中会存在一些其他复杂性，而上述查询中并未体现这种复杂性。 


> [!TIP]
> 请谨慎使用这些 `find` 查询，因为跨数据类型执行扫描[很耗费资源](./query-optimization.md#query-performance-pane)。 如果不需要按计算机获得结果，请按“使用情况”数据类型查询（参阅下文） 。

## <a name="understanding-ingested-data-volume"></a>了解引入的数据量

在“使用情况和预估成本”页上，“单个解决方案的数据引入”图表显示发送的总数据量以及每个解决方案发送的量。 可以确定趋势，例如总数据使用量（或特定解决方案的使用量）是正在增长、保持平稳还是正在下降。 

### <a name="data-volume-for-specific-events"></a>特定事件的数据量

若要查看一组特定事件的引入数据的大小，可以查询特定的表（在此示例中为 `Event`），然后将查询限制为相关事件（在此示例中，事件 ID 为 5145 或 5156）：

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

请注意，子句 `where _IsBillable = true` 从某些解决方案中筛选掉没有引入费用的数据类型。 [详细了解](./log-standard-columns.md#_isbillable) `_IsBillable`。

### <a name="data-volume-by-solution"></a>按解决方案统计的数据量

使用[使用情况](/azure/azure-monitor/reference/tables/usage)数据类型可构建用于查看上个月（最后一部分日期除外）的按解决方案统计的应计费数据量，如下所示：

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

带有 `TimeGenerated` 的子句仅用于确保 Azure 门户中的查询体验在超出默认的 24 小时后会回看。 使用“使用情况”数据类型时，`StartTime` 和 `EndTime` 表示用于显示结果的时间桶。 

### <a name="data-volume-by-type"></a>按类型的数据量

可以进一步钻取，按数据类型查看数据趋势：

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

或者按解决方案和类型查看上个月的表，

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000 by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>按计算机的数据量

“使用情况”数据类型不包括计算机级别的信息。 若要按计算机查看引入的可计费数据的大小，请使用 _BilledSize [属性](./log-standard-columns.md#_billedsize)（以字节为单位提供大小） ：

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer, Type
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes desc nulls last
```

_IsBillable [属性](./log-standard-columns.md#_isbillable)指定引入的数据是否会导致收费。 省略了“使用情况”类型，因为这仅用于分析数据趋势。 

若要查看每台计算机引入的计费事件数，请使用： 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount desc nulls last
```

> [!TIP]
> 请谨慎使用这些 `find` 查询，因为跨数据类型执行扫描[很耗费资源](./query-optimization.md#query-performance-pane)。 如果不需要按计算机获得结果，请按“使用情况”数据类型查询 。

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>按 Azure 资源、资源组或订阅的数据量

对于 Azure 中托管的节点的数据，可以使用 [_ResourceId 属性](./log-standard-columns.md#_resourceid)（提供资源的完整路径）按计算机获取引入的数据的大小：

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

对于 Azure 中托管的节点的数据，可以使用 _SubscriptionId 属性按 Azure 订阅获取引入的数据的大小：

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, _SubscriptionId
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _SubscriptionId | sort by BillableDataBytes nulls last
```

若要按资源组获取数据量，可以分析 _ResourceId：

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

如果需要，还可以更全面地分析 _ResourceId：

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> 请谨慎使用这些 `find` 查询，因为跨数据类型执行扫描[很耗费资源](./query-optimization.md#query-performance-pane)。 如果不需要按订阅、资源组或资源名称获得结果，请按“使用情况”数据类型查询。

> [!WARNING]
> “使用情况”数据类型的某些字段虽然仍在架构中，但已弃用，其值不再填充。 这些是“计算机”以及与引入相关的字段（TotalBatches、BatchesWithinSla、BatchesOutsideSla、BatchesCapped 和 AverageProcessingTimeMs）     。

## <a name="late-arriving-data"></a>延迟到达的数据   

通过旧时间戳引入数据时会出现问题。 例如，如果代理由于连接问题而无法与 Log Analytics 通信，或者主机的时间日期/时间错误。 此问题的表现形式是，按“使用情况”数据类型报告的引入数据，与某个查询针对 TimeGenerated（生成事件时的时间戳）指定的特定日期的原始数据的 _BilledSize 求和后报告的引入数据之间存在明显的偏差  。

若要诊断数据延迟到达的问题，请使用 _TimeReceived 列（[了解详细信息](./log-standard-columns.md#_timereceived)）和 TimeGenerated 列 。 _TimeReceived 是 Azure 云中的 Azure Monitor 引入点收到记录的时间。 例如，在使用“使用情况”记录时，你观察到 2021 年 5 月 2 日的 W3CIISLog 数据引入量较高，这种情况下，可以使用以下查询识别此引入数据的时间戳 ： 

```Kusto
W3CIISLog
| where TimeGenerated > datetime(1970-01-01)
| where _TimeReceived >= datetime(2021-05-02) and _TimeReceived < datetime(2021-05-03) 
| where _IsBillable == true
| summarize BillableDataMB = sum(_BilledSize)/1.E6 by bin(TimeGenerated, 1d)
| sort by TimeGenerated asc 
```

指定 `where TimeGenerated > datetime(1970-01-01)` 语句的目的只是为了在 Log Analytics 用户界面中提供用于分析所有数据的提示。  

## <a name="querying-for-common-data-types"></a>针对常见数据类型进行查询

若要更深入地了解特定数据类型的数据源，请使用下面这些有用的示例查询：

+ 基于工作区的 Application Insights 资源
  - 有关详细信息，请参阅[管理 Application Insights 的使用情况和成本](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)
+ “安全”解决方案
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ “日志管理”解决方案
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ “性能”数据类型
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ “事件”数据类型
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ “Syslog”数据类型
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ AzureDiagnostics 数据类型
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

## <a name="tips-for-reducing-data-volume"></a>有关如何减少数据量的提示

此表列出了有关如何减少收集的日志量的一些建议。

| 高数据量来源 | 如何减少数据量 |
| -------------------------- | ------------------------- |
| 数据收集规则      | [Azure Monitor 代理](../agents/azure-monitor-agent-overview.md)使用数据收集规则来管理数据收集。 您可以使用自定义 XPath 查询[限制数据收集](../agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)。 | 
| 容器见解         | [配置容器见解](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost)，仅收集你需要的数据。 |
| Azure Sentinel | 查看你最近启用的作为附加数据量源的 [Sentinel 数据源](../../sentinel/connect-data-sources.md)。 [详细了解](../../sentinel/azure-sentinel-billing.md) Sentinel 成本和账单。 |
| 安全性事件            | 选择[通用或最低安全性事件](../../security-center/security-center-enable-data-collection.md#data-collection-tier)。 <br> 更改安全审核策略，只收集所需事件。 具体而言，请查看是否需要收集以下对象的事件： <br> - [审核筛选平台](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10))。 <br> - [审核注册表](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))。 <br> - [审核文件系统](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))。 <br> - [审核内核对象](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))。 <br> - [审核句柄操作](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))。 <br> - 审核可移动存储。 |
| 性能计数器       | 更改[性能计数器配置](../agents/data-sources-performance-counters.md)可以： <br> - 降低收集频率。 <br> - 减少性能计数器数。 |
| 事件日志                 | 更改[事件日志配置](../agents/data-sources-windows-events.md)可以： <br> - 减少收集的事件日志数。 <br> - 仅收集必需的事件级别。 例如，不收集“信息”级别事件。 |
| Syslog                     | 更改 [syslog 配置](../agents/data-sources-syslog.md)可以： <br> - 减少收集的设施数。 <br> - 仅收集必需的事件级别。 例如，不收集“信息”和“调试”级别事件 。 |
| AzureDiagnostics           | 更改[资源日志收集](../essentials/diagnostic-settings.md#create-in-azure-portal)可以： <br> - 减少向 Log Analytics 发送日志的资源数。 <br> - 仅收集所需的日志。 |
| 不需解决方案的计算机中的解决方案数据 | 使用[解决方案目标](../insights/solution-targeting.md)，只从必需的计算机组收集数据。 |
| Application Insights | 查看用于[管理 Application Insights 数据量](../app/pricing.md#managing-your-data-volume)的选项。 |
| [SQL Analytics](../insights/azure-sql.md) | 使用 [Set-AzSqlServerAudit](/powershell/module/az.sql/set-azsqlserveraudit) 优化审核设置。 |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>获取按节点定价层中的计费节点

若要获取将按节点计费的计算机的列表（如果工作区位于旧的“按节点”定价层中），请查找要发送“计费数据类型”（某些数据类型免费）的节点。 为此，请使用 [_IsBillable 属性](./log-standard-columns.md#_isbillable)，并使用完全限定域名最左边的字段。 这将返回每小时（这是对节点进行计数和计费的粒度）具有计费数据的计算机数：

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>获取安全和自动化节点计数

若要查看不同安全节点的数目，可以使用以下查询：

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

若要查看不同自动化节点的数目，请使用以下查询：

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>评估旧版按节点定价层

有权访问旧版“按节点”定价层的工作区是就在该层更好，还是在“即用即付”层或在“承诺层级”更好，客户通常很难进行评估  。  这涉及了解按节点定价层中每个受监视节点的固定成本与其包括的每天每个节点 500 MB 数据分配之间的权衡，以及只需支付即用即付（每 GB）层中的引入数据的成本。 

为了促进此评估，可以使用以下查询根据工作区的使用情况模式提出最佳定价层的建议。 此查询将查看过去 7 天内受监视的节点和引入到工作区的数据，并且对每天进行评估了解哪个定价层为最佳。 若要使用查询，需要指定：

- 工作区是否使用 Azure Defender（安全中心），方法是将 workspaceHasSecurityCenter 设置为 true 或 false  。 
- 更新价格（如果采用特定的折扣）。
- 通过设置 daysToEvaluate 指定要回看和分析的天数。 如果查询尝试查看 7 天数据的时间过长，这就很有用。 

以下是定价层建议查询：

```kusto
// Set these parameters before running query
// Pricing details available at https://azure.microsoft.com/pricing/details/monitor/
let daysToEvaluate = 7; // Enter number of previous days to analyze (reduce if the query is taking too long)
let workspaceHasSecurityCenter = false;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let CommitmentTier100Price = 196.; // Enter your price for the 100 GB/day commitment tier
let CommitmentTier200Price = 368.; // Enter your price for the 200 GB/day commitment tier
let CommitmentTier300Price = 540.; // Enter your price for the 300 GB/day commitment tier
let CommitmentTier400Price = 704.; // Enter your price for the 400 GB/day commitment tier
let CommitmentTier500Price = 865.; // Enter your price for the 500 GB/day commitment tier
let CommitmentTier1000Price = 1700.; // Enter your price for the 1000 GB/day commitment tier
let CommitmentTier2000Price = 3320.; // Enter your price for the 2000 GB/day commitment tier
let CommitmentTier5000Price = 8050.; // Enter your price for the 5000 GB/day commitment tier
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend billableGB = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)), DataGB )
| extend PerGBDailyCost = billableGB * PerGBPrice
| extend CommitmentTier100DailyCost = CommitmentTier100Price + max_of(billableGB - 100, 0.)* CommitmentTier100Price/100.
| extend CommitmentTier200DailyCost = CommitmentTier200Price + max_of(billableGB - 200, 0.)* CommitmentTier200Price/200.
| extend CommitmentTier300DailyCost = CommitmentTier300Price + max_of(billableGB - 300, 0.)* CommitmentTier300Price/300.
| extend CommitmentTier400DailyCost = CommitmentTier400Price + max_of(billableGB - 400, 0.)* CommitmentTier400Price/400.
| extend CommitmentTier500DailyCost = CommitmentTier500Price + max_of(billableGB - 500, 0.)* CommitmentTier500Price/500.
| extend CommitmentTier1000DailyCost = CommitmentTier1000Price + max_of(billableGB - 1000, 0.)* CommitmentTier1000Price/1000.
| extend CommitmentTier2000DailyCost = CommitmentTier2000Price + max_of(billableGB - 2000, 0.)* CommitmentTier2000Price/2000.
| extend CommitmentTier5000DailyCost = CommitmentTier5000Price + max_of(billableGB - 5000, 0.)* CommitmentTier5000Price/5000.
| extend MinCost = min_of(
    PerNodeDailyCost,PerGBDailyCost,CommitmentTier100DailyCost,CommitmentTier200DailyCost,
    CommitmentTier300DailyCost, CommitmentTier400DailyCost, CommitmentTier500DailyCost, CommitmentTier1000DailyCost, CommitmentTier2000DailyCost, CommitmentTier5000DailyCost)
| extend Recommendation = case(
    MinCost == PerNodeDailyCost, "Per node tier",
    MinCost == PerGBDailyCost, "Pay-as-you-go tier",
    MinCost == CommitmentTier100DailyCost, "Commitment tier (100 GB/day)",
    MinCost == CommitmentTier200DailyCost, "Commitment tier (200 GB/day)",
    MinCost == CommitmentTier300DailyCost, "Commitment tier (300 GB/day)",
    MinCost == CommitmentTier400DailyCost, "Commitment tier (400 GB/day)",
    MinCost == CommitmentTier500DailyCost, "Commitment tier (500 GB/day)",
    MinCost == CommitmentTier1000DailyCost, "Commitment tier (1000 GB/day)",
    MinCost == CommitmentTier2000DailyCost, "Commitment tier (2000 GB/day)",
    MinCost == CommitmentTier5000DailyCost, "Commitment tier (5000 GB/day)",
    "Error"
)
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, 
    CommitmentTier100DailyCost, CommitmentTier200DailyCost, CommitmentTier300DailyCost, CommitmentTier400DailyCost, CommitmentTier500DailyCost, CommitmentTier1000DailyCost, CommitmentTier2000DailyCost, CommitmentTier5000DailyCost, Recommendation 
| sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

此查询不是对如何计算使用情况的精确复制，而是在大多数情况下用于提供定价层建议。  

> [!NOTE]
> 若要使用通过购买用于 System Center 的 OMS E1 套件、OMS E2 套件或 OMS 附加产品所获得的权利，请选择 Log Analytics 的“按节点”定价层。

## <a name="create-an-alert-when-data-collection-is-high"></a>当数据收集量很高时创建警报

本部分介绍如何使用 Azure Monitor [日志警报](../alerts/alerts-unified-log.md)创建过去 24 小时内数据量超过指定数量的警报。 

若要在过去 24 小时内引入的可计费数据量大于 50 GB 时发出警报，请执行以下操作： 

- **定义警报条件** 将 Log Analytics 工作区指定为资源目标。
- **警报条件** 指定下列项：
   - **信号名称** 选择“自定义日志搜索”
   - 搜索对 `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50` 的查询。  
   - 警报逻辑基于结果数，条件大于阈值 0 
   - “时间段”为 1440 分钟，并将“警报频率”设为每 1440 分钟，以便每天运行一次。
- **定义警报详细信息** 指定以下项：
   - 将“名称”设置为“24 小时内的可计费数据量大于 50 GB”
   - 将“严重性”设置为“警告”

若要在日志警报匹配条件时收到通知，请指定现有的或创建新的[操作组](../alerts/action-groups.md)。

收到警报后，请使用上述部分中有关如何排查使用量超出预期的原因的步骤。

## <a name="data-transfer-charges-using-log-analytics"></a>使用 Log Analytics 的数据传输费用

向 Log Analytics 发送数据可能会产生数据带宽费用。 但这仅限于安装了 Log Analytics 代理的虚拟机，且不适用使用诊断设置或使用 Azure Sentinel 中其他内置连接器的情况。 如 [Azure 带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)中所述，在两个区域中的 Azure 服务之间传输数据将按正常费率收取出站数据传输费。 入站数据传输是免费的。 但是，相比 Log Analytics 日志数据引入费，此传输费很低。 因此，控制 Log Analytics 的成本需要专注于[引入的数据量](#understanding-ingested-data-volume)。 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>排查 Log Analytics 不再收集数据的原因

如果采用的是旧版免费定价层并且某天已发送的数据超过 500 MB，则该天的剩余时间会停止数据收集。 达到每日限制是 Log Analytics 停止数据收集或者看起来缺少数据的常见原因。 在数据收集启动和停止时，Log Analytics 会创建一个“操作”类型的事件。 在搜索中运行以下查询来检查是否已达到每日限制并缺少数据： 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

当数据收集停止时，**OperationStatus** 为 **Warning**。 当数据收集启动时，**OperationStatus** 为 **Succeeded**。 下表列出了数据收集停止的原因以及用于恢复数据收集的建议操作。

|停止收集的原因| 解决方案| 
|-----------------------|---------|
|达到了工作区的每日上限|等到收集自动重启，或者根据“管理每日最大数据量”中所述提高每日数据量限制。 每日上限重置时间显示在“每日上限”页上。 |
| 你的工作区已达到[数据引入量速率](../service-limits.md#log-analytics-workspaces) | 在每个工作区中，使用诊断设置从 Azure 资源发送的数据的默认引入量速率上限约为每分钟 6 GB。 这是一个近似值，因为实际大小在数据类型之间可能会有所不同，具体取决于日志长度及其压缩率。 此限制不适用于从代理或“数据收集器”API 发送的数据。 如果以更高速率将数据发送到单个工作区，则某些数据将丢弃，并且在继续超过阈值的情况下，每 6 小时将向工作区中的“操作”表发送一个事件。 如果引入量继续超过速率限制，或者可能很快达到该限制，则可以通过向 LAIngestionRate@microsoft.com 发送电子邮件或提交支持请求来请求提高工作区的限制。 可通过查询 `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"` 来查找指示数据引入速率限制的事件。 |
|达到旧版免费定价层的每日限制 |等到下一天收集自动重启，或者更改为付费定价层。|
|Azure 订阅由于以下原因处于挂起状态：<br> 免费试用已结束<br> Azure 许可已过期<br> 已达到每月支出限制（例如，在 MSDN 或 Visual Studio 订阅上）|转换为付费订阅<br> 删除限制，或者等到限制重置|

若要在数据收集停止时收到通知，请使用[达到每日上限时发出警报](#alert-when-daily-cap-is-reached)部分中所述的步骤。 若要为警报规则配置电子邮件、Webhook 或 Runbook 操作，请使用[创建操作组](../alerts/action-groups.md)中所述的步骤。 

## <a name="limits-summary"></a>限制摘要

还存在其他 Log Analytics 限制，其中一些限制取决于 Log Analytics 定价层。 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)中记录了这些限制。


## <a name="next-steps"></a>后续步骤

- 若要了解如何使用搜索语言，请参阅 [Azure Monitor 日志中的日志搜索](../logs/log-query-overview.md)。 可以使用搜索查询，对使用情况数据执行其他分析。
- 执行[创建新的日志警报](../alerts/alerts-metric.md)中介绍的步骤，当满足搜索条件时，系统就会通知你。
- 使用[解决方案目标](../insights/solution-targeting.md)，只从必需的计算机组收集数据。
- 若要配置有效的事件收集策略，请参阅 [Azure Defender（安全中心）筛选策略](../../security-center/security-center-enable-data-collection.md)。
- 更改[性能计数器配置](../agents/data-sources-performance-counters.md)。
- 若要修改事件收集设置，请参阅[事件日志配置](../agents/data-sources-windows-events.md)。
- 若要修改 syslog 收集设置，请参阅 [syslog 配置](../agents/data-sources-syslog.md)。
- 若要修改 syslog 收集设置，请参阅 [syslog 配置](../agents/data-sources-syslog.md)。
