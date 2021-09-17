---
title: Azure 中的连接监视器 | Microsoft Docs
description: 了解如何使用连接监视器监视分布式环境中的网络通信。
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 4e8f1847459d16f82b029f9719d87b61dd243524
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542362"
---
# <a name="network-connectivity-monitoring-with-connection-monitor"></a>使用连接监视器进行网络连接监视

> [!IMPORTANT]
> 自 2021 年 7 月 1 日起，你将无法在现有工作区中添加新测试，也无法在网络性能监视器中启用新的工作区。 你还将无法在连接监视器（经典版）中添加新的连接监视器。 可以继续使用在 2021 年 7 月 1 日之前创建的测试和连接监视器。 为了最大程度地减少当前工作负荷的服务中断，请在 2024 年 2 月 29 日之前，在 Azure 网络观察程序中[将测试从网络性能监视器迁移到新连接监视器](migrate-to-connection-monitor-from-network-performance-monitor.md)或[从连接监视器（经典版）迁移到新连接监视器](migrate-to-connection-monitor-from-connection-monitor-classic.md)。

连接监视器在 Azure 网络观察程序中提供统一的端到端连接监视。 连接监视器功能支持混合部署和 Azure 云部署。 网络观察程序提供的工具可用于监视、诊断和查看针对 Azure 部署的与连接相关的指标。

下面是连接监视器的一些用例：

- 前端 Web 服务器 VM 与多层应用程序中的数据库服务器 VM 进行通信。 你希望检查两个 VM 之间的网络连接。
- 你希望美国东部区域的 VM 能够端对端连接到美国中部区域的 VM，并且希望比较两者的跨区域网络延迟。
- 你有多个本地办公场所位于华盛顿州西雅图和弗吉尼亚州阿什本。 你的办公地点连接到 Microsoft 365 URL。 针对 Microsoft 365 URL 的用户，在西雅图和阿什本之间比较延迟。
- 混合应用程序需要连接到 Azure 存储终结点。 本地站点和 Azure 应用程序连接到相同的 Azure 存储终结点。 希望比较本地站点的延迟与 Azure 应用程序的延迟。
- 希望检查本地设置与托管云应用程序的 Azure VM 之间的连接。

连接监视器结合了以下两项功能的优点：网络观察程序[连接监视器（经典）](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint)功能，以及网络性能监视器 (NPM) [服务连接监视器](../azure-monitor/insights/network-performance-monitor-service-connectivity.md)、[ExpressRoute 监视](../expressroute/how-to-npm.md)和[性能监视](../azure-monitor/insights/network-performance-monitor-performance-monitor.md)功能。

下面是连接监视器的一些优点：

* 适用于 Azure 和混合监视需求的直观一致的体验
* 跨区域且跨工作区的连接监视
* 更高的探测频率，更好地了解网络性能
* 为混合部署提供更快的警报
* 支持基于 HTTP、TCP 和 ICMP 的连接检查 
* 同时适用于 Azure 和非 Azure 测试设置的指标和 Log Analytics 支持

![显示连接监视器如何与 Azure VM、非 Azure 主机、终结点和数据存储位置进行交互的示意图](./media/connection-monitor-2-preview/hero-graphic.png)

若要开始使用连接监视器进行监视，请执行以下步骤： 

1. 安装监视代理。
1. 在订阅上启用网络观察程序。
1. 创建连接监视器。
1. 设置数据分析和警报。
1. 诊断网络中的问题。

以下部分提供了更多有关这些步骤的详细信息。

## <a name="install-monitoring-agents"></a>安装监视代理

连接监视器依赖轻型的可执行文件来运行连接性检查。 它同时支持来自 Azure 环境和本地环境的连接性检查。 使用的可执行文件取决于 VM 是托管在 Azure 上还是托管在本地。

### <a name="agents-for-azure-virtual-machines"></a>Azure 虚拟机代理

