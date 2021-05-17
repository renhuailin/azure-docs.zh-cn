---
title: 使用 Azure 专用链接将网络安全地连接到 Azure Monitor
description: 使用 Azure 专用链接将网络安全地连接到 Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 5db990fe4bf54c5604eb58af677ec4891639eb1b
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108165616"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>使用 Azure 专用链接将网络安全地连接到 Azure Monitor

通过 [Azure 专用链接](../../private-link/private-link-overview.md)，可使用专用终结点将 Azure PaaS 服务安全地链接到你的虚拟网络。 对于很多服务，一个资源只需设置一个终结点即可。 不过，Azure Monitor 是不同互连服务的集合，它们相互协同，共同监视你的工作负载。 因此，我们构建了一个名为 Azure Monitor 专用链接范围 (AMPLS) 的资源。 可通过 AMPLS 来定义监视网络的边界并连接到你的虚拟网络。 本文介绍何时使用以及如何设置 Azure Monitor 专用链接范围。

## <a name="advantages"></a>优势

借助专用链接，你可以：

- 以专用方式连接到 Azure Monitor，无需开放任何公共网络访问权限
- 确保仅可通过授权的专用网络访问你正在监视的数据
- 定义通过你的专用终结点连接的特定 Azure Monitor 资源，从而防止你的专用网络出现数据泄露的情况
- 使用 ExpressRoute 和专用链接将你的专用本地网络安全地连接到 Azure Monitor
- 将所有流量保留在 Microsoft Azure 主干网络中

