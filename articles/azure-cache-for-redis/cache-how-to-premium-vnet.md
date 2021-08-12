---
title: 配置虚拟网络 - 高级层 Azure Cache for Redis 实例
description: 了解如何为高级层 Azure Cache for Redis 实例创建和管理虚拟网络支持
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: b8e70b1450aa7c121c88e508667dc37ad01d212e
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536878"
---
# <a name="configure-virtual-network-support-for-a-premium-azure-cache-for-redis-instance"></a>如何为高级 Azure Cache for Redis 实例配置虚拟网络支持

[Azure 虚拟网络](https://azure.microsoft.com/services/virtual-network/)部署提供增强的安全性和隔离性，并提供子网、访问控制策略以及其他用来进一步限制访问的功能。 当 Azure Cache for Redis 实例配置有虚拟网络时，它不可公开寻址。 相反，只能从虚拟网络中的虚拟机和应用程序访问实例。 本文说明如何为高级层 Azure Cache for Redis 实例配置虚拟网络支持。

> [!NOTE]
> Azure Cache for Redis 同时支持经典部署模型和 Azure 资源管理器虚拟网络。
>

## <a name="set-up-virtual-network-support"></a>设置虚拟网络支持

在创建缓存期间，可在“新建 Azure Cache for Redis”窗格中配置虚拟网络支持。

1. 若要创建高级层缓存，请登录到 [Azure 门户](https://portal.azure.com)并选择“创建资源”。  也可以使用资源管理器模板、PowerShell 或 Azure CLI 创建缓存。 若要详细了解如何创建 Azure Cache for Redis 实例，请参阅[创建缓存](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="屏幕截图显示了“创建资源”。":::

1. 在“新建”页上，选择“数据库”。  然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="屏幕截图显示了如何选择“Azure Cache for Redis”。":::

1. 在“新建 Redis 缓存”页上，配置新的高级层缓存的设置。

   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 |
   | **订阅** | 从下拉列表中选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 |
   | **资源组** | 从下拉列表中选择一个资源组，或者选择“新建”并输入一个新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 |
   | **位置** | 从下拉列表中选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
   | **缓存类型** |从下拉列表中选择一个高级层缓存，以配置高级层功能。 有关详细信息，请参阅 [Azure Redis 缓存定价](https://azure.microsoft.com/pricing/details/cache/)。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅 [Azure Cache for Redis 概述](cache-overview.md)。 |

1. 选择“网络”选项卡，或选择页面底部的“网络”按钮 。

1. 在“网络”选项卡中，选择“虚拟网络”作为连接方法 。 若要使用新的虚拟网络，请先创建虚拟网络，方法是执行[使用 Azure 门户创建虚拟网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)或[使用 Azure 门户创建虚拟网络（经典）](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)中的步骤。 然后返回“新建 Azure Cache for Redis”窗格来创建并配置高级层缓存。

   > [!IMPORTANT]
   > 将 Azure Cache for Redis 部署到资源管理器虚拟网络时，缓存必须位于专用子网中，该子网只能包含 Azure Cache for Redis 实例，而不能包含其他资源。 如果尝试将 Azure Cache for Redis 实例部署到包含其他资源的资源管理器虚拟网络子网中或已分配 NAT 网关，部署会失败。
   >
   >

   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **虚拟网络** | 从下拉列表中选择你的虚拟网络。 | 选择你的缓存所在的订阅和位置中的虚拟网络。 |
   | **子网** | 从下拉列表中选择你的子网。 | 应以 CIDR 表示法表示子网的地址范围（例如 192.168.1.0/24）。 它必须包含在虚拟网络的地址空间中。 |
   | **静态 IP 地址** | （可选）输入静态 IP 地址。 | 如果你未指定静态 IP 地址，系统会自动选择一个 IP 地址。 |

   > [!IMPORTANT]
   > Azure 会保留每个子网中的某些 IP 地址，不可以使用这些地址。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   >
   > 除了 Azure 虚拟网络基础结构使用的 IP 地址以外，子网中的每个 Azure Cache for Redis 实例还为每个分片使用两个 IP 地址，为负载均衡器使用一个额外的 IP 地址。 非群集缓存被视为包含一个分片。
   >

1. 选择“下一步:高级”选项卡，或者选择“下一步:高级”按钮（位于页面底部）。

1. 在高级层缓存实例的“高级”选项卡中，配置非 TLS 端口、群集和数据暂留的设置。

1. 选择“下一步:标记”选项卡，或者选择“下一步:标记”按钮（位于页面底部）。

1. 如果希望对资源进行分类，可以在“标记”选项卡中输入名称和值。

1. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”选项卡，Azure 将在此处验证配置。

1. 显示绿色的“已通过验证”消息后，选择“创建” 。

创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。  创建缓存之后，可以在“资源”菜单中选择“虚拟网络”来查看虚拟网络的配置。

![虚拟网络][redis-cache-vnet-info]

若要在使用虚拟网络时连接到你的 Azure Cache for Redis 实例，请在连接字符串中指定你的缓存的主机名，如以下示例所示：

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Azure Cache for Redis 虚拟网络常见问题解答

以下列表包含有关 Azure Redis 缓存缩放的常见问题的解答。

* Azure Cache for Redis 和虚拟网络有哪些常见的配置错误问题？
* [如何验证我的缓存在虚拟网络中是否正常工作？](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* 尝试连接到虚拟网络中的 Azure Cache for Redis 实例时，为何会收到一个指出远程证书无效的错误？
* [是否可以将虚拟网络与标准或基本缓存一起使用？](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* 为什么在某些子网中创建 Azure Cache for Redis 实例会失败，而在其他子网中不会失败？
* [子网地址空间的要求是什么？](#what-are-the-subnet-address-space-requirements)
* [能否将虚拟网络对等互连到缓存？](#can-i-connect-to-my-cache-from-a-peered-virtual-network)
* [当缓存承载在虚拟网络中时，是否所有缓存功能都可以使用？](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Azure Cache for Redis 和虚拟网络有哪些常见的配置错误问题？

当 Azure Cache for Redis 承载在虚拟网络中时，将使用下述表中的端口。

>[!IMPORTANT]
>如果下述表中的端口被阻止，缓存可能无法正常使用。 在虚拟网络中使用 Azure Cache for Redis 时，这些端口中的一个或多个被阻止是最常见的配置错误问题。
>

* [出站端口要求](#outbound-port-requirements)
* [入站端口要求](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>出站端口要求

出站端口有九个要求。 这些范围内的出站请求要么 a) 出站到缓存运行所需的其他服务，要么 b) 在 Redis 子网内部进行节点间通信。 对于异地复制，主缓存和副本缓存的子网之间的通信存在其他出站要求。

| 端口 | 方向 | 传输协议 | 目的 | 本地 IP | 远程 IP |
| --- | --- | --- | --- | --- | --- |
| 80、443 |出站 |TCP |Azure 存储/PKI (Internet) 上的 Redis 依赖关系 | （Redis 子网） |* <sup>4</sup> |
| 443 | 出站 | TCP | Azure Key Vault 和 Azure Monitor 上的 Redis 依赖关系 | （Redis 子网） | AzureKeyVault、AzureMonitor <sup>1</sup> |
| 53 |出站 |TCP/UDP |DNS（Internet/虚拟网络）上的 Redis 依赖关系 | （Redis 子网） | 168.63.129.16 和 169.254.169.254 <sup>2</sup> 以及子网的任何自定义 DNS 服务器 <sup>3</sup> |
| 8443 |出站 |TCP |Redis 的内部通信 | （Redis 子网） | （Redis 子网） |
| 10221-10231 |出站 |TCP |Redis 的内部通信 | （Redis 子网） | （Redis 子网） |
| 20226 |出站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |
| 13000-13999 |出站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |
| 15000-15999 |出站 |TCP |Redis 内部通信和异地复制 | （Redis 子网） |（Redis 子网）（地域副本对等子网） |
| 6379-6380 |出站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |

<sup>1</sup> 可以将服务标记 AzureKeyVault 和 AzureMonitor 用于资源管理器网络安全组 (NSG)。

<sup>2</sup> Microsoft 拥有的这些 IP 地址用于对为 Azure DNS 提供服务的主机 VM 进行寻址。

<sup>3</sup> 如果子网不包含自定义 DNS 服务器或不采用忽略自定义 DNS 的较新 Redis 缓存，则不需要此信息。

<sup>4</sup> 有关详细信息，请参阅[其他虚拟网络连接要求](#additional-virtual-network-connectivity-requirements)。

#### <a name="geo-replication-peer-port-requirements"></a>异地复制对等端口要求

如果在 Azure 虚拟网络中的缓存之间使用异地复制：a) 在入站和出站方向为整个子网取消阻止端口 15000-15999，b) 对两个缓存使用。 使用此配置时，子网中的所有副本组件都可以直接相互通信，即使将来发生异地故障转移。

#### <a name="inbound-port-requirements"></a>入站端口要求

入站端口范围有八个要求。 这些范围中的入站请求是从同一虚拟网络中托管的其他服务入站的入站请求。 或者是相对于 Redis 子网通信而言的内部请求。

| 端口 | 方向 | 传输协议 | 目的 | 本地 IP | 远程 IP |
| --- | --- | --- | --- | --- | --- |
| 6379、6380 |入站 |TCP |与 Redis 的客户端通信、Azure 负载均衡 | （Redis 子网） | （Redis 子网）、（客户端子网）、AzureLoadBalancer <sup>1</sup> |
| 8443 |入站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |
| 8500 |入站 |TCP/UDP |Azure 负载均衡 | （Redis 子网） | AzureLoadBalancer |
| 10221-10231 |入站 |TCP |与 Redis 群集的客户端通信、Redis 内部通信 | （Redis 子网） |（Redis 子网）、AzureLoadBalancer、（客户端子网） |
| 13000-13999 |入站 |TCP |与 Redis 群集的客户端通信、Azure 负载均衡 | （Redis 子网） | （Redis 子网）、（客户端子网）、AzureLoadBalancer |
| 15000-15999 |入站 |TCP |与 Redis 群集的客户端通信、Azure 负载均衡和异地复制 | （Redis 子网） | （Redis 子网）、（客户端子网）、AzureLoadBalancer、（地域副本对等子网） |
| 16001 |入站 |TCP/UDP |Azure 负载均衡 | （Redis 子网） | AzureLoadBalancer |
| 20226 |入站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |

<sup>1</sup> 可以使用服务标记 AzureLoadBalancer（用于资源管理器）或 AZURE_LOADBALANCER（用于经典部署模型）来创作 NSG 规则。

#### <a name="additional-virtual-network-connectivity-requirements"></a>其他虚拟网络连接要求

在虚拟网络中，可能一开始无法满足 Azure Cache for Redis 的网络连接要求。 在虚拟网络中使用时，Azure Cache for Redis 需要以下所有项才能正常运行：

* 与全球 Azure 存储终结点建立的出站网络连接。 包括位于 Azure Cache for Redis 实例所在区域中的终结点，以及位于其他 Azure 区域中的存储终结点。 Azure 存储终结点在以下 DNS 域下解析：*table.core.windows.net*、*blob.core.windows.net*、*queue.core.windows.net* 和 *file.core.windows.net*。
* 与 ocsp.digicert.com、crl4.digicert.com、ocsp.msocsp.com、mscrl.microsoft.com、crl3.digicert.com、cacerts.digicert.com、oneocsp.microsoft.com 和 crl.microsoft.com 的出站网络连接       。 需要此连接才能支持 TLS/SSL 功能。
* 虚拟网络的 DNS 配置必须能够解析前面几点所提到的所有终结点和域。 确保已针对虚拟网络配置并维护有效的 DNS 基础结构即可符合这些 DNS 要求。
* 与以下 Azure Monitor 终结点的出站网络连接，这些终结点在以下 DNS 域下解析：shoebox2-black.shoebox2.metrics.nsatc.net、north-prod2.prod2.metrics.nsatc.net、azglobal-black.azglobal.metrics.nsatc.net、shoebox2-red.shoebox2.metrics.nsatc.net、east-prod2.prod2.metrics.nsatc.net、azglobal-red.azglobal.metrics.nsatc.net、shoebox3.prod.microsoftmetrics.com、shoebox3-red.prod.microsoftmetrics.com、shoebox3-black.prod.microsoftmetrics.com、azredis-red.prod.microsoftmetrics.com 和 azredis-black.prod.microsoftmetrics.com           。

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>如何验证我的缓存在虚拟网络中是否可以正常使用？

>[!IMPORTANT]
>当你连接到承载在虚拟网络中的 Azure Cache for Redis 实例时，你的缓存客户端必须位于同一虚拟网络中，或者位于同一 Azure 区域的启用了虚拟网络对等互连的虚拟网络中。 当前不支持全局虚拟网络对等互连。 此要求适用于任何测试应用程序或诊断 ping 工具。 无论客户端应用程序承载在哪里，都必须配置 NSG 或其他网络层，这样客户端的网络流量才能到达 Azure Cache for Redis 实例。
>

根据上一部分所述配置端口要求后，可通过以下步骤来验证缓存是否可以正常使用：

* [重新启动](cache-administration.md#reboot)所有缓存节点。 如果无法访问所有所需的缓存依赖项（如[入站端口需求](cache-how-to-premium-vnet.md#inbound-port-requirements)和[出站端口需求](cache-how-to-premium-vnet.md#outbound-port-requirements)中所述），缓存将无法成功重启。
* 重启缓存节点后（由 Azure 门户中的缓存状态报告），可执行以下测试：
  + 使用 [tcping](https://www.elifulkerson.com/projects/tcping.php)，从缓存所在的虚拟网络中的某台计算机对缓存终结点发出 ping 命令（使用端口 6380）。 例如：

    `tcping.exe contosocache.redis.cache.windows.net 6380`

    如果 `tcping` 工具报告端口已打开，则可从虚拟网络中的客户端连接缓存。

  + 进行测试的另一种方法：创建测试缓存客户端，使其连接到缓存，以便添加和检索缓存的某些项。 测试缓存客户端可以是使用 StackExchange.Redis 的控制台应用程序。 将示例客户端应用程序安装到缓存所在虚拟网络中的一个 VM 上。 然后运行它来验证与缓存的连接。

### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>尝试连接到虚拟网络中的 Azure Cache for Redis 实例时，为何会收到一个指出远程证书无效的错误？

尝试连接到虚拟网络中的 Azure Cache for Redis 实例时，你会看到类似于以下内容的证书验证错误：

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

这可能是因为你在通过 IP 地址来连接主机。 建议使用主机名。 换而言之，请使用以下字符串：

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

避免使用类似于以下连接字符串的 IP 地址：

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

如果无法解析 DNS 名称，则可使用某些客户端库包括的 `sslHost`（由 StackExchange.Redis 客户端提供）之类的配置选项。 此选项允许你替代用于证书验证的主机名。 例如：

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>是否可以将虚拟网络与标准或基本缓存一起使用？

虚拟网络只能与高级层缓存一起使用。

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>为什么在某些子网中创建 Azure Cache for Redis 实例会失败，而在其他子网中不会失败？

如果要将 Azure Cache for Redis 实例部署到虚拟网络，则缓存必须位于不包含其他资源类型的专用子网中。 如果尝试将 Azure Cache for Redis 实例部署到包含其他资源（例如 Azure 应用程序网关实例和出站 NAT）的资源管理器虚拟网络子网，则部署通常会失败。 先删除其他类型的现有资源，然后再创建新的 Azure Cache for Redis 实例。

子网中还必须有足够的可用 IP 地址。

### <a name="what-are-the-subnet-address-space-requirements"></a>子网地址空间的要求是什么？

Azure 会保留每个子网中的某些 IP 地址，不可以使用这些地址。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

除了 Azure 虚拟网络基础结构使用的 IP 地址以外，子网中的每个 Azure Cache for Redis 实例还为每个群集分片使用两个 IP 地址，为其他副本（如果有）使用 IP 地址。 为负载均衡器使用另外一个 IP 地址。 非群集缓存被视为包含一个分片。

### <a name="can-i-connect-to-my-cache-from-a-peered-virtual-network"></a>能否将虚拟网络对等互连到缓存？

如果虚拟网络位于同一区域，则可以使用虚拟网络对等互连或 VPN 网关 VNET 到 VNET 连接来连接虚拟网络。

如果对等互连的 Azure 虚拟网络位于不同的区域：由于基本负载均衡器的约束，区域 1 中的客户端 VM 无法通过其负载均衡的 IP 地址访问区域 2 中的缓存。 也就是说，除非它是具有标准负载均衡器的缓存（当前仅使用可用性区域创建的缓存）。

有关虚拟网络对等互连约束的详细信息，请参阅“虚拟网络 - 对等互连 - 要求和约束”。 一种解决方案是使用 VPN 网关 VNET 到 VNET 连接，而不是虚拟网络对等互连。

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>当缓存承载在虚拟网络中时，是否所有缓存功能都可以使用？

如果缓存是虚拟网络的一部分，则只有虚拟网络中的客户端可以访问缓存。 因此，以下缓存管理功能目前无法使用。

* Redis 控制台：由于 Redis 控制台在本地浏览器中运行（通常在未连接到虚拟网络的开发人员计算机上），因此它无法连接到缓存。

## <a name="use-expressroute-with-azure-cache-for-redis"></a>将 ExpressRoute 与 Azure Redis 缓存配合使用

客户可以将 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 线路连接到其虚拟网络基础结构。 通过这种方式，可以将本地网络扩展到 Azure。

默认情况下，新创建的 ExpressRoute 线路不会在虚拟网络上执行强制隧道（播发一个默认的路由，即 0.0.0.0/0）。 因此，可以直接从虚拟网络建立出站 Internet 连接。 客户端应用程序可以连接到包括 Azure Cache for Redis 实例在内的其他 Azure 终结点。

常见的客户配置是使用强制隧道（播发默认路由），以强制出站 Internet 流量改为流向本地。 如果接下来出站流量在本地遭到阻止，此通信流会断开与 Azure Cache for Redis 的连接，这样 Azure Cache for Redis 实例就无法与其依赖项通信。

解决方法是在包含 Azure Cache for Redis 实例的子网上定义一个或多个用户定义的路由 (UDR)。 UDR 定义了要遵循的子网特定路由，而不是默认路由。

如果可以，请使用以下配置：

* ExpressRoute 配置会播发 0.0.0.0/0 并默认使用强制隧道将所有出站流量发送到本地。
* 应用于包含 Azure Cache for Redis 实例的子网的 UDR 使用公共 Internet 的 TCP/IP 流量工作路由来定义 0.0.0.0/0。 例如，它可以将下一跃点类型设置为“internet”。

这些步骤的组合效应是子网级 UDR 优先于 ExpressRoute 强制隧道，并可确保从 Azure Cache for Redis 实例进行出站 Internet 访问。

由于性能方面的原因，使用 ExpressRoute 从本地应用程序连接到 Azure Cache for Redis 实例不是典型的使用方案。 为获得最佳性能，应将 Azure Cache for Redis 客户端与 Azure Cache for Redis 实例置于同一区域中。

>[!IMPORTANT]
>UDR 中定义的路由 *必须* 足够明确，以便优先于 ExpressRoute 配置所播发的任何路由。 下面的示例使用的 0.0.0.0/0 地址范围很宽泛，因此可能会意外地被那些使用更明确地址范围的路由播发重写。

>[!WARNING]
>未正确交叉播发从公共对等路径到专用对等路径的路由的 ExpressRoute 配置不支持 Azure Cache for Redis。 已配置公共对等互连的 ExpressRoute 配置会收到来自 Microsoft 的大量 Microsoft Azure IP 地址范围的路由播发。 如果这些地址范围在专用对等路径上未正确交叉播发，则结果是来自 Azure Redis 缓存实例子网的所有出站网络数据包都不会正确地使用强制隧道发送到客户的本地网络基础结构。 此网络流会破坏 Azure Redis 缓存。 此问题的解决方法是停止从公共对等路径到专用对等路径的交叉播发路由。

[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)中提供了有关 UDR 的背景信息。

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)。

## <a name="next-steps"></a>后续步骤

了解有关 Azure Cache for Redis 功能的详细信息。

* [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
