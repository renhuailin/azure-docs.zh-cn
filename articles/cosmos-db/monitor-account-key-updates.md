---
title: 监视 Azure Cosmos DB 帐户的密钥更新和密钥重新生成
description: 使用“帐户密钥已更新”指标监视帐户的密钥更新。 此指标显示帐户的主密钥和辅助密钥更新次数以及密钥更改时间。
ms.service: cosmos-db
ms.topic: how-to
ms.author: sngun
author: SnehaGunda
ms.date: 09/16/2021
ms.openlocfilehash: fe344bdfbd9d4c88fd5cdf4024052ac122cf0e5d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594286"
---
# <a name="monitor-your-azure-cosmos-db-account-for-key-updates-and-key-regeneration"></a>监视 Azure Cosmos DB 帐户的密钥更新和密钥重新生成

适用于 Azure Cosmos DB 的 Azure Monitor 提供指标、警报和日志用于监视帐户。 你可以创建仪表板并根据要求对其进行自定义。 默认会收集 Azure Cosmos DB 指标，因此你无需显式启用或配置任何设置。 若要监视帐户的密钥更新，请使用“帐户密钥已更新”指标。 此指标显示帐户的主密钥和辅助密钥更新次数以及密钥更改时间。 还可以设置警报，以便在密钥更新时收到通知。

## <a name="monitor-key-updates-with-metrics"></a>使用指标监视密钥更新

1. 登录到 [Azure 门户](https://portal.azure.com/)

1. 从左侧导航栏中选择“监视”，然后选择“指标” 。

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Azure Monitor 中的“指标”窗格" border="true":::

1. 从“指标”窗格中，选择要查看其指标的资源范围。

   1. 首先选择所需的订阅，为“资源类型”字段选择“Azure Cosmos DB 帐户”  。 此时将显示 Azure Cosmos DB 帐户所在的资源组的列表。

   1. 选择一个资源组，然后选择现有的 Azure Cosmos 帐户之一。 选择“应用”。

   :::image type="content" source="./media/monitor-account-key-updates/select-account-scope.png" alt-text="选择要查看指标的帐户范围" border="true":::

1. 对于“指标”字段，请选择“帐户密钥已更新”指标 。 将“聚合”字段保留默认值“计数” 。 此视图将显示所选帐户的主密钥和辅助密钥的更新总次数。 还可以在图形中选择时间线，并查看更新密钥的日期和时间。

1. 若要进一步查看更改了哪个密钥，请选择“应用拆分”选项。 选择“密钥类型”并设置“限制”和“排序”属性  。 图形现在会按主密钥和辅助密钥更新进行拆分，如下图所示：

   :::image type="content" source="./media/monitor-account-key-updates/account-keys-updated-metric-chart.png" alt-text="更新主密钥和辅助密钥时的指标图表" border="true" lightbox="./media/monitor-account-key-updates/account-keys-updated-metric-chart.png":::

## <a name="configure-alerts-for-a-key-update"></a>针对密钥更新配置警报

你可能想要监视帐户的密钥更新。 轮换或更新密钥后，需要更新相关的客户端应用程序才能使其可以继续正常工作。 通过配置警报，可以在密钥更新时收到通知。

参考[创建警报](create-alerts.md)文章中的说明，但需要做出一些更改。 选择警报条件时，请选择“帐户密钥已更新”信号。 选择“密钥类型”维度，然后选择“主”或“辅助”密钥  。 根据选择的密钥类型，更新密钥时会触发警报。

以下屏幕截图显示了如何配置在帐户密钥更新时触发的关键类型的警报：

:::image type="content" source="./media/monitor-account-key-updates/configure-key-update-alert.png" alt-text="配置警报以便在帐户密钥更新时收到电子邮件通知":::

## <a name="next-steps"></a>后续步骤

* 使用 Azure 中的[诊断设置](cosmosdb-monitor-resource-logs.md)监视 Azure Cosmos DB 数据。
* [审核 Azure Cosmos DB 控制平面操作](audit-control-plane-logs.md)