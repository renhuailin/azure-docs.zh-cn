---
title: 在 VM 见解来宾运行状况中配置监视（预览）
description: 介绍如何使用 Azure 门户修改 VM 见解来宾运行状况（预览版）的默认监视。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/14/2020
ms.openlocfilehash: 6bda6c9e5f6e23e9e15c12fd507645fc72159302
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583439"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-preview"></a>在 VM 见解来宾运行状况中配置监视（预览）
通过利用 VM 见解来宾运行状况，可以查看虚拟机的运行状况，该运行状况是由一组定期取样的性能测量值定义的。 本文介绍了如何使用 Azure 门户修改默认监视。 还介绍了[使用数据收集规则配置监视](vminsights-health-configure-dcr.md)所需的监视器的基本概念。

## <a name="open-monitor-configuration"></a>打开监视器配置
通过选择监视器，然后选择“配置”选项卡，在 Azure 门户中打开监视器配置。

[![监视器详细信息配置](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>启用或禁用监视器
单元监视器和聚合监视器都具有“运行状况监视器状态”设置，允许启用或禁用监视器。 监视器启用后，其运行状况将显示，并可用于设置 VM 的运行状况。 监视器禁用后，其运行状况将不会计算，且不会用于设置 VM 的运行状况。

| 设置 | 说明 |
|:---|:---|
| Enabled | 无论父级设置如何，都将启用监视器。 |
| 已禁用 | 无论父级设置如何，都将禁用监视器。 |
| 与父级相同 | 监视器将参照父级设置进行启用或禁用。 |

监视器禁用后，任何条件都将显示为不可用，如下面的示例中所示。

![禁用的监视器](media/vminsights-health-configure/disabled-monitor.png)


> [!NOTE]
> 如果父级监视器处于禁用状态，则任何子级监视器也将禁用。 如果显式启用子级监视器，则父级监视器也将一并启用，但其配置状态将保持不变。 在这种情况下，父级监视器中将收到以下消息。
>
> 监视器的配置状态是“已禁用”，但“运行状况”的状态与此有出入。这是因为沿用了已配置的更改，或者任一子级监视器已显式启用。

## <a name="enable-or-disable-virtual-machine"></a>启用或禁用虚拟机
可以禁用 VM 监视，以暂时停止所有监视器。 例如，在对 VM 执行维护时，可以禁用监视。

| 设置 | 说明 |
|:---|:---|
| Enabled  | 显示计算机的运行状况状态。 |
| 已禁用 | 计算机的运行状况状态显示为“已禁用”。 不会创建警报。 |

## <a name="health-state-logic"></a>运行状况状态逻辑
单元监视器的运行状况状态逻辑定义了设置其运行状况状态的条件。 可以指定一个监视器可以有多少种运行状况状态，以及每种运行状况状态计算的阈值。

![示例运行状况条件](media/vminsights-health-configure/sample-health-criteria.png)

聚合监视器不指定任何运行状况状态逻辑。 其运行状况状态由下级的单元监视器根据其运行状况汇总情况进行设置。

每个单元监视器都有两到三种运行状况状态。 每个一定有“正常”状态，另外可以选择“警告”状态和/或“严重”状态。 “警告”和“严重”状态都需要唯一条件，用于定义何时将监视器设置为此状态。 “正常”状态没有条件，因为只要不满足其他状态的条件，就会设置此状态。

在下面的示例中，CPU 使用率设置为以下运行状况状态：

- 超过 90% 时，为“严重”。
- 大于或等于 80% 时，为“警告”。
- 低于 80% 时，为“正常”。 这是隐式条件，因为这是其他两种条件均不适用时的条件。

## <a name="next-steps"></a>后续步骤

- [使用数据收集规则大规模配置监视器。](vminsights-health-configure-dcr.md)
