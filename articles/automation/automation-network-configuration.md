---
title: Azure 自动化网络配置详细信息
description: 本文详细说明了 Azure 自动化状态配置、Azure 自动化混合 Runbook 辅助角色、更新管理和更改跟踪与清单所需的网络信息
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 0add7eed6abbe6c137d423ee4a7ef5f0f60072e3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900146"
---
# <a name="azure-automation-network-configuration-details"></a>Azure 自动化网络配置详细信息

此页提供了 [混合 Runbook 辅助角色和状态配置](#hybrid-runbook-worker-and-state-configuration)所需的网络详细信息，以及 [更新管理和更改跟踪和清单](#update-management-and-change-tracking-and-inventory)。

## <a name="hybrid-runbook-worker-and-state-configuration"></a>混合 Runbook 辅助角色和状态配置

混合 Runbook 辅助角色需要以下端口和 Url，并使 [自动化状态配置](automation-dsc-overview.md) 与 Azure 自动化通信。

* 端口：仅限443的出站 internet 访问权限
* 全局 URL：`*.azure-automation.net`
* US Gov 弗吉尼亚州的全局 URL： `*.azure-automation.us`
* 代理服务：`https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>混合 Runbook 辅助角色的网络规划

对于要连接到 Azure Automation 并向其注册的系统或用户混合 Runbook 辅助角色，它必须有权访问此部分中所述的端口号和 Url。 辅助角色还必须有权访问 Log Analytics 代理连接到 Azure Monitor Log Analytics 工作区 [所需的端口和 url](../azure-monitor/platform/agent-windows.md) 。

如果为特定的区域定义了自动化帐户，则可以限制与该区域数据中心之间的混合 Runbook 辅助角色通信。 查看 [Azure Automation 用于](how-to/automation-region-dns-records.md) 所需 dns 记录的 dns 记录。

### <a name="configuration-of-private-networks-for-state-configuration"></a>配置专用网络的状态配置

如果节点位于专用网络中，则需要使用上面定义的端口和 Url。 这些资源为托管节点提供网络连接，并允许 DSC 与 Azure 自动化功能通信。

如果使用在节点之间传递的 DSC 资源（如 [WaitFor* 资源](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)），还需要允许节点之间产生流量。 请参阅每个 DSC 资源的文档以了解这些网络要求。

若要了解 TLS 1.2 的客户端要求，请参阅[强制 Azure 自动化执行 TLS 1.2](automation-managing-data.md#tls-12-enforcement-for-azure-automation)。

## <a name="update-management-and-change-tracking-and-inventory"></a>更新管理和更改跟踪与清单

此表中的地址对更新管理和更改跟踪和清单都是必需的。 该表后面的段落同样适用于这两种情况。

与这些地址的通信使用 **端口 443**。

|Azure Public  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*.ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*。 azure-automation.us|

在创建网络组安全规则或配置 Azure 防火墙以允许流量流向自动化服务和 Log Analytics 工作区时，请使用 [服务标记](../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** 和 **AzureMonitor**。 这样可简化网络安全规则的日常管理。 若要安全且私下地从 Azure Vm 连接到自动化服务，请参阅 [使用 Azure 专用链接](./how-to/private-link-security.md)。 若要获取当前服务标记和范围信息，并将其包含为本地防火墙配置的一部分，请参阅[可下载的 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

## <a name="next-steps"></a>后续步骤

* 了解 [自动化更新管理概述](update-management\overview.md)。
* 了解 [混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)。
* 了解 [更改跟踪和清单](change-tracking\overview.md)。
* 了解 [自动化状态配置](automation-dsc-overview.md)。
