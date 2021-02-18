---
title: 在用于 VM 的 Azure Monitor 来宾运行状况中配置监视（预览）
description: 介绍如何使用 Azure 门户修改用于 VM 的 Azure Monitor 来宾健康 (预览版) 的默认监视。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/14/2020
ms.openlocfilehash: 427bdec2b5e5ab14d566375d5ad8f9da9dc3e81b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100608830"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-preview"></a>在用于 VM 的 Azure Monitor 来宾运行状况中配置监视（预览）
用于 VM 的 Azure Monitor 来宾健康状况允许你查看按固定时间间隔采样的一组性能度量定义的虚拟机的运行状况。 本文介绍如何使用 Azure 门户修改默认监视。 还介绍了 [使用数据收集规则配置监视](vminsights-health-configure-dcr.md)所需的监视器的基本概念。

## <a name="open-monitor-configuration"></a>打开监视器配置
通过选择监视器，然后选择 " **配置** " 选项卡，打开 "监视配置" bin Azure 门户。

[![监视详细信息配置](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>启用或禁用监视器
单元监视器和聚合监视器都具有 **运行状况监视器状态** 设置，该设置允许您启用或禁用监视器。 启用监视器后，将显示其运行状况，并将其用于设置 VM 的运行状况。 禁用监视器后，不会计算其运行状况，并且不会用于设置 VM 的运行状况。

| 设置 | 说明 |
|:---|:---|
| 已启用 | 无论其父项的设置如何，都将启用该监视器。 |
| 已禁用 | 无论其父项的设置如何，都会禁用该监视器。 |
| 与父项相同 | 监视器将启用或禁用，具体取决于其父项的设置。 |

禁用监视器后，任何条件将显示为 "不可用"，如下面的示例中所示。

![禁用的监视器](media/vminsights-health-configure/disabled-monitor.png)


> [!NOTE]
> 如果父监视器处于禁用状态，则任何子监视器也会被禁用。 如果显式启用子监视器，则还会启用父级，但其配置状态将保持不变。 在这种情况下，你将在父监视器中收到以下消息。
>
> *存在一些差异，因为监视器的配置状态为 "已禁用"，但运行状况状态不会反映这种情况。这是因为，已配置的更改或其任何子监视器已显式启用。*

## <a name="enable-or-disable-virtual-machine"></a>启用或禁用虚拟机
可以禁用 VM 监视，以暂时停止所有监视器。 例如，在对 VM 执行维护时，可以为其禁用监视。

| 设置 | 说明 |
|:---|:---|
| 已启用  | 显示计算机的运行状况状态。 |
| 已禁用 | 计算机的运行状况状态显示为 " **已禁用**"。 不会创建警报。 |

## <a name="health-state-logic"></a>健康状况逻辑
单元监视器的运行状况状态逻辑定义了设置其运行状况状态的条件。 可以指定监视器的运行状况状态数，以及每个运行状况状态的计算方式的阈值。

![示例运行状况条件](media/vminsights-health-configure/sample-health-criteria.png)

聚合监视器不指定任何健康状况逻辑。 它们的运行状况状态由其下的单元监视器根据其运行状况汇总设置。

每个单元监视器有两个或三个运行状况状态。 每个将始终具有正常状态，并且可以选择警告状态和/或警告状态。 警告和严重状态每个都需要唯一的条件，用于定义监视器设置为此状态的时间。 正常状态没有条件，因为当不满足其他状态的条件时，将设置此状态。

在下面的示例中，"CPU 使用率" 设置为 "运行状况状态"：

- 如果大于90%，则为 "严重"。
- 如果其大于或等于80%，则发出警告。
- 如果低于80%，则运行正常。 这是隐式的，因为这种情况下不会应用任何其他条件。

## <a name="next-steps"></a>后续步骤

- [使用数据收集规则大规模配置监视器。](vminsights-health-configure-dcr.md)