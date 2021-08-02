---
title: 管理 Datadog 资源 - Azure 合作伙伴解决方案
description: 本文介绍如何在 Azure 门户中管理 Datadog 资源。 如何设置单一登录、删除 Confluent 组织并获得支持。
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/28/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: e964fd436795e19cc77a25efa95de5f6fcac87ca
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110652421"
---
# <a name="manage-the-datadog-resource"></a>管理 Datadog 资源

本文介绍如何管理 Azure 与 Datadog 的集成设置。

## <a name="resource-overview"></a>资源概述

要查看 Datadog 资源的详细信息，请在左窗格中选择“概述”。

:::image type="content" source="media/manage/resource-overview.png" alt-text="Datadog 资源概述" border="true" lightbox="media/manage/resource-overview.png":::

详细信息包括：

- 资源组名称
- 位置/区域
- 订阅
- Tags
- Datadog 组织的单一登录链接
- Datadog 产品/服务/计划
- 账单期限

它还提供 Datadog 仪表板、日志和主机映射的链接。

“概述”屏幕提供了将日志和指标发送到 Datadog 的资源摘要。

- 资源类型 – Azure 资源类型。
- 总资源 – 资源类型的所有资源计数。
- 发送日志的资源 – 通过集成将日志发送到 Datadog 的资源计数。
- 发送指标的资源 – 通过集成将指标发送到 Datadog 的资源计数。

## <a name="reconfigure-rules-for-metrics-and-logs"></a>重新配置指标和日志的规则

要更改指标和日志的配置规则，请在左窗格中选择“指标和日志”。

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="修改 Datadog 资源的日志和指标配置。" border="true":::

