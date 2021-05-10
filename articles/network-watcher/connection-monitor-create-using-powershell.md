---
title: 创建连接监视器 - PowerShell
titleSuffix: Azure Network Watcher
description: 了解如何使用 PowerShell 创建连接监视器。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 7f175d82b650871437a506ea4513f0ae28360f68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833025"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>使用 PowerShell 创建连接监视器

> [!IMPORTANT]
> 自 2021 年 7 月 1 日起，你将无法在现有工作区中添加新测试，也无法在网络性能监视器中启用新的工作区。 你还将无法在连接监视器（经典）中添加新的连接监视器。 可以继续使用在 2021 年 7 月 1 日之前创建的测试和连接监示器。 为了最大程度地减少当前工作负荷的服务中断，请在 2024 年 2 月 29 日之前，在 Azure 网络观察程序中[将测试从网络性能监视器迁移](migrate-to-connection-monitor-from-network-performance-monitor.md)或[从连接监视器（经典）迁移](migrate-to-connection-monitor-from-connection-monitor-classic.md)到新连接监视器。


了解如何使用 Azure 网络观察程序的连接监视器功能来监视资源之间的通信。


## <a name="before-you-begin"></a>开始之前

在使用连接监视器创建的连接监视器中，可以将本地计算机和 Azure 虚拟机 (VM) 添加为源。 这些连接监视器还可以监视与终结点的连接。 终结点可以位于 Azure 上，也可以位于任何其他 URL 或 IP 上。

连接监视器包含以下实体：

* **连接监视器资源**：区域特定的 Azure 资源。 以下所有实体都是连接监视器资源的属性。
* **终结点**：参与连接性检查的源或目标。 终结点的示例包括 Azure VM、本地代理、URL 和 IP。
* **测试配置**：针对测试的特定于协议的配置。 可以根据所选协议来定义端口、阈值、测试频率和其他参数。
* **测试组**：包含源终结点、目标终结点和测试配置的组。 连接监视器可包含多个测试组。
* **测试**：将源终结点、目标终结点和测试配置组合在一起。 测试是可用于监视数据的最精细级别。 监视数据包括检查失败的百分比和往返时间 (RTT)。

    ![显示连接监视器的示意图，其中定义了测试组和测试之间的关系。](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>创建连接监视器的步骤

使用以下命令通过 PowerShell 创建连接监视器。

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>属性说明

* **ConnectionMonitorName**：连接监视器资源的名称。

* **SUB**：将在其中创建连接监视器的订阅的订阅 ID。

* **NW**：在其中创建连接监视器的网络观察程序资源 ID。

* **位置**：在其中创建连接监视器的区域。

* **终结点**
    * **名称**：每个终结点的唯一名称。
    * **资源 ID**：对于 Azure 终结点，资源 ID 是指 VM 的 Azure 资源管理器资源 ID。 对于非 Azure 终结点，资源 ID 是指链接到非 Azure 代理的 Log Analytics 工作区的 Azure 资源管理器资源 ID。
    * **地址**：仅当未指定资源 ID 或资源 ID 在 Log Analytics 工作区中时适用。 如果在没有资源 ID 的情况下使用，则可以是任何公共终结点的 URL 或 IP。 如果与 Log Analytics 资源 ID 一起使用，则是指监视代理的 FQDN。
    * **筛选器**：对于非 Azure 终结点，使用筛选器从 Log Analytics 工作区选择连接监视资源中的监视代理。 如果未设置筛选器，则属于 Log Analytics 工作区的所有代理均可用于监视。
        * **类型**：设置为“代理地址”。
        * **地址**：设置为本地代理的 FQDN。

* **测试组**
    * **名称**：为测试组命名。
    * **来源**：从之前创建的终结点中选择。 基于 Azure 的源终结点需要安装 Azure 网络观察程序扩展，基于非 Azure 的源终结点需要安装 Azure Log Analytics 代理。 若要为源安装代理，请参阅[安装监视代理](./connection-monitor-overview.md#install-monitoring-agents)。
    * **目标**：从之前创建的终结点中选择。 可以通过将 Azure VM 或任何终结点（公共 IP、URL 或 FQDN）指定为目标，从而监视其连接。 单个测试组中可以添加 Azure VM、Office 365 URL、Dynamics 365 URL 和自定义终结点。
    * **禁用**：为测试组指定的所有源和目标禁用监视。

* **测试配置**
    * **名称**：为测试配置命名。
    * **TestFrequencySec**：指定源对指定协议和端口上的目标执行 ping 操作的频率。 可以选择 30 秒、1 分钟、5 分钟、15 分钟或 30 分钟。 源将根据所选的值来测试与目标的连接。 例如，如果选择 30 秒，则源将在 30 秒的时间段内至少检查一次与目标的连接。
    * **协议**：选择 TCP、ICMP、HTTP 或 HTTPS。 根据协议，还可以选择以下特定于协议的配置：
        * **preferHTTPS**：指定是否使用 HTTPS 而不是 HTTP。
        * **port**：指定首选目标端口。
        * **disableTraceRoute**：阻止源发现拓扑和逐跳 RTT。 这适用于使用 TCP 或 ICMP 的测试组。
        * **方法**：选择 HTTP 请求方法（GET 或 POST）。 这适用于使用 HTTP 的测试配置。
        * **path**：指定要追加到 URL 的路径参数。
        * **validStatusCodes**：选择适用的状态代码。 如果响应代码不匹配，将显示一条诊断消息。
        * **requestHeaders**：指定传递到目标的自定义请求标头字符串。
    * **成功阈值**：在以下网络参数上设置阈值：
        * **checksFailedPercent**：设置在源使用指定条件检查到目标的连接情况时可能检查失败的百分比。 对于 TCP 或 ICMP 协议，检查失败的百分比可能会与数据包丢失的百分比相同。 对于 HTTP 协议，此字段表示未接收到响应的 HTTP 请求的百分比。
        * **roundTripTimeMs**：设置用于确定源按测试配置连接到目标所需的时间（以毫秒为单位）。

## <a name="scale-limits"></a>规模限制

连接监视器具有以下规模限制：

* 每个区域每个订阅的最大连接监视器数：100
* 每个连接监视器的最大测试组：20 个
* 每个连接监视器的最大源和目标：100
* 每个连接监视器的最大测试组：20

## <a name="next-steps"></a>后续步骤

* 了解[如何分析监视数据并设置警报](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)。
* 了解[如何诊断网络中的问题](./connection-monitor-overview.md#diagnose-issues-in-your-network)。
