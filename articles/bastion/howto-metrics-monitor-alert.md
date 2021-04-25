---
title: 使用 Azure Monitor 配置监视和度量值
titleSuffix: Azure Bastion
description: 了解使用 Azure Monitor（Azure 中指标、警报和诊断日志的解决方案）的 Azure Bastion 监视和指标。
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: c4e03318fae8d8d3a8b4d29538cad49f9ef39593
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259109"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>如何使用 Azure Monitor 配置 Azure Bastion 的监视和指标

本文可帮助你使用 Azure Monitor 配置 Azure Bastion 的监视和指标。

>[!NOTE]
>建议不要使用 **经典指标**。
>

## <a name="about-metrics"></a>关于指标

Azure Bastion 有多种可用指标。 下表显示了每个可用指标的类别和维度。

|**指标**|**类别**|**维度**|
| --- | --- | --- |
|Bastion 通信状态**|[可用性](#availability)|不适用|
|总内存量|[可用性](#availability)|实例|
|已用 CPU|[交通](#traffic)|实例
|已用内存|[交通](#traffic)|实例
|会话计数|[“性能”](#performance)|实例|

**Bastion 通信状态仅适用于 2020 年 11 月后部署的堡垒主机。

### <a name="availability-metrics"></a><a name="availability"></a>可用性指标

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>Bastion 通信状态

可以查看 Azure Bastion 的通信状态，状态在组成堡垒主机的所有实例之间聚合。

* 值为 1 表示堡垒可用。
* 值为 0 表示堡垒服务不可用。

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="显示通信状态的屏幕截图。":::

#### <a name="total-memory"></a><a name="total-memory"></a>内存总量

可以查看 Azure Bastion 的总内存，按每个堡垒实例进行拆分。

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="显示总内存的屏幕截图。":::

### <a name="traffic-metrics"></a><a name="traffic"></a>流量指标

#### <a name="used-cpu"></a><a name="used-cpu"></a>已用 CPU

可以查看 Azure Bastion 的 CPU，按每个堡垒实例进行拆分。 监视此指标将有助于测量组成 Azure Bastion 的实例的可用性和容量

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="显示已用 CPU 的屏幕截图。":::

#### <a name="used-memory"></a><a name="used-memory"></a>已用内存

可以查看每个堡垒实例的内存使用率，按每个堡垒实例进行拆分。 监视此指标将有助于度量组成 Azure Bastion 的实例的可用性和容量。

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="显示已用内存的屏幕截图。":::

### <a name="performance-metrics"></a><a name="performance"></a>性能指标

#### <a name="session-count"></a>会话计数

可以查看每个堡垒实例的活动会话计数，按每个会话类型（RDP 和 SSH）聚合。 每个 Azure Bastion 都可支持一系列的活动 RDP 和 SSH 会话。 监视此指标将帮助你了解是否需要调整运行堡垒服务的实例数。 有关 Azure Bastion 可支持的会话计数的详细信息，请参阅 [Azure Bastion 常见问题解答](bastion-faq.md)。

此指标配置的建议值如下：

* **聚合**：平均值
* **粒度**：5 或 15 分钟
* 建议按实例拆分以获取更准确的计数

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="显示会话计数的屏幕截图。":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>如何查看指标

1. 若要查看指标，请导航到堡垒主机。
1. 从“监视”列表中选择“指标” 。
1. 选择参数。 如果未设置指标，请单击“添加度量值”，然后选择参数。

   * **作用域：** 默认情况下，作用域设置为堡垒主机。
   * **指标命名空间：** 标准指标。
   * **指标：** 选择要查看的指标。

1. 选择指标后，将应用默认聚合。 （可选）可以应用拆分，它将显示具有不同维度的指标。

## <a name="next-steps"></a>后续步骤

阅读 [Bastion 常见问题解答](bastion-faq.md)。
  