有关详细信息，请参阅[专用链接的主要优势](../../private-link/private-link-overview.md#key-benefits)。

## <a name="how-it-works"></a>工作原理

Azure Monitor 专用链接范围 (AMPLS) 将专用终结点连接（及包含它们的 VNet）连接到一个或多个 Azure Monitor 资源 - Log Analytics 工作区和 Application Insights 组件。

![基本资源拓扑关系图](./media/private-link-security/private-link-basic-topology.png)

* VNet 中的专用终结点允许其通过网络池中的专用 IP 而不是终结点的公共 IP 来访问 Azure Monitor 终结点。 这样就可以继续使用 Azure Monitor 资源，无需向不需要的出站流量开放 VNet。 
* 从专用终结点到 Azure Monitor 资源的流量将通过 Microsoft Azure 主干，而不会路由到公用网络。 
* 可以配置每个工作区或组件，以允许或拒绝从公用网络引入和查询。 这提供了资源级保护，因此可以控制流入特定资源的流量。

> [!NOTE]
> 一个 Azure Monitor 资源可属于多个 AMPLS，但无法将一个 VNet 连接到多个 AMPLS。 

## <a name="planning-your-private-link-setup"></a>规划专用链接设置

设置 Azure Monitor 专用链接之前，请考虑网络拓扑，特别是 DNS 路由拓扑。 

### <a name="the-issue-of-dns-overrides"></a>DNS 替代问题
一些 Azure Monitor 服务使用全局终结点，这意味着它们会针对任何工作区/组件提供请求。 其中一些示例包括 Application Insights 引入终结点，以及 Application Insights 和 Log Analytics 的查询终结点。

设置专用链接连接时会更新 DNS，以便将 Azure Monitor 终结点映射到 VNet IP 范围内的专用 IP 地址。 此更改替代这些终结点的任何先前映射，这可能会产生有意义的影响，如下所示。 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor 专用链接适用于全部 Azure Monitor 资源 - 全或无
由于某些 Azure Monitor 终结点是全局的，因此无法为特定组件或工作区创建专用链接连接。 相反，将专用链接设置到单个 Application Insights 组件或 Log Analytics 工作区时，将为全部 Application Insights 组件更新 DNS 记录。 任何引入或查询组件的尝试都会通过专用链接，并且可能会失败。 对于 Log Analytics，引入和配置终结点都针对特定的工作区，这意味着专用链接设置仅适用于指定的工作区。 引入和配置其他工作区将被定向到默认的公共 Log Analytics 终结点。

![单个 VNet 中的 DNS 替代示意图](./media/private-link-security/dns-overrides-single-vnet.png)

这不仅适用于特定的 VNet，还适用于所有共享同一 DNS 服务器的 VNet（请参阅 [DNS 的替代问题](#the-issue-of-dns-overrides)）。 例如，将日志引入任何 Application Insights 组件的请求始终通过专用链接路由发送。 未链接到 AMPLS 的组件将无法通过专用链接验证，因而不会通过专用链接。

> [!NOTE]
> 总结：设置单个资源的专用链接链接后，它将适用于整个网络的 Azure Monitor 资源。 这对于 Application Insights 来说是“全或无”。 这实际上意味着，应该将网络中的所有 Application Insights 资源添加到 AMPLS，或者不添加任何 Application Insights 资源。
> 
> 为了处理数据外泄的风险，我们建议将所有 Application Insights 和 Log Analytics 资源添加到 AMPLS，并尽可能阻止网络出口流量。

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor 专用链接适用于整个网络
某些网络由多个 VNet 组成。 如果 VNet 使用相同的 DNS 服务器，则它们将替代彼此的 DNS 映射，并可能会中断彼此与 Azure Monitor 的通信（请参阅 [DNS 的替代问题](#the-issue-of-dns-overrides)）。 最终，只有最后一个 VNet 才能与 Azure Monitor 通信，因为 DNS 会将 Azure Monitor 终结点映射到此 VNet 范围内的专用 IP（可能无法从其他 VNet 访问此地址）。

![多个 VNet 中的 DNS 替代示意图](./media/private-link-security/dns-overrides-multiple-vnets.png)

在上面的关系图中，VNet 10.0.1.x 首先连接到 AMPLS1，并将 Azure Monitor 的全局终结点映射到其范围内的 IP。 稍后，VNet 10.0.2.x 连接到 AMPLS2，并替代具备其范围内 IP 的“相同全局终结点”的 DNS 映射。 由于这些 VNet 不对等互连，因此第一个 VNet 现在无法访问这些终结点。

> [!NOTE]
> 总结：AMPLS 设置会影响共享同一 DNS 区域的所有网络。 若要避免替代彼此的 DNS 终结点映射，最好在对等互连网络（如中心 VNet）上设置单个专用终结点，或者在 DNS 级别分离网络（例如，使用 DNS 转发器或完全分离 DNS 服务器）。

### <a name="hub-spoke-networks"></a>中心辐射型网络
中心辐射型拓扑可以通过在中心（主）VNet 上设置专用链接，而不是在每个分支 VNet 上设置，来避免 DNS 替代的问题。 这种设置是有意义的，特别是当辐射 VNet 使用共享 Azure Monitor 资源时很有意义。 

![中心辐射型单一专用终结点](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> 你可能有意倾向于为辐射 VNet 创建单独的专用链接，例如，允许每个 VNet 访问有限的监视资源组。 在这种情况下，可以为每个 VNet 创建专用的专用终结点和 AMPLS，但也必须验证它们并不共享相同的 DNS 区域，以避免 DNS 替代。


### <a name="consider-limits"></a>考虑限制

如[局限和限制](#restrictions-and-limitations)中所列，AMPLS 对象有多个限制，如下面的拓扑所示：
* 每个 VNet 仅连接到 1 个 AMPLS 对象。
* AMPLS B 使用 10 个可能专用终结点连接中的其中 2 个，连接到两个 VNet (VNet2 和 VNet3) 的专用终结点。
* AMPLS A 使用 50 个可能 Azure Monitor 资源连接中的其中 3 个，连接到两个工作区和一个 Application Insights 组件。
* Workspace2 使用 5 个可能 AMPLS 连接中的其中 2 个，连接到 AMPLS A 和 AMPLS B。

![AMPLS 限制关系图](./media/private-link-security/ampls-limits.png)

> [!NOTE]
> 如果使用需要自动化帐户的 Log Analytics 解决方案，如更新管理、更改跟踪或清单，则还应为自动化帐户设置一个单独的专用链接。 有关详细信息，请参阅[使用 Azure 专用链接将网络安全地连接到 Azure 自动化](https://docs.microsoft.com/azure/automation/how-to/private-link-security)。


## <a name="example-connection"></a>示例连接

首先创建一个 Azure Monitor 专用链接范围资源。

1. 转到 Azure 门户中的“创建资源”，并搜索“Azure Monitor 专用链接范围” 。

   ![查找 Azure Monitor 专用链接范围](./media/private-link-security/ampls-find-1c.png)

2. 选择“创建”。
3. 选择一个订阅和资源组。
4. 为 AMPLS 指定名称。 最好使用有意义且清晰的名称，如“AppServerProdTelem”。
5. 选择“查看 + 创建”  。 

   ![创建 Azure Monitor 专用链接范围](./media/private-link-security/ampls-create-1d.png)

6. 让验证通过，然后选择“创建”。

### <a name="connect-azure-monitor-resources"></a>连接 Azure Monitor 资源

将 Azure Monitor 资源（Log Analytics 工作区和 Application Insights 组件）连接到 AMPLS。

1. 在 Azure Monitor 专用链接范围中，选择左侧菜单中的“Azure Monitor 资源”。 选择“添加”按钮。
2. 添加工作区或组件。 选择“添加”按钮将调出一个对话框，你可在这里选择 Azure Monitor 资源。 你可浏览订阅和资源组，也可键入其名称通过筛选找到它们。 选择工作区或组件，然后选择“应用”，让它们添加到范围中。

    ![显示选择范围 UX 的屏幕截图](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> 删除 Azure Monitor 资源需要首先取消它们与任何 AMPLS 对象之间的连接。 不能删除连接到 AMPLS 的资源。

### <a name="connect-to-a-private-endpoint"></a>连接到专用终结点

你现已将资源连接到 AMPLS，接下来可创建一个专用终结点来连接我们的网络。 可在 [Azure 门户专用链接中心](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)或 Azure Monitor 专用链接范围中执行此任务，如下例所示。

1. 在范围资源中，选择左侧资源菜单中的“专用终结点连接”。 选择“专用终结点”，启动终结点创建进程。 你还可批准在专用链接中心启动的连接，方式是将其选择并选择“批准”。

    ![专用终结点连接 UX 的屏幕截图](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. 选择订阅、资源组、终结点的名称及其应位于的区域。 区域需要与要连接到的 VNet 的所在区域相同。

3. 在完成时选择“下一步:资源”。 

4. 在“资源”屏幕中，

   a. 选择包含你的 Azure Monitor 专用范围资源的订阅。 

   b. 对于“资源类型”，选择“Microsoft.insights/privateLinkScopes” 。 

   c. 从“资源”下拉列表中，选择你之前创建的专用连接范围。 

   d. 选择“下一步: 配置>”。
      ![显示选择“创建专用终结点”的屏幕截图](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. 在“配置”窗格中，

   a.    选择要连接到 Azure Monitor 资源的虚拟网络和子网 。 
 
   b.    针对“与专用 DNS 区域集成”选择“是”，让它自动新建一个专用 DNS 区域 。 实际 DNS 区域可能不同于下面屏幕截图中显示的区域。 
   > [!NOTE]
   > 如果选择“否”，并且喜欢手动管理 DNS 记录，请首先完成专用链接设置 - 包括此专用终结点和 AMPLS 配置。 然后，根据 [Azure 专用终结点 DNS 配置](../../private-link/private-endpoint-dns.md)中的说明配置 DNS。 切勿在准备专用链接设置时创建空记录。 你创建的 DNS 记录会替代现有设置，并且会影响与 Azure Monitor 的连接。
 
   c.    选择“查看 + 创建”  。
 
   d.    让验证通过。 
 
   e.    选择“创建”  。 

    ![显示选择“专用终结点”详细信息的屏幕截图。](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

现在创建了一个连接到此 AMPLS 的新的专用终结点。

## <a name="review-and-validate-your-private-link-setup"></a>查看并验证专用链接设置

### <a name="reviewing-your-endpoints-dns-settings"></a>查看终结点的 DNS 设置
你创建的专用终结点现在应配置了五个 DNS 区域：

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net
* privatelink-blob-core-windows-net

> [!NOTE]
> 其中每个区域将特定 Azure Monitor 终结点映射到 VNet IP 池中的专用 IP。 以下图像中显示的 IP 地址只是示例。 你的配置应显示自己网络中的专用 IP。

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
此区域涵盖 Azure Monitor 使用的全局终结点，这意味着这些终结点为考虑所有资源而不是特定资源的请求提供服务。 此区域应具有为以下终结点映射的终结点：
* `in.ai` - Application Insights 引入终结点（全局和区域条目）
* `api` - Application Insights 和 Log Analytics 终结点
* `live` - Application Insights 实时指标流终结点
* `profiler` - Application Insights 探查器终结点
* `snapshot` - Application Insights 快照终结点[![专用 DNS 区域 monitor-azure-com 的屏幕截图。](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
此区域涵盖特定工作区到 OMS 终结点的映射。 应该会看到链接到与此专用终结点连接的 AMPLS 的每个工作区的条目。
[![专用 DNS 区域 oms-opinsights-azure-com 的屏幕截图。](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
此区域涵盖特定工作区到 ODS 终结点的映射 - Log Analytics 的引入终结点。 应该会看到链接到与此专用终结点连接的 AMPLS 的每个工作区的条目。
[![专用 DNS 区域 ods-opinsights-azure-com 的屏幕截图。](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
此区域涵盖特定工作区到代理服务自动化终结点的映射。 应该会看到链接到与此专用终结点连接的 AMPLS 的每个工作区的条目。
[![专用 DNS 区域代理 svc-azure-automation-net 的屏幕截图。](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-blob-core-windows-net
此区域配置与全局代理的解决方案包存储帐户的连接。 通过它，代理可以下载新的或更新的解决方案包（也称为管理包）。 无论使用多少个工作区，都只需一个条目来处理 Log Analytics 代理。
[![专用 DNS 区域 blob-core-windows-net 的屏幕截图。](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> 此条目仅会添加到 2021 年 4 月 19 日或之后创建的专用链接设置中。


### <a name="validating-you-are-communicating-over-a-private-link"></a>验证是否通过专用链接进行通信
* 若要验证你的请求现在是否是通过专用终结点发送的，可使用网络跟踪工具甚至浏览器来查看它们。 例如，尝试查询工作区或应用程序时，请确保将请求发送到映射到 API 终结点的专用 IP，此示例中为“172.17.0.9”。

    注意：一些浏览器可能会使用其他 DNS 设置（请参阅[浏览器 DNS 设置](#browser-dns-settings)）。 请确保应用 DNS 设置。

* 若要确保工作区或组件没有收到来自公用网络（未通过 AMPLS 连接）的请求，请将资源的公共引入和查询标志设置为“否”，如[从专用链接范围外管理访问权限](#manage-access-from-outside-of-private-links-scopes)中所述。

* 在受保护网络的客户端上，使用 `nslookup` 到 DNS 区域中列出的任何终结点。 它应由 DNS 服务器解析为映射的专用 IP，而不是默认使用的公共 IP。


## <a name="configure-log-analytics"></a>配置 Log Analytics

转到 Azure 门户。 在 Log Analytics 工作区资源菜单中，左侧有一个“网络隔离”菜单项。 你可通过此菜单控制两种不同的状态。

![LA 网络隔离](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>已连接的 Azure Monitor 专用链接范围
连接到工作区的所有范围都显示在此屏幕中。 连接到范围 (AMPLSs) 后，便可以使连接到每个 AMPLS 的虚拟网络的网络流量到达此工作区。 通过这里建立连接，其效果与我们在[连接 Azure Monitor 资源](#connect-azure-monitor-resources)中所做的在范围上设置连接相同。 若要添加新连接，请选择“添加”，然后选择 Azure Monitor专用链接范围。 选择“应用”进行连接。 请注意，工作区可以连接到5个 AMPLS 对象，如[局限和限制](#restrictions-and-limitations)中所述。 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>从专用链接范围外管理访问权限
此页面底部的设置控制公用网络的访问权限，即未通过列出的范围 (AMPLS) 进行连接的网络。 将“允许引入通过公用网络访问”设置为“否”，则会阻止从连接范围之外的计算机引入日志。 将“允许查询通过公用网络访问”设置为“否”，则会阻止来自范围外的计算机的查询。 这包括通过工作簿、仪表板、基于查询 API 的客户端体验、Azure 门户中的见解等运行的查询。 对于 Azure 门户外部运行的体验，其查询 Log Analytics 数据也必须在专用链接的 VNET 中运行。

### <a name="exceptions"></a>例外
如上所述的限制访问不适用于 Azure 资源管理器，因此具有以下限制：
* 对数据的访问 - 阻止/允许来自公用网络的查询适用于大多数 Log Analytics 体验，但一些体验通过 Azure 资源管理器查询数据，因此如果不将专用链接设置也应用到资源管理器（此功能即将上线），便无法查询数据。 例如 Azure Monitor 解决方案、工作簿和见解以及逻辑应用连接器。
* 工作区管理 - 工作区设置和配置更改（例如打开或关闭这些访问设置）由 Azure 资源管理器进行管理。 使用适当的角色、权限、网络控件和审核来限制对工作区管理的访问。 有关详细信息，请参阅 [Azure Monitor 角色、权限和安全性](../roles-permissions-security.md)。

> [!NOTE]
> 通过[诊断设置](../essentials/diagnostic-settings.md)上传到工作区的日志和指标会通过安全的专用 Microsoft 通道，且不受这些设置控制。

### <a name="log-analytics-solution-packs-download"></a>Log Analytics 解决方案包下载
Log Analytics 代理需要访问全局存储帐户才能下载解决方案包。 2021 年 4 月 19 日或之后创建的专用链接设置可以通过专用链接访问代理的解决方案包存储。 可通过为 [blob.core.windows.net](#privatelink-blob-core-windows-net) 创建的新 DNS 区域实现此目的。

如果专用链接设置是在 2021 年 4 月 19 日之前创建的，则无法通过专用链接访问解决方案包存储。 若要处理此情况，可以执行以下操作之一：
* 重新创建 AMPLS 和与其连接的专用终结点
* 通过将以下规则添加到防火墙允许列表，使代理可以通过其公共终结点访问存储帐户：

    | 云环境 | 代理资源 | 端口 | 方向 |
    |:--|:--|:--|:--|
    |Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | 出站
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  出站
    |Azure 中国世纪互联      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | 出站


## <a name="configure-application-insights"></a>配置 Application Insights

转到 Azure 门户。 在 Azure Monitor Application Insights 组件资源 中，左侧有“网络隔离”菜单项。 你可通过此菜单控制两种不同的状态。

![AI 网络隔离](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

首先，可将该 Application Insights 资源连接到你有权访问的 Azure Monitor 专用链接范围。 选择“添加”并选择 Azure Monitor 专用链接范围 。 选择“应用”进行连接。 所有已连接的范围都显示在此屏幕中。 建立此连接后，连接的虚拟网络中的网络流量可以访问此组件，效果与我们在[连接 Azure Monitor 资源](#connect-azure-monitor-resources)中从范围进行连接时相同。 

其次，你能控制可如何从之前列出的专用链接范围 (AMPLS) 外部访问该资源。 如果将“允许公用网络访问以便执行引入”设置为“否”，则已连接的范围之外的计算机或 SDK 无法将数据上传到此组件中 。 如果将“允许公用网络访问以便执行查询”设置为“否”，则范围之外的计算机无法访问该 Application Insights 资源中的数据 。 该数据包括访问 APM 日志、指标和实时指标流、以及根据工作簿、仪表板、基于查询 API 的客户端体验和 Azure 门户中的见解等构建的体验等等。 

> [!NOTE]
> 不使用门户的体验也必须在包含受监视工作负荷的专用链接 VNET 上运行。

需要将托管受监视工作负载的资源添加到专用链接中。 请参阅[为 Azure Web 应用使用专用终结点](../../app-service/networking/private-endpoint.md)查看示例。

仅可以此方式限制对 Application Insights 资源中数据的访问。 然而，配置更改（例如打开或关闭这些访问设置）由 Azure 资源管理器进行管理。 因此，你应当使用适当的角色、权限、网络控件和审核来限制对资源管理器的访问。 有关详细信息，请参阅 [Azure Monitor 角色、权限和安全性](../roles-permissions-security.md)。

> [!NOTE]
> 若要完全保护基于工作区的 Application Insights，需要锁定对 Application Insights 资源和基础 Log Analytics 工作区的访问。
>
> 代码级诊断（探查器/调试器）需要你[提供自己的存储帐户](../app/profiler-bring-your-own-storage.md)以支持专用链接。

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>处理专用链接的“全或无”本质
如[规划专用链接设置](#planning-your-private-link-setup)中所述，即使针对单个资源设置专用链接也会影响该网络及共享同一 DNS 的其他网络中的所有 Azure Monitor 资源。 此行为会使你的加入过程充满挑战。 请考虑以下选项：

* 全部加入 - 最简单且最安全的方法是将全部 Application Insights 组件添加到 AMPLS。 对于仍希望从其他网络访问的组件，请将“允许公共 Internet 访问引入/查询”标志设置为“是”（默认设置）。
* 隔离网络 - 如果你正使用辐射 VNet（或可以与之保持一致），请按照 [Azure 中的中心辐射网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)中的指导进行操作。 然后，在相关辐射 VNet 中设置单独的专用链接设置。 请确保也将 DNS 区域分隔开，因为与其他辐射网络共享 DNS 区域将导致 [DNS 替代](#the-issue-of-dns-overrides)。
* 对特定应用使用自定义 DNS 区域 - 此解决方案允许你通过专用链接访问所选 Application Insights 组件，同时使其他流量继续通过公用路由。
    - 设置[自定义专用 DNS 区域](../../private-link/private-endpoint-dns.md)，并为其指定唯一名称，如 internal.monitor.azure.com
    - 创建 AMPLS 和专用终结点，并选择不与专用 DNS 自动集成
    - 转到专用终结点 -> DNS 配置，并查看 FQDN 的建议映射。
    - 选择添加配置并选择刚创建的 internal.monitor.azure.com 区域
    - 为上述![已配置 DNS 区域的屏幕截图](./media/private-link-security/private-endpoint-global-dns-zone.png)添加记录
    - 转到 Application Insights 组件，并复制其[连接字符串](../app/sdk-connection-string.md)。
    - 希望通过专用链接调用此组件的应用或脚本应使用 EndpointSuffix=internal.monitor.azure.com 的连接字符串
* 通过主机文件而非 DNS 来映射终结点 - 只通过网络中的特定计算机/VM 获得专用链接访问权限：
    - 设置 AMPLS 和专用终结点，并选择不与专用 DNS 自动集成 
    - 在主机文件中运行应用的计算机上配置上述记录


## <a name="use-apis-and-command-line"></a>使用 API 和命令行

可使用 Azure 资源管理器模板、REST、和命令行接口将前面描述的过程自动化。

若要创建和管理专用链接范围，请使用 [REST API](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) 或 [Azure CLI (az monitor private-link-scope)](/cli/azure/monitor/private-link-scope)。

要管理工作区或组件上的网络访问标志，请在 [Log Analytics 工作区](/cli/azure/monitor/log-analytics/workspace)或 [Application Insights 组件](/cli/azure/ext/application-insights/monitor/app-insights/component)上使用 `[--ingestion-access {Disabled, Enabled}]` 和 `[--query-access {Disabled, Enabled}]` 标志。

### <a name="example-arm-template"></a>ARM 模板示例
以下 ARM 模板可创建：
* 名为“my-scope”的专用链接范围 (AMPLS)
* 名为“my-workspace”的 Log Analytics 工作区
* 将范围内的资源添加到名为“my-workspace-connection”的“my-scope”AMPLS

```
{
    "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
    "contentVersion": "1.0.0.0",
    "parameters": {
        "private_link_scope_name": {
            "defaultValue": "my-scope",
            "type": "String"
        },
        "workspace_name": {
            "defaultValue": "my-workspace",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.insights/privatelinkscopes",
            "apiVersion": "2019-10-17-preview",
            "name": "[parameters('private_link_scope_name')]",
            "location": "global",
            "properties": {}
        },
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-10-01",
            "name": "[parameters('workspace_name')]",
            "location": "westeurope",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "publicNetworkAccessForIngestion": "Enabled",
                "publicNetworkAccessForQuery": "Enabled"
            }
        },
        {
            "type": "microsoft.insights/privatelinkscopes/scopedresources",
            "apiVersion": "2019-10-17-preview",
            "name": "[concat(parameters('private_link_scope_name'), '/', concat(parameters('workspace_name'), '-connection'))]",
            "dependsOn": [
                "[resourceId('microsoft.insights/privatelinkscopes', parameters('private_link_scope_name'))]",
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            ],
            "properties": {
                "linkedResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            }
        }
    ]
}
```

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>通过专用链接收集自定义日志和 IIS 日志

在自定义日志的引入过程中会使用存储帐户。 默认使用服务托管的存储帐户。 但若要通过专用链接引入自定义日志，必须使用你自己的存储帐户并将其与 Log Analytics 工作区关联。 请查看更多详细信息，了解如何使用[命令行](/cli/azure/monitor/log-analytics/workspace/linked-storage)设置此类帐户。

要详细了解如何使用你自己的存储帐户，请参阅[使用客户拥有的存储帐户引入日志](private-storage.md)

## <a name="restrictions-and-limitations"></a>限制和局限

### <a name="ampls"></a>AMPLS
在规划专用链接设置时，应考虑到 AMPLS 对象的许多限制：

* 一个 VNet 只能连接到 1 个 AMPLS 对象。 这意味着，AMPLS 对象必须提供对 VNet 应有权访问的所有 Azure Monitor 资源的访问权限。
* 一个 Azure Monitor 资源（工作区或 Application Insights 组件）最多可连接到 5 个 AMPLS。
* 一个 AMPLS 对象最多可连接到 50 个 Azure Monitor 资源。
* 一个 AMPLS 对象最多可连接到 10 个专用终结点。

若要深入了解这些限制，请参阅[考虑限制](#consider-limits)。

### <a name="agents"></a>代理

必须使用最新版本的 Windows 和 Linux 代理，以支持安全引入到 Log Analytics 工作区。 旧版本无法通过专用网络加载监视数据。

**Log Analytics Windows 代理**

使用 Log Analytics 代理版本 10.20.18038.0 或更高版本。

**Log Analytics Linux 代理**

使用代理版本 1.12.25 或更高版本。 如果无法使用，请在 VM 上运行以下命令。

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure 门户

要使用 Azure Monitor 门户体验（例如 Application Insights 和 Log Analytics），你需要使 Azure 门户和 Azure Monitor 扩展能在专用网络上进行访问。 在防火墙中添加“AzureActiveDirectory”、“AzureResourceManager”、
“AzureFrontDoor.FirstParty”和“AzureFrontdoor.Frontend”[服务标记](../../firewall/service-tags.md)   。

### <a name="querying-data"></a>正在查询数据
专用链接不支持[`externaldata`运算符](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor)，因为运算符从存储帐户读取数据，但不保证以专用方式访问存储。

### <a name="programmatic-access"></a>以编程方式访问

要在专用网络上将 REST API、[CLI](/cli/azure/monitor) 或 PowerShell 与 Azure Monitor 结合使用，请在防火墙中添加 AzureActiveDirectory 和 AzureResourceManager [服务标记](../../virtual-network/service-tags-overview.md) 。

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>从内容分发网络下载 Application Insights SDK

在脚本中捆绑 JavaScript 代码，让浏览器不尝试从 CDN 中下载代码。 有关示例，可查看 [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>浏览器 DNS 设置

如果通过专用链接连接到 Azure Monitor 资源，则通往这些资源的流量必须经过在网络上配置的专用终结点。 若要启用专用终结点，请根据[连接到专用终结点](#connect-to-a-private-endpoint)中的所述内容更新 DNS 设置。 一些浏览器使用自己的 DNS 设置，而不是你设置的 DNS 设置。 浏览器可能会尝试连接到 Azure Monitor 公用终结点，并完全绕过专用链接。 验证你的浏览器设置不会替代或缓存旧的 DNS 设置。 

## <a name="next-steps"></a>后续步骤

- 了解[私有存储](private-storage.md)
