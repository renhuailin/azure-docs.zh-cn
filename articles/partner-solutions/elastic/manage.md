---
title: 管理与 Azure 的 Elastic 集成 - Azure 合作伙伴解决方案
description: 本文介绍如何在 Azure 门户上管理 Elastic。 如何配置诊断设置并删除资源。
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/20/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 9feb4c5eb143ced9079bfe9e63b451aecd437212
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952592"
---
# <a name="manage-the-elastic-integration-with-azure"></a>管理与 Azure 的 Elastic 集成

本文介绍如何管理与 Azure 的 Elastic 集成。 它演示如何配置诊断设置并删除 Elastic 资源。

## <a name="reconfigure-rules-for-metrics-and-logs"></a>重新配置指标和日志的规则

当你创建 Elastic 资源时，配置了将哪些日志发送到 Elastic。 如果需要更改这些设置，请在左窗格中选择“指标和日志”。 对将日志发送到 Elastic 的方式进行所需的更改。

有关这两种日志类型的详细信息，请参阅[快速入门：开始使用 Elastic](create.md)。

:::image type="content" source="media/manage/reconfigure-logs.png" alt-text="更改日志设置":::

## <a name="view-monitored-resources"></a>查看监视的资源

要查看向 Elastic 发送日志的资源列表，请在左窗格中选择“监视的资源”。

:::image type="content" source="media/manage/monitored-resources.png" alt-text="查看监视的资源":::

可以按资源类型、资源组名称、位置以及资源是否正在发送日志来筛选该列表。

“发送到 Elastic 的日志”列指示资源是否正在将日志发送到 Elastic。 如果资源未发送日志，则此字段会详细说明不发送日志的原因。 原因可能包括：

* 资源不支持发送日志。 只有在此处定义的所有资源类型和日志类别的 Azure 资源日志可以配置为向 Elastic 发送日志
* 达到了五个诊断设置的限制。 每个 Azure 资源最多可以有五个[诊断设置](../../azure-monitor/essentials/diagnostic-settings.md)。
* 错误会阻止将日志发送到 Elastic。
* 不会为资源配置日志。 只会将具有相应资源标记的资源发送到 Elastic。 你在日志配置中指定了标记规则。 
* 区域不受支持。 Azure 资源位于目前不将日志发送到 Elastic 的区域。 

## <a name="monitor-virtual-machines-using-elastic-agent"></a>使用 Elastic 代理监视虚拟机

可以将 Elastic 代理作为扩展安装在虚拟机上。 若要查看订阅中的可用虚拟机，请从 Elastic 资源的左侧窗格中选择“虚拟机”。

:::image type="content" source="media/manage/vm-agents.png" alt-text="查看虚拟机":::

对于每个虚拟机，将显示以下数据：

* 资源名称 – 虚拟机名称。
* 资源状态 – 虚拟机已停止还是正在运行。 Elastic 代理只能安装在正在运行的虚拟机上。 如果虚拟机已停止，则安装 Elastic 代理将被禁用。
* 代理版本 - Elastic 代理版本号。
* 代理状态 - Elastic 代理是否正在虚拟机上运行。
* 已启用集成 - Elastic 代理正在收集的关键指标。
* 发送日志 - Elastic 代理是否要将日志发送到 Elastic。

若要安装 Elastic 代理，请选择虚拟机，并选择“安装扩展”。

门户要求确认是否要安装具有默认身份验证的代理。 选择“确定”开始安装。 门户会在安装并预配代理之前，将状态显示为“正在安装”。

安装 Elastic 代理后，状态将更改为“已安装”。

要查看 Elastic 代理是否已安装，请选择该虚拟机，然后导航到“扩展”。

若要在虚拟机上卸载 Elastic 代理，请选择“虚拟机”和“卸载扩展”。

## <a name="configure-diagnostic-settings"></a>配置诊断设置

若要为资源配置诊断设置，请选择该资源。 在左窗格中，选择“诊断设置”。 

在目标详细信息部分中，选中“发送到合作伙伴解决方案”选项以选择 Elastic 作为目标。 此选项仅在创建 Elastic 资源后可用。

:::image type="content" source="media/manage/diagnostic-settings.png" alt-text="配置诊断设置":::

## <a name="delete-elastic-resource"></a>删除 Elastic 资源

如果不再需要 Elastic 资源，请删除 Azure 门户中的相应资源。

若要删除 Azure 中的资源，请选择你的 Elastic 资源。 在“概述”中，选择“删除” 。 确认要删除 Elastic 资源。

:::image type="content" source="media/manage/delete-elastic.png" alt-text="删除 Elastic 资源":::

删除 Elastic 资源后，不再将日志发送到 Elastic。 通过 Azure 市场对 Elastic 的所有计费停止。

## <a name="next-steps"></a>后续步骤

有关故障排除的帮助，请参阅[与 Azure 的 Elastic 集成故障排除](troubleshoot.md)。