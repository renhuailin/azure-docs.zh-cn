---
title: 设计专用链接设置
description: 设计专用链接设置
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: b42b3c9146b99ee6e65dc83968ba8e97c8f209fb
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730482"
---
# <a name="design-your-private-link-setup"></a>设计专用链接设置

设置 Azure Monitor 专用链接之前，请考虑网络拓扑，特别是 DNS 路由拓扑。
如[工作原理](./private-link-security.md#how-it-works)中所述，设置专用链接会影响到所有 Azure Monitor 资源的流量。 对 Application Insights 资源尤其如此。 此外，它不仅影响连接到专用终结点的网络，还会影响共享同一 DNS 的所有其他网络。

最简单且最安全的方法是：
1. 创建单个专用链接连接，其中包含单个专用终结点和单个 AMPLS。 如果网络已对等互连，请在共享（或中心）VNet 上创建专用链接连接。
2. 将所有 Azure Monitor 资源（Application Insights 组件和 Log Analytics 工作区）添加到该 AMPLS。
3. 尽可能阻止网络出口流量。


## <a name="plan-by-network-topology"></a>按网络拓扑制定计划

### <a name="guiding-principle-avoid-dns-overrides-by-using-a-single-ampls"></a>指导原则：通过使用单个 AMPLS 避免 DNS 替代
某些网络由多个 VNet 或其他互连网络组成。 如果这些网络共享同一 DNS，则在其中任一网络中设置专用链接都会更新 DNS，并影响所有网络中的流量。

在下面的关系图中，VNet 10.0.1.x 首先连接到 AMPLS1，并将 Azure Monitor 的全局终结点映射到其范围内的 IP。 稍后，VNet 10.0.2.x 连接到 AMPLS2，并替代具备其范围内 IP 的“相同全局终结点”的 DNS 映射。 由于这些 VNet 不对等互连，因此第一个 VNet 现在无法访问这些终结点。

若要避免此冲突，请为每个 DNS 仅创建一个 AMPLS 对象。

![多个 VNet 中的 DNS 替代示意图](./media/private-link-security/dns-overrides-multiple-vnets.png)


### <a name="hub-and-spoke-networks"></a>中心辐射型网络
中心辐射型网络应使用在中心（主）网络上设置的单个专用链接连接，而不是在每个分支 VNet 上设置的连接。 

![中心辐射型单一专用终结点](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> 你可能有意倾向于为辐射 VNet 创建单独的专用链接，例如，允许每个 VNet 访问有限的监视资源组。 在这种情况下，可以为每个 VNet 创建专用的专用终结点和 AMPLS，但也必须验证它们并不共享相同的 DNS 区域，以避免 DNS 替代。

### <a name="peered-networks"></a>对等互连网络
网络对等互连用于中心辐射型拓扑以外的各种拓扑。 此类网络可共享连接彼此的 IP 地址，并且很可能共享相同的 DNS。 在这种情况下，我们再次建议在网络上创建可供其他网络访问的单个专用链接。 不要创建多个专用终结点和 AMPLS 对象，因为最终只会应用 DNS 中设置的最后一个终结点和 AMPLS 对象。

### <a name="isolated-networks"></a>隔离网络
如果网络未对等互连，则必须分隔其 DNS 才能使用专用链接。 完成后，为每个网络创建一个单独的专用终结点，并创建一个单独的 AMPLS 对象。 AMPLS 对象可以链接到相同的工作区/组件，也可以链接到不同的工作区/组件。

### <a name="testing-locally-edit-your-machines-hosts-file-instead-of-the-dns"></a>在本地测试：编辑计算机的主机文件，而不是 DNS 
若要在本地测试专用链接而不影响网络上的其他客户端，请确保在创建专用终结点时不要更新 DNS。 相反，请编辑计算机上的主机文件，以便它将请求发送到专用链接终结点：
* 设置专用链接，但在连接到专用终结点时，选择不与 DNS 自动集成（步骤 5b）。
* 在计算机的主机文件上配置相关终结点。 若要查看需要映射的 Azure Monitor 终结点，请参阅[查看终结点的 DNS 设置](./private-link-configure.md#reviewing-your-endpoints-dns-settings)。

不建议在生产环境中使用这种方法。

## <a name="control-how-private-links-apply-to-your-networks"></a>控制专用链接如何应用于网络
专用链接访问模式（于 2021 年 9 月推出）使你能够控制专用链接如何影响网络流量。 这些设置可应用于 AMPLS 对象（以影响所有连接的网络）或连接该对象的特定网络。

选择适当的访问模式会对网络流量产生不利影响。 可以分别为引入和查询设置这些模式中的每一种：

* 仅专用 - 允许 VNet 仅访问专用链接资源（AMPLS 中的资源）。 这是最安全的工作模式，可防止数据外泄。 为实现此目的，阻止了从 AMPLS 传出到 Azure Monitor 资源的流量。
![AMPLS“仅专用”访问模式示意图](./media/private-link-security/ampls-private-only-access-mode.png)
* 开放式 - 允许 VNet 访问专用链接资源和不在 AMPLS 中的资源（如果这些资源[接受来自公共网络的流量](./private-link-design.md#control-network-access-to-your-resources)）。 虽然开放式访问模式不能防止数据外泄，但它仍具有专用链接的其他好处：到专用链接资源的流量通过专用终结点发送、经过验证并通过 Microsoft 主干发送。 开放式模式适用于混合工作模式（公开访问某些资源，通过专用链接访问其他资源），或适用于逐步加入的过程。
![AMPLS“开放式”访问模式的示意图](./media/private-link-security/ampls-open-access-mode.png)为引入和查询分别设置各访问模式。 例如，可以为引入设置“仅专用”模式，为查询设置“开放式”模式。

选择访问模式时请小心。 如果使用“仅专用”访问模式，则将阻止流向以下资源的流量：在所有共享同一 DNS 的网络中不属于 AMPLS，无论订阅或租户如何（Log Analytics 引入请求除外，在下文中有所介绍）。 如果无法将所有 Azure Monitor 资源添加到 AMPLS，请首先添加选定资源并应用开放访问模式。 只有在将所有 Azure Monitor 资源添加到 AMPLS 后，才能切换到“仅专用”模式以获得最大安全性。

有关配置详细信息和示例，请参阅[使用 API 和命令行](./private-link-configure.md#use-apis-and-command-line)。

> [!NOTE]
> Log Analytics 引入操作使用资源特定的终结点。 因此，它不遵循 AMPLS 访问模式。 若要确保 Log Analytics 引入请求无法访问 AMPLS 外部的工作区，请将网络防火墙设置为阻止流量发送到公共终结点，不管 AMPLS 访问模式如何。

### <a name="setting-access-modes-for-specific-networks"></a>设置特定网络的访问模式
对 AMPLS 资源设置的访问模式会影响所有网络，但你可以替代特定网络的这些设置。

在下图中，VNet1 使用“开放式”模式，VNet2 使用“仅专用”模式。 因此，来自 VNet1 的请求通过专用链接可以访问 Workspace1 和 Component2，不通过专用链接可以访问 Component3（如果其[接受来自公共网络的流量](./private-link-design.md#control-network-access-to-your-resources)）。 但是，VNet2 请求将无法访问 Component3。 
![混合访问模式示意图](./media/private-link-security/ampls-mixed-access-modes.png)


## <a name="consider-ampls-limits"></a>考虑 AMPLS 限制
AMPLS 对象具有以下限制：
* 一个 VNet 只能连接到一个 AMPLS 对象。 这意味着，AMPLS 对象必须提供对 VNet 应有权访问的所有 Azure Monitor 资源的访问权限。
* 一个 AMPLS 对象最多可连接到 50 个 Azure Monitor 资源。
* 一个 Azure Monitor 资源（工作区或 Application Insights 组件）最多可连接到 5 个 AMPLS。
* 一个 AMPLS 对象最多可连接到 10 个专用终结点。

在下图中：
* 每个 VNet 仅连接到一个 AMPLS 对象。
* AMPLS A 使用 50 个可能 Azure Monitor 资源连接中的其中 3 个，连接到两个工作区和一个 Application Insights 组件。
* Workspace2 使用 5 个可能的 AMPLS 连接中的其中 2 个，连接到 AMPLS A 和 AMPLS B。
* AMPLS B 使用 10 个可能的专用终结点连接中的其中 2 个，连接到 2 个 VNet (VNet2 和 VNet3) 的专用终结点。

![AMPLS 限制关系图](./media/private-link-security/ampls-limits.png)


## <a name="control-network-access-to-your-resources"></a>控制对资源的网络访问
Log Analytics 工作区或 Application Insights 组件可以设置为：
* 接受或阻止来自公共网络（未连接到资源 AMPLS 的网络）的引入。
* 接受或阻止来自公共网络（未连接到资源 AMPLS 的网络）的查询。

根据这一粒度，你可以按需设置每个工作区的访问权限。 例如，你只能通过专用链接连接的网络（即特定 VNet）接受引入，但仍可以选择接受来自所有网络（公用网络和专用网络）的查询。 

阻止来自公用网络的查询意味着连接的 AMPLS 之外的客户端（计算机、SDK 等）无法查询资源中的数据。 这些数据包括日志、指标和实时指标流。 阻止来自公共网络的查询会影响运行这些查询的所有体验，如工作簿、仪表板、Azure 门户中的见解以及从 Azure 门户外部运行的查询。

有关配置详细信息，请参阅[设置资源访问标志](./private-link-configure.md#set-resource-access-flags)。

### <a name="exceptions"></a>例外

#### <a name="diagnostic-logs"></a>诊断日志
通过[诊断设置](../essentials/diagnostic-settings.md)上传到工作区的日志和指标会通过安全的专用 Microsoft 通道，且不受这些设置控制。

#### <a name="azure-resource-manager"></a>Azure 资源管理器
上述访问限制也适用于资源中的数据。 然而，配置更改（例如打开或关闭这些访问设置）由 Azure 资源管理器进行管理。 若要控制这些设置，应使用适当的角色、权限、网络控件和审核来限制对资源的访问。 有关详细信息，请参阅 [Azure Monitor 角色、权限和安全性](../roles-permissions-security.md)

> [!NOTE]
> 通过 Azure 资源管理 (ARM) API 发送的查询无法使用 Azure Monitor 专用链接。 仅当目标资源允许来自公共网络的查询时（通过“网络隔离”边栏选项卡或[使用 CLI](./private-link-configure.md#set-resource-access-flags) 设置），这些查询才能通过。
>
> 以下体验可通过 ARM API 运行查询：
> * Sentinel
> * LogicApp 连接器
> * 更新管理解决方案
> * 更改跟踪解决方案
> * VM Insights
> * 容器见解
> * Log Analytics 的“工作区摘要”边栏选项卡（其中显示解决方案仪表板）

## <a name="application-insights-considerations"></a>Application Insights 注意事项
* 需要将托管受监视工作负载的资源添加到专用链接中。 请参阅[为 Azure Web 应用使用专用终结点](../../app-service/networking/private-endpoint.md)查看示例。
* 不使用门户的体验也必须在包含受监视工作负荷的专用链接 VNET 上运行。
* 为了支持对探查器和调试程序使用专用链接，需要[提供自己的存储帐户](../app/profiler-bring-your-own-storage.md) 

> [!NOTE]
> 若要完全保护基于工作区的 Application Insights，需要锁定对 Application Insights 资源和基础 Log Analytics 工作区的访问。

## <a name="log-analytics-considerations"></a>Log Analytics 注意事项

### <a name="log-analytics-solution-packs-download"></a>Log Analytics 解决方案包下载
Log Analytics 代理需要访问全局存储帐户才能下载解决方案包。 在 2021 年 4 月 19 日或之后（或从 2021 年 6 月开始在 Azure 主权云上）创建的专用链接设置可以通过专用链接访问代理的解决方案包存储。 可通过为“blob.core.windows.net”创建的 DNS 区域实现此功能。

如果专用链接设置是在 2021 年 4 月 19 日之前创建的，则无法通过专用链接访问解决方案包存储。 若要处理此情况，可使用下列任一方式：
* 重新创建 AMPLS 和与其连接的专用终结点
* 通过将以下规则添加到防火墙允许列表，使代理可以通过其公共终结点访问存储帐户：

    | 云环境 | 代理资源 | 端口 | 方向 |
    |:--|:--|:--|:--|
    |Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | 出站
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  出站
    |Azure 中国世纪互联      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | 出站

### <a name="collecting-custom-logs-and-iis-log-over-private-link"></a>通过专用链接收集自定义日志和 IIS 日志
在自定义日志的引入过程中会使用存储帐户。 默认使用服务托管的存储帐户。 但若要通过专用链接引入自定义日志，必须使用你自己的存储帐户并将其与 Log Analytics 工作区关联。 请查看更多详细信息，了解如何使用[命令行](/cli/azure/monitor/log-analytics/workspace/linked-storage)设置此类帐户。

要详细了解如何连接自己的存储帐户，请参阅[使用客户拥有的存储帐户引入日志](private-storage.md)

### <a name="automation"></a>自动化
如果使用需要自动化帐户的 Log Analytics 解决方案（如更新管理、更改跟踪或盘存），则还应为自动化帐户创建一个专用链接。 有关详细信息，请参阅[使用 Azure 专用链接将网络安全地连接到 Azure 自动化](../../automation/how-to/private-link-security.md)。

> [!NOTE]
> 某些产品和 Azure 门户体验通过 Azure 资源管理器查询数据，因此如果不将专用链接设置也应用到资源管理器，便无法通过专用链接查询数据。 若要解决这一问题，可以将资源配置为接受来自公共网络的查询，如[控制对资源的网络访问](./private-link-design.md#control-network-access-to-your-resources)中所述（引入可仍然仅限于专用链接网络）。
我们确定了以下产品和体验通过 Azure 资源管理器查询工作区：
> * LogicApp 连接器
> * 更新管理解决方案
> * 更改跟踪解决方案
> * 门户中的“工作区摘要”边栏选项卡（其中显示解决方案仪表板）
> * VM Insights
> * 容器见解

## <a name="requirements"></a>要求

### <a name="network-subnet-size"></a>网络子网大小
支持的最小 IPv4 子网为 /27（使用 CIDR 子网定义）。 虽然 Azure VNet [最小可以为 /29](../../virtual-network/virtual-networks-faq.md#how-small-and-how-large-can-vnets-and-subnets-be)，但 Azure [保留了 5 个 IP 地址](../../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)，并且 Azure Monitor 专用链接设置至少需要 11 个额外的 IP 地址，即使连接到单个工作区也是如此。 [查看终结点的 DNS 设置](./private-link-configure.md#reviewing-your-endpoints-dns-settings)以获取 Azure Monitor 专用链接终结点的详细列表。


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

### <a name="programmatic-access"></a>以编程方式访问
要在专用网络上将 REST API、[CLI](/cli/azure/monitor) 或 PowerShell 与 Azure Monitor 结合使用，请在防火墙中添加 AzureActiveDirectory 和 AzureResourceManager [服务标记](../../virtual-network/service-tags-overview.md) 。

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>从内容分发网络下载 Application Insights SDK
在脚本中捆绑 JavaScript 代码，让浏览器不尝试从 CDN 中下载代码。 有关示例，可查看 [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>浏览器 DNS 设置
如果通过专用链接连接到 Azure Monitor 资源，则通往这些资源的流量必须经过在网络上配置的专用终结点。 若要启用专用终结点，请根据[连接到专用终结点](./private-link-configure.md#connect-to-a-private-endpoint)中的所述内容更新 DNS 设置。 一些浏览器使用自己的 DNS 设置，而不是你设置的 DNS 设置。 浏览器可能会尝试连接到 Azure Monitor 公用终结点，并完全绕过专用链接。 验证你的浏览器设置不会替代或缓存旧的 DNS 设置。 

### <a name="querying-limitation-externaldata-operator"></a>查询限制：externaldata 运算符
专用链接不支持[`externaldata`运算符](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor)，因为运算符从存储帐户读取数据，但不保证以专用方式访问存储。

## <a name="next-steps"></a>后续步骤
- 了解如何[配置专用链接](private-link-configure.md)
- 了解[私有存储](private-storage.md)
- 了解[自动化的专用链接](../../automation/how-to/private-link-security.md)