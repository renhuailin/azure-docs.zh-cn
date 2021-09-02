---
title: 为 Azure ExpressRoute 配置连接监视器
description: 为 Azure ExpressRoute 线路配置基于云的网络连接监视。 这包括通过 ExpressRoute 专用对等互连和 Microsoft 对等互连进行监视。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/28/2021
ms.author: duau
ms.openlocfilehash: 794e841d8d50683374c86301ec55bc4f6ab53ec6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778614"
---
# <a name="configure-connection-monitor-for-expressroute"></a>为 ExpressRoute 配置连接监视器

本文可帮助配置连接监视器扩展以监视 ExpressRoute。 连接监视器是基于云的网络监视解决方案，用于监视 Azure 云部署和本地位置（分支机构等）之间的连接。 连接监视器属于 Azure Monitor 日志。  该扩展还允许监视专用和 Microsoft 对等互连连接的网络连接。 为 ExpressRoute 配置连接监视器后，可以检测需要识别和消除的网络问题。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

可以使用 ExpressRoute 连接监视器：

* 跨各种 VNet 监视数据丢失和延迟情况并设置警报。

* 监视网络上的所有路径（包括冗余路径）。

* 对难以复制且在特定时间点出现的暂时性网络问题进行故障排除。

* 帮助确定网络上导致性能下降的特定分段。

## <a name="workflow"></a><a name="workflow"></a> 工作流

监视本地和 Azure 中的多个服务器上安装的代理。 代理相互通信，发送 TCP 握手数据包。 通过代理间的通信，Azure 可以映射流量可能经过的网络拓扑和路径。

1. 创建 Log Analytics 工作区。

1. 安装和配置软件代理。 （如果只想通过 Microsoft 对等互连进行监视，则无需安装和配置软件代理。）：

    * 在本地服务器和 Azure VM 上安装监视代理（用于专用对等互连）。
    * 在监视代理服务器上配置设置，允许监视代理进行通信。 （打开防火墙端口等）

1. 配置网络安全组 (NSG) 规则，允许 Azure VM 上安装的监视代理与本地监视代理进行通信。

1. 在订阅上启用网络观察程序。

1. 设置监视：创建包含测试组的连接监视器，以监视网络中的源终结点和目标终结点。

如已使用网络性能监视器（已弃用）或连接监视器来监视其他对象或服务，并且已在某个受支持的区域内拥有 Log Analytics 工作区， 则可以跳过步骤 1 和步骤 2，并在步骤 3 开始配置。

## <a name="create-a-workspace"></a><a name="configure"></a> 创建工作区

在具有连接到 ExpressRoute 线路的 VNet 链路的订阅中创建一个工作区。

