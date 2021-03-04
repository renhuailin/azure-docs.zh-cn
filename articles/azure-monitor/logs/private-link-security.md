---
title: 使用 Azure 专用链接将网络安全地连接到 Azure Monitor
description: 使用 Azure 专用链接将网络安全地连接到 Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 65af5810152034fd7b6014041edd07835eebd194
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101471"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>使用 Azure 专用链接将网络安全地连接到 Azure Monitor

通过 [Azure 专用链接](../../private-link/private-link-overview.md)，可使用专用终结点将 Azure PaaS 服务安全地链接到你的虚拟网络。 对于很多服务，一个资源只需设置一个终结点即可。 不过，Azure Monitor 是不同互连服务的集合，它们相互协同，共同监视你的工作负载。 因此，我们构建了一个名为 Azure Monitor 专用链接范围 (AMPLS) 的资源。 AMPLS 允许定义监视网络的边界并连接到虚拟网络。 本文介绍何时使用以及如何设置 Azure Monitor 专用链接范围。

## <a name="advantages"></a>优势

借助专用链接，你可以：

- 以专用方式连接到 Azure Monitor，无需开放任何公共网络访问权限
- 确保仅可通过授权的专用网络访问你正在监视的数据
- 定义通过你的专用终结点连接的特定 Azure Monitor 资源，从而防止你的专用网络出现数据泄露的情况
- 使用 ExpressRoute 和专用链接将你的专用本地网络安全地连接到 Azure Monitor
- 将所有流量保留在 Microsoft Azure 主干网络中