有关详细信息，请参阅[配置指标和日志](create.md#configure-metrics-and-logs)。

## <a name="view-monitored-resources"></a>查看监视的资源

要查看向 Datadog 发出日志的资源的列表，请在左窗格中选择“监视的资源”。

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="查看 Datadog 监视的资源" border="true":::

可以按资源类型、资源组名称、位置以及资源是否正在发送日志和指标来筛选资源列表。

列“日志到 Datadog”指示资源是否正在将日志发送到 Datadog。 如果资源不发送日志，则此字段指示日志不发送到 Datadog 的原因。 原因可能包括：

- 资源不支持发送日志。 只能将具有监视日志类别的资源类型配置为向 Datadog 发送日志。
- 达到了五个诊断设置的限制。 每个 Azure 资源最多可以有五个诊断设置。 有关详细信息，请参阅[诊断设置](../../azure-monitor/essentials/diagnostic-settings.md)。
- 错误。 资源配置为将日志发送到 Datadog，但被错误阻止。
- 未配置日志。 只有具有相应资源标记的 Azure 资源配置为将日志发送到 Datadog。
- 区域不受支持。 Azure 资源位于目前不支持将日志发送到 Datadog 的区域。
- 未配置 Datadog 代理。 未安装 Datadog 代理的虚拟机不会将日志发送到 Datadog。

## <a name="api-keys"></a>API 密钥

要查看 Datadog 资源的 API 密钥列表，请在左窗格中选择“密钥”。 将看到有关密钥的信息。

:::image type="content" source="media/manage/keys.png" alt-text="Datadog 组织的 API 密钥。" border="true":::

Azure 门户提供 API 密钥的只读视图。 要管理密钥，请选择 Datadog 门户链接。 在 Datadog 门户中进行更改后，刷新 Azure 门户视图。

通过 Azure Datadog 集成，可以在虚拟机或应用服务上安装 Datadog 代理。 如果未选择默认密钥，Datadog 代理安装将失败。

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>使用 Datadog 代理监视虚拟机

可以将 Datadog 代理作为扩展安装在虚拟机上。 转到左侧窗格中的“Datadog 组织配置”下的“虚拟机代理”。 此屏幕显示订阅中所有虚拟机的列表。

对于每个虚拟机，将显示以下数据：

- 资源名称 – 虚拟机名称
- 资源状态 – 虚拟机已停止还是正在运行。 Datadog 代理只能安装在正在运行的虚拟机上。 如果虚拟机已停止，将禁用 Datadog 代理安装。
- 代理版本 – Datadog 代理版本号。
- 代理状态 – Datadog 代理是否正在虚拟机上运行。
- 已启用集成 – Datadog 代理正在收集的关键指标。
- 安装方法 – 用于安装 Datadog 代理的特定工具。 例如，Chef 或 Script。
- 发送日志 – Datadog 代理是否要将日志发送到 Datadog。

选择要在其上安装 Datadog 代理的虚拟机。 选择“安装代理”。

门户要求确认是否要安装具有默认密钥的代理。 选择“确定”开始安装。 Azure 会在安装并预配代理之前，将状态显示为“正在安装”。

安装 Datadog 代理后，状态将更改为“已安装”。

要查看 Datadog 代理是否已安装，请选择该虚拟机，然后导航到“扩展”窗口。

转到“虚拟机代理”，可在虚拟机上卸载 Datadog 代理。 选择虚拟机和“卸载代理”。

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>使用 Datadog 代理作为扩展来监视应用服务

可以在应用服务上安装 Datadog 代理作为扩展。 转到左侧窗格中的“应用服务扩展”。 此屏幕显示订阅中所有应用服务的列表。

对于每个应用服务，将显示以下数据元素：

- 资源名称 – 虚拟机名称。
- 资源状态 – 应用服务已停止还是正在运行。 Datadog 代理只能安装在正在运行的应用服务上。 如果应用服务已停止，将禁用 Datadog 代理安装。
- 应用服务计划 – 为应用服务配置的特定计划。
- 代理版本 – Datadog 代理版本号。

要安装 Datadog 代理，请选择应用服务和“安装扩展”。 最新的 Datadog 代理作为扩展安装在应用服务中。

门户确认你要安装 Datadog 代理。 此外，将用默认密钥更新特定应用服务的应用程序设置。 安装完 Datadog 代理后，将重启应用服务。

选择“确定”开始 Datadog 代理的安装过程。 门户会在安装代理之前，将状态显示为“正在安装”。 安装 Datadog 代理后，状态将更改为“已安装”。

要卸载应用服务上的 Datadog 代理，请转到“应用服务扩展”。 选择应用服务和“卸载扩展”

## <a name="reconfigure-single-sign-on"></a>重新配置单一登录

如果要重新配置单一登录，请在左侧窗格中选择“单一登录”。

要通过 Azure Active Directory 建立单一登录，请选择“通过 Azure Active Directory 启用单一登录”。

门户将从 Azure Active Directory 中检索相应的 Datadog 应用程序。 此应用来自在设置集成时选择的企业应用名称。 选择 Datadog 应用名称，如下所示：
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="重新配置单一登录应用程序。" border="true":::
 
## <a name="change-plan"></a>更改计划

若要更改 Datadog 计费计划，请转到“概述”并选择“更改计划” 。

:::image type="content" source="media/manage/datadog-select-change-plan.png" alt-text="选择更改 Datadog 计费计划。" border="true":::

门户将检索租户的所有可用 Datadog 计划。 选择适当的计划，然后单击“更改计划”。

:::image type="content" source="media/manage/datadog-change-plan.png" alt-text="选择要更改的 Datadog 计费计划。" border="true":::
  
## <a name="disable-or-enable-integration"></a>禁用或启用集成

可以停止将日志和指标从 Azure 发送到 Datadog。 对于与监视指标和日志无关的其他 Datadog 服务，将继续计费。

要禁用 Azure 与 Datadog 的集成，请转到“概述”。 选择“禁用”和“确定”。
 
:::image type="content" source="media/manage/disable.png" alt-text="禁用 Datadog 资源。" border="true":::

要启用 Azure 与 Datadog 的集成，请转到“概述”。 选择“启用”和“确定”。 选择“启用”将检索任何以前的指标和日志配置。 配置确定哪些 Azure 资源会向 Datadog 发出指标和日志。 完成该步骤后，会将指标和日志发送到 Datadog。
 
:::image type="content" source="media/manage/enable.png" alt-text="启用 Datadog 资源。" border="true":::

## <a name="delete-datadog-resource"></a>删除 Datadog 资源

转到左侧窗格中的“概述”，然后选择“删除”。 确认要删除 Datadog 资源。 选择“删除”。 

:::image type="content" source="media/manage/delete.png" alt-text="删除 Datadog 资源" border="true":::

如果只有一个 Datadog 资源映射到 Datadog 组织，则不再将日志和指标发送到 Datadog。 Datadog 的所有账单都通过 Azure 市场停止。

如果有多个 Datadog 资源映射到 Datadog 组织，则删除 Datadog 资源仅停止发送该 Datadog 资源的日志和指标。 由于 Datadog 组织已链接到其他 Azure 资源，因此将通过 Azure 市场继续计费。

## <a name="next-steps"></a>后续步骤

有关故障排除的帮助，请参阅 [Datadog 解决方案故障排除](troubleshoot.md)。