若要使连接监视器将 Azure VM 识别为监视源，请在其上安装网络观察程序代理虚拟机扩展。 此扩展也称为 *网络观察程序扩展*。 Azure 虚拟机需要该扩展来触发端对端监控和其他高级功能。 

可以在[创建 VM](./connection-monitor.md#create-the-first-vm) 时安装网络观察程序扩展。 还可以为 [Linux](../virtual-machines/extensions/network-watcher-linux.md) 和 [Windows](../virtual-machines/extensions/network-watcher-windows.md) 分别安装、配置网络观察程序扩展并对其进行故障排除。

用于网络安全组 (NSG) 或防火墙的规则可以阻止源和目标之间的通信。 连接监视器检测到此问题并将其显示为拓扑中的诊断消息。 若要启用连接监视，请确保 NSG 和防火墙规则允许在源和目标之间使用 TCP 或 ICMP 上的数据包。

### <a name="agents-for-on-premises-machines"></a>本地计算机的代理

若要使连接监视器将本地计算机识别为要监视的源，请在计算机上安装 Log Analytics 代理。  然后启用[网络性能监视器解决方案](/azure-monitor/insights/network-performance-monitor.md#configure-the-solution)。 这些代理是链接到 Log Analytics 工作区的，因此，需要先设置工作区 ID 和主密钥，然后代理才能开始进行监视。

若要安装适用于 Windows 计算机的 Log Analytics 代理，请参阅[在 Windows 上安装 Log Analytics 代理](../azure-monitor/agents/agent-windows.md)。

如果路径包括防火墙或网络虚拟设备 (NVA)，请确保可访问目标。

对于 Windows 计算机，若要打开端口，请使用管理员权限在 PowerShell 窗口中运行不带任何参数的 [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell 脚本。

对于 Linux 计算机，需要手动更改要使用的 portNumbers。 
* 导航到路径：/var/opt/microsoft/omsagent/npm_state。 
* 打开文件：npmdregistry
* 更改端口号 ```“PortNumber:<port of your choice>”``` 的值

 请注意，在工作区使用的所有代理中，所用的端口号都应该相同。 

该脚本可创建解决方案所需的注册表项。 它还会创建 Windows 防火墙规则，允许代理创建彼此之间的 TCP 连接。 该脚本创建的注册表项指定是否记录调试日志和该日志文件的路径。 该脚本还会定义用于通信的代理 TCP 端口。 该脚本会自动设置这些注册表项的值。 请勿手动更改这些注册表项。 默认打开的端口为 8084。 通过向该脚本提供参数 portNumber 即可使用自定义端口。 在运行该脚本的所有计算机上使用相同端口。 [详细了解](../azure-monitor/agents/log-analytics-agent.md#network-requirements) Log Analytics 代理的网络要求

此脚本仅在本地配置 Windows 防火墙。 如果有网络防火墙，请确保该防火墙允许流量去往网络性能监视器使用的 TCP 端口。

LA Windows 代理可以是多宿主的，以便将数据发送到多个工作区和 System Center Operations Manager 管理组。 Linux 代理只能发送到一个目标（一个工作区或一个管理组）。

#### <a name="enable-network-performance-monitor-solution-for-on-premise-machines"></a>为本地计算机启用网络性能监视器解决方案 

按照以下步骤为本地计算机启用网络性能监视器解决方案 

1. 在 Azure 门户主页上，转到“网络观察程序”
2. 在左侧的“监视”部分，选择“网络性能监视器” 
3. 在门户中，你将看到已启用 NPM 解决方案的工作区列表，并按订阅进行了筛选 
4. 若要在“新建工作区”中添加 NPM 解决方案，请单击门户左上方的“+ 添加 NPM” 
5. 选择要在其中启用解决方案的订阅和工作区，然后单击“创建”
6. 启用解决方案后，工作区需要几分钟才能显示在门户上。

:::image type="content" source="./media/connection-monitor/network-performance-monitor-solution-enable.png" alt-text="显示如何在连接监视器中添加 NPM 解决方案的屏幕截图" lightbox="./media/connection-monitor/network-performance-monitor-solution-enable.png":::

与 LA 代理不同，NPM 解决方案只能配置为将数据发送到单个 LA 工作区

## <a name="enable-network-watcher-on-your-subscription"></a>在订阅上启用网络观察程序

使用网络观察程序启用具有虚拟网络的所有订阅。 在订阅中创建虚拟网络时，虚拟网络的区域和订阅中会自动启用网络观察程序。 这种自动启用不会影响资源，也不会产生费用。 确保未在订阅上显式禁用网络观察程序。 

请务必确保网络观察程序[可用于你所在的区域](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher&regions=all)。 有关详细信息，请参阅[启用网络观察程序](./network-watcher-create.md)。

## <a name="create-a-connection-monitor"></a>创建连接监视器 

连接监视器会定期监视通信。 它会通知你有关可访问性和延迟的更改情况。 还可以检查源代理和目标终结点之间当前的和历史的网络拓扑。

源可以是 Azure VM，源也可以是具有已安装的监视代理的本地计算机。 目标终结点可以是 Microsoft 365 URL、Dynamics 365 URL、自定义 URL、Azure VM 资源 ID、IPv4、IPv6、FQDN 或任何域名。

### <a name="access-connection-monitor"></a>访问连接监视器

1. 在 Azure 门户主页上，转到“网络观察程序”。
1. 在左侧的“监视”部分，选择“连接监视器” 。
1. 你会看到在连接监视器中创建的所有连接监视器。 若要查看在经典连接监视器体验中创建的连接监视器，请转到“连接监视器”选项卡。
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="屏幕截图显示了在连接监视器中创建的连接监视器" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>创建连接监视器

在通过连接监视器创建的连接监视器中，可以将本地计算机和 Azure VM 添加为源。 这些连接监视器还可以监视与终结点的连接。 终结点可以位于 Azure 上，也可以位于任何其他 URL 或 IP 上。

连接监视器包含以下实体：

* **连接监视器资源** – 特定于区域的 Azure 资源。 以下所有实体都是连接监视器资源的属性。
* **终结点** – 参与连接检查的源或目标。 终结点的示例包括 Azure VM、本地代理、URL 和 IP。
* **测试配置** – 针对测试的特定于协议的配置。 根据选定协议，可以定义端口、阈值、测试频率和其他参数。
* **测试组** – 包含源终结点、目标终结点和测试配置的组。 连接监视器可包含多个测试组。
* **测试** – 将源终结点、目标终结点和测试配置组合在一起。 测试是可用于监视数据的最精细级别。 监视数据包括检查失败的百分比和往返时间 (RTT)。

 ![显示连接监视器的示意图（该图定义了测试组和测试之间的关系）](./media/connection-monitor-2-preview/cm-tg-2.png)

可以使用 [Azure 门户](./connection-monitor-create-using-portal.md)、[ARMClient](./connection-monitor-create-using-template.md) 或 [PowerShell](connection-monitor-create-using-powershell.md) 创建连接监视器

添加到测试组中的所有源、目标和测试配置将细分为单独的测试。 下面是如何分解源和目标的示例：

* 测试组：TG1
* 源：3 (A, B, C)
* 目标：2 (D, E)
* 测试配置：2 (Config 1, Config 2)
* 已创建的测试总数：12

| 测试编号 | Source | 目标 | 测试配置 |
| --- | --- | --- | --- |
| 1 | A | D | Config 1 |
| 2 | A | D | Config 2 |
| 3 | A | E | Config 1 |
| 4 | A | E | Config 2 |
| 5 | B | D | Config 1 |
| 6 | B | D | Config 2 |
| 7 | B | E | Config 1 |
| 8 | B | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a>规模限制

连接监视器具有以下规模限制：

* 每个区域每个订阅的最大连接监视器数：100
* 每个连接监视器的最大测试组：20 个
* 每个连接监视器的最大源和目标：100
* 每个连接监视器的最大测试组：20

## <a name="analyze-monitoring-data-and-set-alerts"></a>分析监视数据并设置警报

创建连接监视器后，源会根据测试配置来检查与目标的连接。

### <a name="checks-in-a-test"></a>签入测试

根据你在测试配置中所选的协议，连接监视器对源-目标对运行一系列检查。 根据所选测试频率运行检查。

如果使用 HTTP，则服务会计算返回了有效响应代码的 HTTP 响应数。 可使用 PowerShell 和 CLI 设置有效的响应代码。 结果确定检查失败的百分比。 为了计算 RTT，服务会测量 HTTP 调用和响应之间的时间。

如果使用 TCP 或 ICMP，则服务会计算数据包丢失百分比，以确定检查失败的百分比。 若要计算 RTT，则服务会测量为发送的数据包接收确认 (ACK) 所花费的时间。 如果已为网络测试启用了跟踪路由数据，则可以查看本地网络的逐跳丢失和延迟。

### <a name="states-of-a-test"></a>测试的状态

根据检查返回的数据，测试可以具有以下状态：

* 通过 - 检查失败和 RTT 的百分比的实际值都在指定的阈值内。
* 失败 - 检查失败或 RTT 的百分比的实际值超出指定的阈值。 如果未指定阈值，则当检查失败的百分比为 100 时，测试进入失败状态。
* 警告 - 
     * 如果指定了阈值，并且连接监视器观察到检查失败百分比超过阈值的 80%，则测试将标记为“警告”。
     * 如果没有指定的阈值，连接监视器会自动分配一个阈值。 超过此阈值时，测试状态将更改为“警告”。 对于 TCP 或 ICMP 测试中的往返时间，阈值为 750msec。 对于检查失败百分比，阈值为 10%。 
* 不确定  - Log Analytics 工作区中没有数据。  检查指标。 
* 未运行  - 已通过禁用测试组进行禁用  

### <a name="data-collection-analysis-and-alerts"></a>数据收集、分析和警报

连接监视器收集的数据存储在 Log Analytics 工作区中。 创建连接监视器时设置此工作区。 

监视数据也可用于 Azure Monitor 指标中。 可以根据需要使用 Log Analytics 保留监视数据。 默认情况下，Azure Monitor 仅存储 30 天的指标。 

可以[根据数据设置基于指标的警报](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)。

#### <a name="monitoring-dashboards"></a>监视仪表板

在监视仪表板上，可以查看可以访问的订阅、区域、时间戳、源和目标类型的连接监视器列表。

从网络观察程序中转到连接监视器时，可以通过以下方式查看数据：

* 连接监视器 - 为订阅、区域、时间戳、源和目标类型创建的所有连接监视器的列表。 此视图为默认视图。
* 测试组 - 为订阅、区域、时间戳、源和目标类型创建的所有测试组的列表。 这些测试组不由连接监视器进行筛选。
* 测试 - 针对订阅、区域、时间戳、源和目标类型运行的所有测试的列表。 这些测试不由连接监视器或测试组进行筛选。

在下图中，这三个数据视图由箭头 1 指示。

在仪表板上，可以展开每个连接监视器以查看其测试组。 然后，可以展开每个测试组，以查看在其中运行的测试。 

你可以基于以下内容筛选列表：

* 顶层筛选器 - 按文本、实体类型（连接监视器、测试组或测试）时间戳和范围搜索列表。 范围包括订阅、区域、源和目标类型。 请查看下图中的框 1。
* 基于状态的筛选器 - 按连接监视器、测试组或测试的状态进行筛选。 请查看下列图像中的框 2。
* **基于警报的筛选器** - 按连接监视器资源上触发的警报进行筛选。 请查看下图中的框 3。

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="屏幕截图显示了如何在连接监视器中筛选连接监视器、测试组和测试的视图" lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
例如，若要在连接监视器中查看源 IP 是 10.192.64.56 的的所有测试，请执行以下操作：
1. 将视图更改为“测试”。
1. 在搜索字段中键入“10.192.64.56”
1. 在顶层筛选器的“范围”中，选择“源” 。

若要在连接监视器中仅显示源 IP 是 10.192.64.56 的失败测试，请执行以下操作：
1. 将视图更改为“测试”。
1. 对于基于状态的筛选器，请选择“失败”。
1. 在搜索字段中键入“10.192.64.56”
1. 在顶层筛选器的“范围”中，选择“源” 。

若要在连接监视器中仅显示目标为 outlook.office365.com 的失败测试，请执行以下操作：
1. 将视图更改为“测试”。
1. 对于基于状态的筛选器，请选择“失败”。
1. 在搜索字段中，输入“office.live.com”
1. 在顶层筛选器的“范围”中，选择“目标” 。
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="显示筛选为仅显示 Outlook.Office365.com 目标测试失败的视图的屏幕截图" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

若要了解连接监视器、测试组或测试的失败原因，请单击“原因”列。  该列会显示超过了哪个阈值（检查失败的百分比或 RTT）以及相关的诊断消息
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="显示连接监视器、测试或测试组失败原因的屏幕截图" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
若要查看 RTT 的趋势和连接监视器检查失败的百分比，请执行以下操作：
1. 选择要调查的连接监视器。

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="显示连接监视器的指标的屏幕截图（按测试组显示）" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. 会看到以下部分  
    1. 概要 - 所选连接监视器的资源特定属性 
    1. 汇总 - 
        1. 对于连接监视器中的所有测试，RTT 的聚合趋势线和检查失败百分比。 可设置特定时间以查看详细信息。
        1. 基于 RTT 或检查失败百分比得出的测试组、源和目标中的前 5 项。 
    1. 测试组、源、目标和测试配置的选项卡 - 列出连接监视器中的测试组、源或目标。 失败的检查测试数，聚合 RTT 和检查失败百分比值。  还可回到过去的时间查看数据。 
    1. 问题 - 连接监视器中每个测试的跃点级别问题。 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="显示连接监视器的指标的屏幕截图（按测试组显示）第 2 部分" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. 可以
    * 单击“查看所有测试”- 查看连接监视器中的所有测试
    * 单击“查看所有测试组、测试配置、源和目标”- 查看特定于每项的详细信息。 
    * 选择测试组、测试配置、源或目标 - 查看实体中的所有测试。

1. 在“查看所有测试”视图中，你可以：
    * 选择测试并单击“比较”。
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="显示 2 个测试的比较情况的屏幕截图" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * 使用群集将 VNET 和子网等复合资源扩展到其子资源
    * 单击拓扑查看任何测试的拓扑。

若要查看 RTT 的趋势和测试组检查失败的百分比，请执行以下操作：
1. 选择要调查的测试组。 
1. 你将看到与连接监视器类似的内容 - 有关于测试组、源、目标和测试配置的概要、汇总和表。 导航方式与连接监视器的一样

若要查看 RTT 的趋势和测试检查失败的百分比，请执行以下操作：
1. 选择要调查的测试。 你将看到网络拓扑，以及检查失败百分比和往返时间的端到端趋势图。 若要查看标识的问题，请在拓扑中选择路径中的任何跃点。 （这些跃点都是 Azure 资源。）此功能当前不适用于本地网络

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="显示测试的拓扑视图的屏幕截图" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Log Analytics 中的日志查询

使用 Log Analytics 创建监视数据的自定义视图。 UI 显示的所有数据都来自 Log Analytics。 可以以交互方式分析存储库中的数据。 关联代理运行状况中的数据或基于 Log Analytics 的其他解决方案。 将数据导出到 Excel 或 Power BI，或创建可共享的链接。

#### <a name="network-topology-in-connection-monitor"></a>连接监视器中的网络拓扑 

连接监视器拓扑通常是使用代理执行的“跟踪路由”命令的结果构建的，该命令主要获取从源到目标的所有跃点。
但是，如果任一源/目标位于 Azure 边界内，则会通过合并 2 个不同操作的结果来构建拓扑。
第一个结果显然是“跟踪路由”命令的结果。 第二个结果是一个内部命令（非常类似于 NW 的“下一个跃点诊断”工具）的结果，该命令基于 Azure 边界内的（客户）网络配置来识别逻辑路由。 由于后一个结果是合乎逻辑的，而前一个结果通常无法标识 Azure 边界中的任何跃点，因此合并的结果（几乎是 Azure 边界中的所有跃点）中几乎不会有跃点具有延迟值。

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor 中的指标

在连接监视器体验推出之前创建的连接监视器中，以下所有四个指标都可用：探测失败百分比、AverageRoundtripMs、ChecksFailedPercent 和 RoundTripTimeMs。 在连接监视器体验中创建的连接监视器中，数据只可用于 ChecksFailedPercent、RoundTripTimeMs 和“测试结果”指标。

指标按监视频率发出，描述某个连接监视器在特定时间的信息。 连接监视器指标还具有多个维度，例如 SourceName、DestinationName、TestConfiguration 和 TestGroup 等。这些维度可用于可视化特定的数据集，还可以在定义警报时以该数据集为目标。
目前，Azure 指标允许的最小粒度为 1 分钟，如果频率小于 1 分钟，则将显示聚合结果。

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="屏幕截图显示了连接监视器中的指标" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

使用指标时，将资源类型设置为“Microsoft.Network/networkWatchers/connectionMonitors”

| 指标 | Display name | 计价单位 | 聚合类型 | 说明 | 维度 |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent（经典） | 失败的探测百分比（经典） | 百分比 | 平均值 | 连接监视探测的失败百分比。<br>此指标仅适用于连接监视器经典版  | 无维度 |
| AverageRoundtripMs（经典） | 平均值往返时间（毫秒）（经典） | 毫秒 | 平均值 | 源和目标之间发送的连接监视探测的平均网络 RTT。<br>此指标仅适用于连接监视器经典版 |             无维度 |
| ChecksFailedPercent | 检查未通过百分比 | 百分比 | 平均值 | 测试的检查失败百分比。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>协议 <br>目的地地址 <br>DestinationName <br>DestinationResourceId <br>目标类型 <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>区域 <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| RoundTripTimeMs | 往返时间（毫秒） | 毫秒 | 平均值 | 在源和目标之间发送的用于检查的 RTT。 此值不是平均值。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>协议 <br>目的地地址 <br>DestinationName <br>DestinationResourceId <br>目标类型 <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>区域 <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| TestResult | 测试结果 | 计数 | 平均值 | 连接监视器测试结果 <br>结果值的解释如下所示： <br>0 - 不确定 <br>1 - 通过 <br>2 - 警告 <br>3 - 失败| SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>协议 <br>目的地地址 <br>DestinationName <br>DestinationResourceId <br>目标类型 <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |

#### <a name="metric-based-alerts-for-connection-monitor"></a>连接监视器基于指标的警报

可使用以下方法在连接监视器上创建指标警报 

1. 通过连接监视器：此方法在[使用 Azure 门户](connection-monitor-preview-create-using-portal.md#)创建连接监视器期间使用 
1. 通过连接监视器：使用仪表板中的“配置警报” 
1. 通过 Azure Monitor - 若要在 Azure Monitor 中创建警报： 
    1. 选择在连接监视器中创建的连接监视器资源。
    1. 确保“指标”显示为连接监视器的信号类型。
    1. 在“添加条件”中，选择“ChecksFailedPercent”或“RoundTripTimeMs”作为“信号名称”   。
    1. “信号类型”选择“指标” 。 例如，选择“ChecksFailedPercent”。
    1. 将列出指标的所有维度。 选择维度名称和维度值。 例如，选择“源地址”，然后在连接监视器中输入任何源的 IP 地址。
    1. 在“警报逻辑”中填写以下详细信息：
        * 条件类型：静态。
        * 条件和阈值 。
        * 计算的聚合粒度和计算频率：连接监视器每分钟更新一次数据。
    1. 在“操作”中选择操作组。
    1. 提供警报详细信息。
    1. 创建警报规则。

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="显示 Azure Monitor 中的“创建规则”区域的屏幕截图。突出显示了“源地址”和“源终结点名称”" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>诊断网络中的问题

连接监视器可以帮助你诊断连接监视器中的问题和网络中的问题。 混合网络中的问题通过你之前安装的 Log Analytics 代理进行检测。 Azure 中的问题由网络观察程序扩展进行检测。 

可以在网络拓扑的 Azure 网络中查看问题。

对于源为本地 VM 的网络，可以检测到以下问题：

* 请求已超时。
* DNS 未解析终结点 - 暂时或永久。 URL 无效。
* 无法找到主机。
* 源无法连接到目标。 无法通过 ICMP 访问目标。
* 与证书相关的问题： 
    * 验证代理所需的客户端证书。 
    * 无法访问证书重定位列表。 
    * 终结点的主机名与证书的使用者或使用者备用名称不匹配。 
    * 源的本地计算机受信任的证书颁发机构存储中缺少根证书。 
    * SSL 证书已过期、无效、吊销或不兼容。

针对其源为Azure VM 的网络，可以检测到以下问题：

* 代理问题：
    * 代理已停止。
    * DNS 解析失败。
    * 无应用程序或在目标端口上侦听的侦听器。
    * 无法打开套接字。
* VM 状态问题： 
    * 正在启动
    * 正在停止
    * 已停止
    * 正在解除分配
    * 已解除分配
    * 正在重新启动
    * 未分配
* 缺少 ARP 表条目。
* 由于本地防火墙问题或 NSG 规则，流量遭到阻止。
* 虚拟网络网关问题： 
    * 缺少路由。
    * 两个网关之间的隧道已断开连接或处于缺失状态。
    * 第二个网关没有让隧道发现。
    * 无法找到对等信息。
> [!NOTE]
> 如果有 2 个连接的网关，并且其中一个网关与源终结点不在同一个区域，则 CM 会在拓扑视图中将其标识为“未学习路由”。 连接不受影响。 这是一个已知问题，正在进行修复。 
* Microsoft Edge 中缺少路由。
* 由于系统路由或 UDR 的原因，流量已停止。
* 网关连接上未启用 BGP。
* 已关闭负载均衡器上的 DIP 探测。

## <a name="comparison-between-azures-connectivity-monitoring-support"></a>Azure 连接监视支持之间的比较 

只需单击一下，无需停机，即可将测试从网络性能监视器和连接监视器（经典）迁移到经过改进的新连接监视器。
 
迁移将有助于生成以下结果：

* 代理和防火墙设置按原样工作。 不需要进行任何更改。 
* 现有的连接监视器已映射到“连接监视器”>“测试组”>“测试格式”。 通过选择“编辑”，可以查看和修改新连接监视器的属性，下载模板以对连接监视器进行更改，然后通过 Azure 资源管理器提交。 
* 具有网络观察程序扩展的 Azure 虚拟机将数据发送到工作区和指标。 连接监视器通过新指标（ChecksFailedPercent 和 RoundTripTimeMs）而不是旧指标（ProbesFailedPercent 和 AverageRoundtripMs）提供数据。 旧指标将迁移到新指标，如 > ProbesFailedPercent -> ChecksFailedPercent 和 AverageRoundtripMs -> RoundTripTimeMs。
* 数据监视：
   * **警报**：自动迁移到新指标。
   * **仪表板和集成**：需要手动编辑指标集。 
   
从网络性能监视器和连接监视器（经典）迁移到连接监视器可出于多种原因。 下面是一些用例，展示了 Azure 的连接监视器与网络性能监视器和连接监视器（经典）之间的性能比较。 

 | 功能  | 网络性能监视器 | 连接监视器（经典） | 连接监视器 |
 | -------  | --------------------------- | -------------------------- | ------------------ | 
 | Azure 和混合监视的统一体验 | 不可用 | 不可用 | 可用 |
 | 跨订阅、跨区域、跨工作区监视 | 支持跨订阅和跨区域监视但不支持跨工作区监视 | 不可用 | 支持跨订阅和跨工作区监视；Azure 代理具有区域边界  |
 | 集中式工作区支持 |  不可用 | 不可用   | 可用 |
 | 多个源可以 ping 多个目标 | 性能监视允许多个源对多个目标发出 ping 命令，服务连接监视允许多个源对单个服务/URL 发出 ping 命令，而 Express Route 允许多个源对多个目标发出 ping 命令 | 不可用 | 可用 |
 | 跨本地、Internet 跃点和 Azure 的统一拓扑 | 不可用 | 不可用 | 可用 |
 | HTTP 状态代码检查 | 不可用  | 不可用 | 可用 |
 | 连接诊断 | 不可用 | 可用 | 可用 |
 | 复合资源 - VNET、子网和本地自定义网络 | 性能监视支持子网、本地网络和逻辑网络组，而服务连接监视和 Express Route 仅支持本地和 Azure 代理 | 不可用 | 可用 |
 | 连接指标和维度度量 |   不可用 | 丢失、延迟、RTT | 可用 |
 | 自动化 - PS/CLI/Terraform | 不可用 | 可用 | 可用 |
 | 对 Linux 的支持 | 性能监视支持 Linux，而服务连接监视器和 Express Route 不支持 Linux | 可用 | 可用 |
 | 对公有云、政府云、Mooncake 和气隙云的支持 | 可用 | 可用 | 可用|


## <a name="faq"></a>常见问题解答

### <a name="are-classic-vms-supported"></a>是否支持经典 VM？
不支持，连接监视器不支持经典 VM。 建议将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器，因为经典资源将[弃用](../virtual-machines/classic-vm-deprecation.md)。 请参阅本文了解[如何迁移](../virtual-machines/migration-classic-resource-manager-overview.md)。

### <a name="my-topology-is-not-decorated-or-my-hops-have-missing-information"></a>我的拓扑未经修饰或我的跃点缺少信息？
从非 Azure 到 Azure，只有在目标 Azure 资源和连接监视器资源位于同一区域的情况下才能修饰拓扑 

### <a name="my-connection-monitor-creation-is-failing-with-error-we-dont-allow-creating-different-endpoints-for-the-same-vm"></a>我创建连接监视器失败了，出现“我们不允许为同一 VM 创建不同的终结点”错误。
同一 Azure VM 不能用于同一连接监视器中的不同配置。 例如，不支持在同一连接监视器中将同一 VM 用于有筛选器的情况和没有筛选器的情况。

### <a name="the-test-failure-reason-is-nothing-to-display"></a>测试失败的原因是“没有要显示的内容”？
在拓扑发现或跃点探索过程中，发现了连接监视器仪表板上显示的问题。 可能会有这样的情况：突破了为丢失百分比或 RTT 设置的阈值，但未在跃点上发现问题。

### <a name="while-migrating-existing-connection-monitor-classic-to-connection-monitor-the-external-endpoint-tests-are-being-migrated-with-tcp-protocol-only"></a>将现有连接监视器（经典）迁移到连接监视器时，是否仅使用 TCP 协议迁移外部终结点测试？ 
连接监视器（经典）中无法选择协议。 因此，客户无法在连接监视器（经典）中指定使用 HTTP 协议连接到外部终结点。
所有测试在连接监视器（经典）中都只有 TCP 协议，这就是迁移时我们在连接监视器的测试中创建 TCP 配置的原因。 

## <a name="next-steps"></a>后续步骤
    
   * 了解[如何使用 Azure 门户创建连接监视器](./connection-monitor-create-using-portal.md)  
   * 了解[如何使用 ARMClient 创建连接监视器](./connection-monitor-create-using-template.md)