有关详细信息，请参阅[专用链接的主要优势](../../private-link/private-link-overview.md#key-benefits)。

## <a name="how-it-works"></a>工作原理

Azure Monitor 专用链接范围 (AMPLS) 将专用终结点连接 (，并将它们包含在) 中的 Vnet 连接到一个或多个 Azure Monitor 资源-Log Analytics 工作区和 Application Insights 组件。

![基本资源拓扑示意图](./media/private-link-security/private-link-basic-topology.png)

* VNet 中的专用终结点允许它通过网络池中的专用 Ip 访问 Azure Monitor 终结点，而不是使用这些终结点的公共 Ip。 这样，你就可以继续使用 Azure Monitor 资源，而无需打开 VNet 来 unrequired 出站流量。 
* 从专用终结点到你的 Azure Monitor 资源的流量将通过 Microsoft Azure 主干，而不会路由到公共网络。 
* 可以配置每个工作区或组件，以允许或拒绝从公共网络引入和查询。 这提供了资源级保护，使你可以控制对特定资源的流量。

> [!NOTE]
> 一个 Azure Monitor 资源可属于多个 AMPLS，但无法将一个 VNet 连接到多个 AMPLS。 

## <a name="planning-your-private-link-setup"></a>规划专用链接设置

设置 Azure Monitor 专用链接之前，请考虑网络拓扑，并特别是 DNS 路由拓扑。 

### <a name="the-issue-of-dns-overrides"></a>DNS 覆盖的问题
某些 Azure Monitor 服务使用全局终结点，这意味着它们会针对任何工作区/组件提供请求。 几个示例是 Application Insights 摄取终结点，以及 Application Insights 和 Log Analytics 的查询终结点。

设置专用链接连接时，会更新 DNS，以便将终结点 Azure Monitor 终结点映射到 VNet IP 范围内的专用 IP 地址。 此更改将覆盖这些终结点的任何先前映射，这些终结点可能有意义的含义，如下所示。 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor 专用链接适用于所有 Azure Monitor 资源-它是全部或无
由于某些 Azure Monitor 终结点是全局的，因此无法为特定组件或工作区创建专用链接连接。 相反，将专用链接设置为单个 Application Insights 组件时，将更新 **所有** Application Insights 组件的 DNS 记录。 尝试引入或查询组件会经历专用链接，并且可能会失败。 同样，将专用链接设置为单个工作区将导致所有 Log Analytics 查询通过专用链接查询终结点 (但不会引入请求，后者) 特定于工作区的终结点。

![单个 VNet 中的 DNS 覆盖示意图](./media/private-link-security/dns-overrides-single-vnet.png)

这不仅适用于特定的 VNet，还适用于共享同一 DNS 服务器的所有 Vnet (请参阅 [DNS 覆盖](#the-issue-of-dns-overrides)) 的问题。 例如，将日志引入任何 Application Insights 组件的请求始终通过专用链接路由发送。 未链接到 AMPLS 的组件将无法通过专用链接验证而不会通过。

> [!NOTE]
> 结束：将专用链接连接到单个资源后，它将应用于网络中的所有 Azure Monitor 资源-这是全部或全部。 这实际上意味着，你应该将网络中的所有 Azure Monitor 资源添加到 AMPLS，或者不添加任何资源。

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor 专用链接适用于整个网络
某些网络由多个 Vnet 组成。 如果 Vnet 使用相同的 DNS 服务器，则它们将覆盖彼此的 DNS 映射，并可能会中断彼此与 Azure Monitor 的通信 (请参阅 [DNS 覆盖) 问题](#the-issue-of-dns-overrides) 。 最终，只有最后一个 VNet 才能与 Azure Monitor 通信，因为 DNS 会将 Azure Monitor 终结点映射到此 Vnet 范围内的专用 Ip (这可能无法从其他 Vnet) 访问。

![多个 Vnet 中的 DNS 覆盖示意图](./media/private-link-security/dns-overrides-multiple-vnets.png)

在上面的关系图中，VNet 10.0.1 版首先连接到 AMPLS1，并将 Azure Monitor 的全局终结点映射到其范围内的 Ip。 稍后，VNet 10.0.2 连接到 AMPLS2，并重写其范围内具有 Ip 的 *相同全局终结点* 的 DNS 映射。 由于这些 Vnet 不对等互连，因此第一个 VNet 现在无法访问这些终结点。

> [!NOTE]
> 结束： AMPLS 安装程序会影响共享同一 DNS 区域的所有网络。 若要避免重写彼此的 DNS 终结点映射，最好在对等互连网络 (如集线器 VNet) 上设置单个专用终结点，或者使用 DNS 转发器或完全) 的单独 DNS 服务器将 DNS 级别的网络隔离 (foe 示例。

### <a name="hub-spoke-networks"></a>中心辐射型网络
中心辐射型拓扑可以通过在主) VNet (上设置一个专用链接来避免 DNS 覆盖的问题，而不是分别为每个 VNet 设置专用链接。 这种设置特别适用于分支 Vnet 使用的 Azure Monitor 资源是否共享。 

![中心辐射型-单 PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> 你可能会有意为你的辐射 Vnet 创建单独的专用链接，例如，允许每个 VNet 访问一组有限的监视资源。 在这种情况下，可以为每个 VNet 创建专用的专用终结点和 AMPLS，但也必须验证它们不共享相同的 DNS 区域，以免 DNS 覆盖。


### <a name="consider-limits"></a>考虑限制

如 [限制和限制](#restrictions-and-limitations)中所列，AMPLS 对象具有多个限制，如下图所示：
* 每个 VNet 仅连接到 **1** 个 AMPLS 对象。
* AMPLS B 连接到两个 Vnet (VNet2 和 VNet3) 的专用终结点，并使用10个可能的专用终结点连接中的2个。
* AMPLS A 连接到两个工作区和一个 Application insights 组件，使用50可能 Azure Monitor 资源连接中的3种。
* Workspace2 使用5个可能 AMPLS 连接中的2个连接到 AMPLS A 和 AMPLS B。

![AMPLS 限制关系图](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>示例连接

首先创建一个 Azure Monitor 专用链接范围资源。

1. 转到 Azure 门户中的“创建资源”，并搜索“Azure Monitor 专用链接范围” 。

   ![查找 Azure Monitor 专用链接范围](./media/private-link-security/ampls-find-1c.png)

2. 选择“创建”。
3. 选择一个订阅和资源组。
4. 为 AMPLS 指定名称。 最好使用有意义且清晰的名称，如 "AppServerProdTelem"。
5. 选择“查看 + 创建”。 

   ![创建 Azure Monitor 专用链接范围](./media/private-link-security/ampls-create-1d.png)

6. 让验证通过，然后选择 " **创建**"。

### <a name="connect-azure-monitor-resources"></a>连接 Azure Monitor 资源

将 Azure Monitor 资源 (Log Analytics 工作区和 Application Insights) 组件连接到 AMPLS。

1. 在 Azure Monitor "专用链接范围" 中，选择左侧菜单中的 " **Azure Monitor 资源** "。 选择“添加”按钮。
2. 添加工作区或组件。 选择 " **添加** " 按钮会显示一个对话框，可在其中选择 Azure Monitor 资源。 你可浏览订阅和资源组，也可键入其名称通过筛选找到它们。 选择工作区或组件，然后选择 " **应用** "，将其添加到你的作用域。

    ![显示选择范围 UX 的屏幕截图](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> 删除 Azure Monitor 资源需要首先将其连接到它们连接到的任何 AMPLS 对象。 不能删除连接到 AMPLS 的资源。

### <a name="connect-to-a-private-endpoint"></a>连接到专用终结点

你现已将资源连接到 AMPLS，接下来可创建一个专用终结点来连接我们的网络。 可在 [Azure 门户专用链接中心](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)或 Azure Monitor 专用链接范围中执行此任务，如下例所示。

1. 在作用域资源中，选择左侧资源菜单中的 " **专用终结点连接** "。 选择 " **专用终结点** " 以启动终结点创建进程。 你还可以通过选择并选择 " **批准**"，来批准在专用链接中心启动的连接。

    ![专用终结点连接 UX 的屏幕截图](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. 选择订阅、资源组、终结点的名称及其应位于的区域。 区域必须与连接到的 VNet 位于同一区域。

3. 在完成时选择“下一步:资源”。 

4. 在“资源”屏幕中，

   a. 选择包含你的 Azure Monitor 专用范围资源的订阅。 

   b. 对于“资源类型”，选择“Microsoft.insights/privateLinkScopes” 。 

   c. 从“资源”下拉列表中，选择你之前创建的专用连接范围。 

   d. 选择 **下一步：配置 >**。
      ![显示选择“创建专用终结点”的屏幕截图](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. 在“配置”窗格中，

   a.    选择要连接到 Azure Monitor 资源的虚拟网络和子网 。 
 
   b.    针对“与专用 DNS 区域集成”选择“是”，让它自动新建一个专用 DNS 区域 。 实际 DNS 区域可能不同于下面屏幕截图中显示的内容。 
   > [!NOTE]
   > 如果你选择 " **否** "，并且喜欢手动管理 DNS 记录，请首先完成设置专用链接-包括此专用终结点和 AMPLS 配置。 然后，根据 [Azure 专用终结点 DNS 配置](../../private-link/private-endpoint-dns.md)中的说明配置 DNS。 切勿在准备专用链接设置时创建空记录。 你创建的 DNS 记录会替代现有设置，并且会影响与 Azure Monitor 的连接。
 
   c.    选择“查看 + 创建”。
 
   d.    让验证通过。 
 
   e.    选择“创建”  。 

    ![选择专用终结点详细信息的屏幕截图。](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

你现在已创建了一个连接到此 AMPLS 的新专用终结点。

## <a name="review-and-validate-your-private-link-setup"></a>查看并验证专用链接设置

### <a name="reviewing-your-endpoints-dns-settings"></a>查看终结点的 DNS 设置
你创建的专用终结点现在应配置四个 DNS 区域：

[![专用终结点 DNS 区域的屏幕截图。](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-azure-com
* privatelink-opinsights-azure-com
* privatelink-opinsights-azure com
* privatelink-agentsvc-net

> [!NOTE]
> 其中每个区域将特定 Azure Monitor 终结点映射到 VNet 的 Ip 池中的专用 Ip。 以下图像中的 IP 地址 showns 只是示例。 你的配置应改为显示你自己的网络中的专用 Ip。

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-azure-com
此区域涵盖 Azure Monitor 使用的全局终结点，这意味着，这些终结点为考虑所有资源而不是特定资源的请求提供服务。 此区域应为映射终结点：
* `in.ai` - (Application Insights 引入终结点，你将看到一个全局和区域条目
* `api` -Application Insights 和 Log Analytics API 终结点
* `live` -Application Insights 实时指标终结点
* `profiler` -Application Insights 探查器终结点
* `snapshot`-Application Insights 快照终结点[ ![ 专用 DNS 区域监视器的屏幕截图-azure com。](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-opinsights-azure-com
此区域涵盖特定于工作区的映射到 OMS 终结点。 应该会看到链接到 AMPLS 与此专用终结点连接的每个工作区的条目。
[![专用 DNS 区域 opinsights 的屏幕截图。](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-opinsights-azure com
此区域涵盖特定于工作区的到 ODS 终结点的映射-Log Analytics 的引入终结点。 应该会看到链接到 AMPLS 与此专用终结点连接的每个工作区的条目。
[![专用 DNS 区域 opinsights 的屏幕截图。](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-net
此区域涵盖特定于工作区的映射到代理服务自动化终结点。 应该会看到链接到 AMPLS 与此专用终结点连接的每个工作区的条目。
[![专用 DNS 区域代理 svc 的屏幕截图-azure-自动化-网络。](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

### <a name="validating-you-are-communicating-over-a-private-link"></a>验证是否通过专用链接进行通信
* 若要验证你的请求现在是通过专用终结点和专用 IP 映射终结点发送的，你可以通过网络跟踪（甚至是你的浏览器）来查看这些请求。 例如，尝试查询你的工作区或应用程序时，请确保将请求发送到映射到 API 终结点的专用 IP，在此示例中为 *172.17.0.9*。

    注意：某些浏览器可能会使用其他 DNS 设置 (请参阅 [BROWSER DNS settings](#browser-dns-settings)) 。 请确保应用 DNS 设置。

* 若要确保你的工作区或组件没有收到来自公共网络的请求 (未通过 AMPLS) 连接，请将资源的公共引入和查询标志设置为 " *否* "，如 [从专用链接范围外管理访问](#manage-access-from-outside-of-private-links-scopes)中所述。

* 从受保护网络上的客户端使用 `nslookup` 到 DNS 区域中列出的任何终结点。 它应由 DNS 服务器解析为映射的专用 Ip，而不是默认使用的公共 Ip。


## <a name="configure-log-analytics"></a>配置 Log Analytics

转到 Azure 门户。 在 Log Analytics 工作区资源菜单中，左侧有一项名为 " **网络隔离** " 的项。 你可通过此菜单控制两种不同的状态。

![LA 网络隔离](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>已连接 Azure Monitor 专用链接范围
连接到工作区的所有作用域都显示在此屏幕中。  (AMPLSs) 连接到作用域后，便可以从虚拟网络连接到每个 AMPLS 的网络流量到达此工作区。 通过此处创建连接与在作用域上设置连接与在 [连接 Azure Monitor 资源](#connect-azure-monitor-resources)时相同。 若要添加新连接，请选择 " **添加** "，然后选择 "Azure Monitor" 专用链接范围。 选择 " **应用** " 来连接它。 请注意，工作区可以连接到5个 AMPLS 对象，如 [限制和限制](#restrictions-and-limitations)中所述。 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>从专用链接范围外管理访问权限
此页面底部的设置控制从公共网络访问，这意味着网络未通过上面列出的作用域进行连接。 如果设置为 " **允许从公共网络访问** "，则 **不** 会阻止从连接范围之外的计算机引入日志。 将 " **允许对查询的公共网络访问** " 设置为 " **无** " 可阻止来自范围之外的计算机的查询。 这包括通过工作簿、面板、基于 API 的客户端体验、Azure 门户中的见解等运行的查询。 在 Azure 门户外运行的体验，还必须在专用链接的 VNET 中运行查询 Log Analytics 数据。

### <a name="exceptions"></a>异常
如上所述的限制访问不适用于 Azure 资源管理器，因此具有以下限制：
* 对数据的访问-同时阻止/允许来自公共网络的查询适用于大多数 Log Analytics 体验，一些经验通过 Azure 资源管理器查询数据，因此将无法查询数据，除非资源管理器) 不久就会将专用链接设置应用到 (。 例如 Azure Monitor 解决方案、工作簿和见解以及逻辑应用连接器。
* 工作区管理-工作区设置和配置更改 (包括打开或关闭这些访问设置) 由 Azure 资源管理器管理。 使用适当的角色、权限、网络控制和审核限制对工作区管理的访问。 有关详细信息，请参阅 [Azure Monitor 角色、权限和安全性](../roles-permissions-security.md)。

> [!NOTE]
> 通过[诊断设置](../essentials/diagnostic-settings.md)上传到工作区的日志和指标会通过安全的专用 Microsoft 通道，且不受这些设置控制。

### <a name="log-analytics-solution-packs-download"></a>Log Analytics 解决方案包下载

若要使 Log Analytics 代理能够下载解决方案包，请在防火墙允许列表中添加适当的完全限定的域名。 


| 云环境 | 代理资源 | 端口 | 方向 |
|:--|:--|:--|:--|
|Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | 出站
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  出站
|Azure 中国世纪互联      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | 出站

## <a name="configure-application-insights"></a>配置 Application Insights

转到 Azure 门户。 在 Azure Monitor Application Insights 组件资源中，是左侧的菜单项 " **网络隔离** "。 你可通过此菜单控制两种不同的状态。

![AI 网络隔离](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

首先，可将该 Application Insights 资源连接到你有权访问的 Azure Monitor 专用链接范围。 选择 " **添加** "，然后选择 **Azure Monitor 专用链接范围**。 选择 "应用" 来连接它。 所有已连接的范围都显示在此屏幕中。 建立此连接后，连接的虚拟网络中的网络流量可以访问此组件，并与从作用域连接到连接 [Azure Monitor 资源](#connect-azure-monitor-resources)时相同。 

其次，你可以控制此资源如何从私有链接范围以外 (AMPLS) 之前列出。 如果将 " **允许引入公共网络访问** " 设置为 " **否**"，则已连接范围之外的计算机或 sdk 无法将数据上载到此组件。 如果将 " **允许对查询进行公用网络访问** " 设置为 " **否**"，则范围之外的计算机无法访问此 Application Insights 资源中的数据。 该数据包括访问 APM 日志、指标和实时指标流、以及根据工作簿、仪表板、基于查询 API 的客户端体验和 Azure 门户中的见解等构建的体验等等。 

> [!NOTE]
> 非门户消耗体验还必须在包含受监视工作负荷的专用链接 VNET 上运行。

需要将托管受监视工作负载的资源添加到专用链接中。 有关示例，请参阅 [使用 Azure Web 应用的专用终结点](../../app-service/networking/private-endpoint.md)。

仅可以此方式限制对 Application Insights 资源中数据的访问。 但是，配置更改（包括打开或关闭这些访问设置）由 Azure 资源管理器管理。 因此，你应该使用适当的角色、权限、网络控制和审核来限制对资源管理器的访问。 有关详细信息，请参阅 [Azure Monitor 角色、权限和安全性](../roles-permissions-security.md)。

> [!NOTE]
> 若要完全保护基于工作区的 Application Insights，需要锁定对 Application Insights 资源和基础 Log Analytics 工作区的访问。
>
> 代码级诊断 (探查器/调试器) 需要你 [提供自己的存储帐户](../app/profiler-bring-your-own-storage.md) 以支持专用链接。

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>处理专用链接的全部或全部本质
如 [规划专用链接设置](#planning-your-private-link-setup)中所述，即使针对单个资源设置专用链接也会影响该网络中的所有 Azure Monitor 资源，以及共享同一 DNS 的其他网络中的所有资源。 此行为会使你的载入过程变得困难。 请考虑以下选项：

* 最简单且最安全的方法是将所有 Application Insights 组件添加到 AMPLS。 对于想要仍从其他网络访问的组件，请将 "允许引入/查询公共 internet 访问" 标志设置为 "是" (默认) 。
* 隔离网络-如果你 (，或者可以使用辐射 vnet 与) 对齐，请按照 [Azure 中的中心辐射网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)中的指导进行操作。 然后，在相关辐射 Vnet 中设置单独的专用链接设置。 请确保分隔 DNS 区域，因为与其他辐射网络共享 DNS 区域将导致 [dns 覆盖](#the-issue-of-dns-overrides)。
* 对特定应用使用自定义 DNS 区域-此解决方案允许你通过专用链接访问选择 Application Insights 组件，同时通过公用路由保留所有其他流量。
    - 设置 [自定义专用 DNS 区域](../../private-link/private-endpoint-dns.md)，并为其指定唯一名称，如 internal.monitor.azure.com
    - 创建 AMPLS 和专用终结点，并选择 **不** 自动与专用 DNS 集成
    - 中转到专用终结点-> DNS 配置，并查看 Fqdn 的建议映射。
    - 选择添加配置并选择刚创建的 internal.monitor.azure.com 区域
    - 为 ![ 已配置 DNS 区域的上述屏幕截图添加记录](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - 中转到 Application Insights 组件，并复制其 [连接字符串](../app/sdk-connection-string.md)。
    - 希望通过专用链接调用此组件的应用或脚本应使用 EndpointSuffix = internal 的连接字符串
* 通过主机文件（而不是 DNS）映射终结点，只能通过网络中的特定计算机/VM 获得专用链接访问权限：
    - 设置 AMPLS 和专用终结点，并选择 **不** 自动与专用 DNS 集成 
    - 在运行 hosts 文件中的应用的计算机上配置上述 A 记录


## <a name="use-apis-and-command-line"></a>使用 API 和命令行

可以使用 Azure 资源管理器模板、REST 和命令行接口来自动执行前面所述的过程。

若要创建和管理专用链接范围，请使用 [REST API](/rest/api/monitor/private%20link%20scopes%20(preview)) 或 [Azure CLI (az 监视器专用链接范围) ](/cli/azure/monitor/private-link-scope)。

要管理网络访问，请在 [Log Analytics 工作区](/cli/azure/monitor/log-analytics/workspace)或 [Application Insights 组件](/cli/azure/ext/application-insights/monitor/app-insights/component)上使用 `[--ingestion-access {Disabled, Enabled}]` 和 `[--query-access {Disabled, Enabled}]` 标志。

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>收集自定义日志和 IIS 日志 over Private 链接

在自定义日志的引入过程中会使用存储帐户。 默认使用服务托管的存储帐户。 但若要通过专用链接引入自定义日志，必须使用你自己的存储帐户并将其与 Log Analytics 工作区关联。 请查看更多详细信息，了解如何使用[命令行](/cli/azure/monitor/log-analytics/workspace/linked-storage)设置此类帐户。

要详细了解如何使用你自己的存储帐户，请参阅[使用客户拥有的存储帐户引入日志](private-storage.md)

## <a name="restrictions-and-limitations"></a>限制和局限

### <a name="ampls"></a>AMPLS
在规划专用链接设置时，AMPLS 对象具有一些应考虑的限制：

* VNet 只能连接到1个 AMPLS 对象。 这意味着，AMPLS 对象必须提供对 VNet 应有权访问的所有 Azure Monitor 资源的访问权限。
* Azure Monitor 资源 (工作区或 Application Insights 组件) 最多可以连接到5个 AMPLSs。
* AMPLS 对象最多可连接到 50 Azure Monitor 资源。
* AMPLS 对象最多可连接到10个私有终结点。

若要深入了解这些限制，请参阅 [考虑限制](#consider-limits) 。

### <a name="agents"></a>代理

Windows 和 Linux 代理的最新版本必须用于支持 Log Analytics 工作区的安全引入。 旧版本无法通过专用网络上传监视数据。

**Log Analytics Windows 代理**

使用 Log Analytics 代理版本10.20.18038.0 或更高版本。

**Log Analytics Linux 代理**

使用代理版本 1.12.25 或更高版本。 如果无法运行，请在 VM 上运行以下命令。

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure 门户

要使用 Azure Monitor 门户体验（例如 Application Insights 和 Log Analytics），你需要使 Azure 门户和 Azure Monitor 扩展能在专用网络上进行访问。 将 **AzureActiveDirectory**、 **AzureResourceManager**、 **AzureFrontDoor** 和 **AzureFrontDoor** [服务标记](../../firewall/service-tags.md) 添加到网络安全组。

### <a name="querying-data"></a>正在查询数据
专用链接不支持该[ `externaldata` 运算符](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor)，因为它从存储帐户读取数据，但不保证会私下访问存储。

### <a name="programmatic-access"></a>以编程方式访问

若要在专用网络上使用 Azure Monitor 的 REST API、 [CLI](/cli/azure/monitor) 或 PowerShell，请将 [服务标记](../../virtual-network/service-tags-overview.md)  **AzureActiveDirectory** 和 **AzureResourceManager** 添加到防火墙。

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>从内容分发网络下载 Application Insights SDK

在脚本中捆绑 JavaScript 代码，使浏览器不会尝试从 CDN 下载代码。 有关示例，可查看 [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>浏览器 DNS 设置

如果要通过专用链接连接到 Azure Monitor 资源，则必须通过在网络上配置的专用终结点完成到这些资源的通信。 若要启用专用终结点，请根据 [连接到专用终结点](#connect-to-a-private-endpoint)中所述更新 DNS 设置。 某些浏览器使用自己的 DNS 设置，而不是设置的设置。 浏览器可能会尝试连接到 Azure Monitor 公用终结点，并完全绕过专用链接。 验证你的浏览器设置不会替代或缓存旧的 DNS 设置。 

## <a name="next-steps"></a>后续步骤

- 了解 [专用存储](private-storage.md)