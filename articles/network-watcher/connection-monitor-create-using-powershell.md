---
title: 创建连接监视器-PowerShell
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
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: 1d5f879ead35ef6d47b993ff833dc0b0595e3c6c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861912"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>使用 PowerShell 创建连接监视器

了解如何使用 Azure 网络观察程序的连接监视器功能来监视资源之间的通信。


## <a name="before-you-begin"></a>开始之前

在用连接监视器创建的连接监视器中，可以将本地计算机和 Azure 虚拟机 (Vm) 为源。 这些连接监视器还可以监视与终结点的连接。 终结点可以位于 Azure 上，也可以位于任何其他 URL 或 IP 上。

连接监视器包括以下实体：

* **连接监视器资源**：特定于区域的 Azure 资源。 以下所有实体都是连接监视器资源的属性。
* **终结点**：参与连接检查的源或目标。 终结点的示例包括 Azure VM、本地代理、URL 和 IP。
* **测试配置**：测试的协议特定配置。 可以根据所选协议来定义端口、阈值、测试频率和其他参数。
* **测试组**：包含源终结点、目标终结点和测试配置的组。 连接监视器可包含多个测试组。
* **测试**：源终结点、目标终结点和测试配置的组合。 测试是可用于监视数据的最精细级别。 监视数据包括检查失败的百分比和往返时间 (RTT)。

    ![显示连接监视器的关系图，定义测试组和测试之间的关系。](./media/connection-monitor-2-preview/cm-tg-2.png)

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

* **SUB**：要在其中创建连接监视器的订阅的订阅 ID。

* **NW**：在其中创建连接监视器的网络观察程序资源 ID。

* **位置**：在其中创建连接监视器的区域。

* **Endpoints**
    * **名称**：每个终结点的唯一名称。
    * **资源 id**：对于 azure 终结点，资源 id 是指 Vm 的 Azure 资源管理器资源 id。 对于非 Azure 终结点，资源 ID 是指链接到非 Azure 代理的 Log Analytics 工作区的 Azure 资源管理器资源 ID。
    * **Address**：仅当未指定资源 id 或资源 id 在 Log Analytics 工作区中时才适用。 如果在没有资源 ID 的情况下使用，则可以是任何公共终结点的 URL 或 IP。 如果与 Log Analytics 资源 ID 一起使用，则表示监视代理的 FQDN。
    * **筛选器**：对于非 Azure 终结点，请使用筛选器从连接监视器资源的 "Log Analytics" 工作区中选择 "监视代理"。 如果未设置筛选器，则可以使用属于 Log Analytics 工作区的所有代理进行监视。
        * **类型**：设置为 **代理地址**。
        * **Address**：设置为本地代理的 FQDN。

* **测试组**
    * **名称**：为测试组命名。
    * **源**：从前面创建的终结点中进行选择。 基于 azure 的源终结点需要安装 Azure 网络观察程序扩展;非基于 Azure 的源终结点需要安装 Azure Log Analytics 代理。 若要为源安装代理，请参阅[安装监视代理](./connection-monitor-overview.md#install-monitoring-agents)。
    * **目标**：从前面创建的终结点中进行选择。 可以通过将 Azure VM 或任何终结点（公共 IP、URL 或 FQDN）指定为目标，从而监视其连接。 单个测试组中可以添加 Azure VM、Office 365 URL、Dynamics 365 URL 和自定义终结点。
    * **禁用**：对测试组指定的所有源和目标禁用监视。

* **测试配置**
    * **名称**：命名测试配置。
    * **TestFrequencySec**：指定在指定的协议和端口上源 ping 目标的频率。 可以选择 30 秒、1 分钟、5 分钟、15 分钟或 30 分钟。 源根据你选择的值测试与目标的连接。 例如，如果选择30秒，则源会在30秒的时间段内至少检查与目标的连接。
    * **协议**：选择 TCP、ICMP、HTTP 或 HTTPS。 根据协议，还可以选择以下特定于协议的配置：
        * **preferHTTPS**：指定是否通过 HTTP 使用 HTTPS。
        * **端口**：指定所选的目标端口。
        * **disableTraceRoute**：阻止源发现拓扑和逐跃点的 RTT。 这适用于带有 TCP 或 ICMP 的测试组。
        * **方法**：选择 (GET 或 POST) 的 HTTP 请求方法。 这适用于带有 HTTP 的测试配置。
        * **路径**：指定要追加到 URL 的路径参数。
        * **validStatusCodes**：选择适用的状态代码。 如果响应代码不匹配，则会显示诊断消息。
        * **requestHeaders**：指定传递给目标的自定义请求标头字符串。
    * **成功阈值**：在以下网络参数上设置阈值：
        * **checksFailedPercent**：设置在源使用指定的条件检查到目标的连接时可能失败的检查的百分比。 对于 TCP 或 ICMP 协议，失败检查的百分比可能等于数据包丢失的百分比。 对于 HTTP 协议，此字段表示未接收到响应的 HTTP 请求的百分比。
        * **roundTripTimeMs**：设置源可以通过测试配置连接到目标所需的时间（以毫秒为单位）。

## <a name="scale-limits"></a>规模限制

连接监视器具有以下规模限制：

* 每个区域每个订阅的最大连接监视器数：100
* 每个连接监视器的最大测试组：20 个
* 每个连接监视器的最大源和目标：100
* 每个连接监视器的最大测试组：20

## <a name="next-steps"></a>后续步骤

* 了解[如何分析监视数据并设置警报](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)。
* 了解 [如何诊断网络中的问题](./connection-monitor-overview.md#diagnose-issues-in-your-network)。