1. 登录 [Azure 门户](https://portal.azure.com)。 从连接到 ExpressRoute 线路的虚拟网络的订阅中选择“+ 创建资源”。 搜索 Log Analytics 工作区，然后选择“创建”。

   >[!NOTE]
   >可以创建新的工作区或使用现有的工作区。 如果想要使用现有工作区，则必须确保工作区已迁移到新的查询语言。 [详细信息...](../azure-monitor/logs/log-query-overview.md)
   >

    :::image type="content" source="./media/how-to-configure-connection-monitor/search-log-analytics.png" alt-text="在“创建资源”中搜索 Log Analytics 的屏幕截图。":::

1. 通过输入或选择以下信息来创建工作区。  

    | 设置 | 值 |
    | -------- | ----- |
    | 订阅 | 选择具有 ExpressRoute 线路的订阅。 |
    | 资源组 | 创建一个新资源组或选择现有资源组。 |
    | 名称 | 输入名称以标识此工作区。 |
    | 区域 | 选择将在其中创建此工作区的区域。 |

    :::image type="content" source="./media/how-to-configure-connection-monitor/create-workspace-basic.png" alt-text="用于创建 Log Analytics 工作区的基本选项卡的屏幕截图。":::

     >[!NOTE]
     >ExpressRoute 线路可以位于世界的任何位置。 它不一定要在工作区所在的同一区域。
     >

1. 选择“查看和创建”进行验证，然后选择“创建”以部署工作区。  部署工作区后，请继续下一部分来配置监视解决方案。

## <a name="configure-monitoring-solution"></a><a name="npm"></a>配置监视解决方案

完成下方的 Azure PowerShell 脚本，方法是替换 $SubscriptionId、$location、$resourceGroup 和 $workspaceName 的值。    然后运行该脚本以配置监视解决方案。

```azurepowershell-interactive
$subscriptionId = "Subscription ID should come here"
Select-AzSubscription -SubscriptionId $subscriptionId

$location = "Workspace location should come here"
$resourceGroup = "Resource group name should come here"
$workspaceName = "Workspace name should come here"

$solution = @{
    Location          = $location
    Properties        = @{
        workspaceResourceId = "/subscriptions/$($subscriptionId)/resourcegroups/$($resourceGroup)/providers/Microsoft.OperationalInsights/workspaces/$($workspaceName)"
    }
    Plan              = @{
        Name          = "NetworkMonitoring($($workspaceName))" 
        Publisher     = "Microsoft"
        Product       = "OMSGallery/NetworkMonitoring"
        PromotionCode = ""
    }
    ResourceName      = "NetworkMonitoring($($workspaceName))" 
    ResourceType      = "Microsoft.OperationsManagement/solutions" 
    ResourceGroupName = $resourceGroup
}

New-AzureRmResource @solution -Force
```

配置监视解决方案后， 继续执行下一步，即在服务器上安装和配置监视代理。

## <a name="install-and-configure-agents-on-premises"></a><a name="agents"></a>在本地安装和配置代理

### <a name="download-the-agent-setup-file"></a><a name="download"></a>下载代理安装程序文件

1. 导航至“Log Analytics 工作区”，并选择“设置”下的“代理管理”。  下载与计算机的操作系统相对应的代理。

    :::image type="content" source="./media/how-to-configure-connection-monitor/download-agent.png" alt-text="工作区中代理管理页的屏幕截图。":::

1. 接下来，将“工作区 ID”和“主密钥”复制到记事本。

    :::image type="content" source="./media/how-to-configure-connection-monitor/copy-id-key.png" alt-text="工作区 ID 和主密钥的屏幕截图。":::

1. 对于 Windows，请使用管理员权限在 PowerShell 窗口下载并运行此 PowerShell 脚本 [EnableRules.ps1](https://aka.ms/npmpowershellscript)。 PowerShell 脚本将打开与 TCP 事务相关的防火墙端口。 
 
    对于 Linux 计算机，需要按照以下步骤手动更改端口号：

    * 导航至路径：/var/opt/microsoft/omsagent/npm_state。
    * 打开文件：npmdregistry
    * 更改端口号 `PortNumber:<port of your choice>` 的值

### <a name="install-log-analytics-agent-on-each-monitoring-server"></a><a name="installagentonprem"></a>在每个监视服务器上安装 Log Analytics 代理

建议在 ExpressRoute 连接两侧的至少两台服务器上安装 Log Analytics 代理，以实现冗余。 例如，本地和 Azure 虚拟网络。 使用以下步骤安装代理：

1. 请选择下面相应的操作系统，获取在服务器上安装 Log Analytics 代理的步骤。

    * [Windows](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)
    * [Linux](../azure-monitor/agents/agent-linux.md)

1. 完成后，Microsoft Monitoring Agent 将显示在“控制面板”中。 可以查看配置并[验证代理是否已连接到](../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor) Azure Monitor 日志。

1. 针对要用于监视的其他本地计算机重复步骤 1 和 2。

### <a name="install-network-watcher-agent-on-each-monitoring-server"></a><a name="installagentazure"></a>在每个监视服务器上安装网络观察程序代理

#### <a name="new-azure-virtual-machine"></a>新 Azure 虚拟机

如果要创建新的 Azure VM 来监视 VNet 的连接，可以在[创建 VM](../network-watcher/connection-monitor.md#create-the-first-vm) 时安装网络观察程序代理。

#### <a name="existing-azure-virtual-machine"></a>现有 Azure 虚拟机

如果使用现有 VM 来监视连接，可以分别安装适用于 [Linux](../virtual-machines/extensions/network-watcher-linux.md) 和 [Windows](../virtual-machines/extensions/network-watcher-windows.md) 的网络代理。

### <a name="open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>打开监视代理服务器上的防火墙端口

用于防火墙的规则可以阻止源服务器与目标服务器之间的通信。 连接监视器检测到此问题并将其显示为拓扑中的诊断消息。 若要启用连接监视，请确保防火墙规则允许在源和目标之间使用 TCP 或 ICMP 上的数据包。

#### <a name="windows"></a>Windows

对于 Windows 计算机，可以运行 PowerShell 脚本，以创建连接监视器所需的注册表项。 此脚本还会创建 Windows 防火墙规则，允许监视代理创建彼此之间的 TCP 连接。 该脚本创建的注册表项指定是否记录调试日志和该日志文件的路径。 还会定义用于通信的代理 TCP 端口。 该脚本会自动设置这些注册表项的值。 请勿手动更改这些密钥。

8084 端口默认打开。 通过向该脚本提供参数“portNumber”即可使用自定义端口。 但是，如果这样做，则必须为运行脚本的所有服务器指定同一端口。

>[!NOTE]
>“EnableRules”PowerShell 脚本仅在运行该脚本的服务器上配置 Windows 防火墙规则。 如果有网络防火墙，应确保该防火墙允许流量去往连接监视器正在使用的 TCP 端口。
>

在代理服务器上，使用管理权限打开 PowerShell 窗口。 运行 [EnableRules](https://aka.ms/npmpowershellscript) PowerShell 脚本（之前已下载）。 不要使用任何参数。

:::image type="content" source="./media/how-to-configure-connection-monitor/enable-rules-script.png" alt-text="在 PowerShell 窗口中运行启用规则脚本的屏幕截图。":::

#### <a name="linux"></a>Linux

对于 Linux 计算机，需要手动更改要使用的端口号：

1. 导航至路径：/var/opt/microsoft/omsagent/npm_state。
1. 打开文件：npmdregistry
1. 更改端口号 `PortNumber:\<port of your choice\>` 的值。 在工作区使用的所有代理中，所用的端口号都应该相同

## <a name="configure-network-security-group-rules"></a><a name="opennsg"></a>配置网络安全组规则

若要监视 Azure 中的服务器，必须配置网络安全组 (NSG) 规则，以允许连接监视器的 TCP 或 ICMP 流量。 默认端口为 **8084，该端口允许安装在 Azure VM 上的监视代理与本地监视代理通信。

有关 NSG 的详细信息，请参阅有关[筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)的教程。

> [!NOTE]
> 请确保已安装代理（包括本地服务器代理和 Azure 服务器代理），并且在执行此步骤前已运行 PowerShell 脚本。
>

## <a name="enable-network-watcher"></a><a name="enablenetworkwatcher"></a>启用网络观察程序

使用网络观察程序启用具有虚拟网络的所有订阅。 确保未明确禁用订阅的网络观察程序。 有关详细信息，请参阅[启用网络观察程序](../network-watcher/network-watcher-create.md)。

## <a name="create-a-connection-monitor"></a><a name="createcm"></a>创建连接监视器

若要大致了解如何在网络中跨源终结点和目标终结点创建连接监视器、测试和测试组，请参阅[创建连接监视器](../network-watcher/connection-monitor-create-using-portal.md)。 使用以下步骤为专用对等互连和 Microsoft 对等互连配置连接监视。

1. 在 Azure 门户中导航至“网络观察程序”资源，并选择“监控”下的“连接监视器”。  然后选择“创建”，以创建新的连接监视器。

    :::image type="content" source="./media/how-to-configure-connection-monitor/create-connection-monitor.png" alt-text="网络观察程序中的连接监视器的屏幕截图。":::

1. 在创建工作流的“基本信息”选项卡上，为“区域”字段选择与部署 Log Analytics 工作区相同的区域。 对于“工作区配置”，请选择之前创建的现有 Log Analytics 工作区。 然后，选择“下一步：测试组 >>”。

    :::image type="content" source="./media/how-to-configure-connection-monitor/connection-monitor-basic.png" alt-text="用于创建连接监视器的基本选项卡的屏幕截图。":::

1. 在“添加测试组详细信息”页上，添加测试组的源终结点和目标终结点。 还将在两者之间设置测试配置。 为此测试组输入一个名称。

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-group-details.png" alt-text="添加测试组详细信息页的屏幕截图。":::

1. 选择“添加源”，并导航至“非 Azure 终结点”选项卡。 选择安装有 Log Analytics 代理的本地资源以监视连接，然后选择“添加终结点”。

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-source-endpoints.png" alt-text="添加源终结点的屏幕截图。":::

1. 接下来，选择“添加目标”。 

    若要监视通过 ExpressRoute 专用对等互连监视连接，请导航至“Azure 终结点”选项卡。 选择安装有网络观察程序代理的 Azure 资源，以监视到 Azure 中虚拟网络的连接。 请确保在 IP 列中选择每个此类资源的专用 IP 地址。 选择“添加终结点”，将这些终结点添加到测试组的目标列表中。
    
    :::image type="content" source="./media/how-to-configure-connection-monitor/add-destination-endpoints.png" alt-text="添加 Azure 目标终结点的屏幕截图。":::

    若要通过 ExpressRoute Microsoft 对等互连监视连接，请导航至“外部地址”选项卡。 选择希望通过 Microsoft 对等互连监视连接的 Microsoft 服务终结点。 选择“添加终结点”，将这些终结点添加到测试组的目标列表中。

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-external-destination-endpoints.png" alt-text="添加外部目标终结点的屏幕截图。":::

1. 现在选择“添加测试配置”。 针对协议选择 TCP，并输入服务器上打开的目标端口。  然后，为失败的检查和往返时间配置测试频率和阈值。  然后选择“添加测试配置”。

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-configuration.png" alt-text="添加测试配置页的屏幕截图。":::

1. 添加源、目标和测试配置后，选择“添加测试组”。

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-group-details-configured.png" alt-text="配置了“添加测试组详细信息”的屏幕截图。" lightbox="./media/how-to-configure-connection-monitor/add-test-group-details-configured-expanded.png":::

1. 若想创建警报，请选择“下一步：创建警报 >>”。 完成后，选择“查看和创建”，然后选择“创建”。 

## <a name="view-results"></a>查看结果

1. 进入“网络观察程序”资源，并选择“监控”下的“连接监视器”。  应在 5 分钟后看到新的连接监视器。 若要查看连接监视器的网络拓扑和性能图表，请从测试组下拉列表中选择测试。

    :::image type="content" source="./media/how-to-configure-connection-monitor/overview.png" alt-text="连接监视器概述页的屏幕截图。" lightbox="./media/how-to-configure-connection-monitor/overview-expanded.png":::

1. 在“性能分析”面板中，可以查看失败检查的百分比和往返时间的每个测试结果。 可以通过选择面板顶部的下拉列表来调整所显示数据的时间范围。

    :::image type="content" source="./media/how-to-configure-connection-monitor/performance-analysis.png" alt-text="性能分析面板的屏幕截图。" lightbox="./media/how-to-configure-connection-monitor/performance-analysis-expanded.png":::

1. 关闭“性能分析”面板将显示所选源终结点与目标终结点之间的连接监视器检测到的网络拓扑。 此视图显示源终结点和目标终结点之间的双向流量路径。 在数据包到达 Microsoft 边缘网络之前，还可以查看数据包的逐跳延迟。 

    :::image type="content" source="./media/how-to-configure-connection-monitor/topology.png" alt-text="连接监视器中的网络拓扑的屏幕截图。" lightbox="./media/how-to-configure-connection-monitor/topology-expanded.png":::

    选择拓扑视图中的任何跃点将显示有关该跃点的其他信息。 连接监视器检测到的有关跃点的任何问题也会在此显示。

    :::image type="content" source="./media/how-to-configure-connection-monitor/hop-details.png" alt-text="网络跃点详细信息的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

详细了解[监视 Azure ExpressRoute](monitor-expressroute.md)