---
title: 为 Redis 实例配置虚拟网络-高级层 Azure 缓存
description: 了解如何为 Redis 实例创建和管理高级 Azure 缓存的虚拟网络支持。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 9343bc424a0a38da173a56701528c4fd7549aabd
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734651"
---
# <a name="configure-virtual-network-support-for-a-premium-tier-azure-cache-for-redis-instance"></a>为 Redis 实例的高级层 Azure 缓存配置虚拟网络支持

Azure Redis 缓存具有不同的缓存产品/服务，使缓存大小和功能的选择更加灵活。 高级层功能包括群集、持久性和虚拟网络支持。 虚拟网络是云中的专用网络。 使用虚拟网络配置用于 Redis 实例的 Azure 缓存时，它不会公开寻址，只能从虚拟网络中的虚拟机和应用程序进行访问。 本文介绍如何为 Redis 实例的高级层 Azure 缓存配置虚拟网络支持。

> [!NOTE]
> 适用于 Redis 的 azure 缓存支持经典部署模型和 Azure 资源管理器虚拟网络。
> 

## <a name="why-virtual-network"></a>为什么选择虚拟网络？

[Azure 虚拟网络](https://azure.microsoft.com/services/virtual-network/) 部署为 Redis 实例的 azure 缓存提供增强的安全性和隔离性，并提供子网、访问控制策略和其他功能，以进一步限制访问。

## <a name="virtual-network-support"></a>虚拟网络支持

在创建缓存期间，在 **新的 Azure Cache For Redis** 窗格上配置虚拟网络支持。

1. 若要创建高级层缓存，请登录到 [Azure 门户](https://portal.azure.com) ，然后选择 " **创建资源**"。 除了在 Azure 门户中创建缓存外，还可以通过使用资源管理器模板、PowerShell 或 Azure CLI 来创建缓存。 有关如何为 Redis 实例创建 Azure 缓存的详细信息，请参阅 [创建缓存](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="显示 &quot;创建资源&quot; 的屏幕截图。":::
   
1. 在 " **新建** " 页上，选择 " **数据库**"。 然后 **为 Redis 选择 "Azure 缓存**"。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="显示为 Redis 选择 Azure 缓存的屏幕截图。":::

1. 在 " **新建 Redis 缓存** " 页上，配置新的高级层缓存的设置。
   
   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 名称必须以数字或字母开头和结尾，并且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 |
   | **订阅** | 从下拉列表中选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 |
   | **资源组** | 从下拉列表中选择资源组，或选择 **"新建" 并输入** 新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 |
   | **位置** | 从下拉列表中选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
   | **缓存类型** |从下拉列表中选择一个高级层缓存，以配置高级层功能。 有关详细信息，请参阅 [Azure Redis 缓存定价](https://azure.microsoft.com/pricing/details/cache/)。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅 [Azure Cache For Redis 概述](cache-overview.md)。 |

1. 选择 " **网络** " 选项卡，或选择页面底部的 " **网络** " 按钮。

1. 在 " **网络** " 选项卡上，选择 " **虚拟网络** " 作为连接方法。 若要使用新的虚拟网络，请先按照 [使用 Azure 门户创建虚拟网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network) 中的步骤创建该网络，或 [使用 Azure 门户创建虚拟网络 (经典) ](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)。 然后返回到新的 " **用于 Redis 的 Azure 缓存** " 窗格，以创建和配置高级层缓存。

   > [!IMPORTANT]
   > 将 Redis 的 Azure Cache 部署到资源管理器虚拟网络时，缓存必须位于不包含其他资源的专用子网中，Azure Cache for Redis 实例除外。 如果尝试将 Redis 实例的 Azure 缓存部署到包含其他资源的资源管理器虚拟网络子网，则部署将失败。
   > 
   > 

   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **虚拟网络** | 从下拉列表中选择你的虚拟网络。 | 选择与缓存位于同一订阅和位置的虚拟网络。 |
   | **子网** | 从下拉列表中选择子网。 | 子网的地址范围应为 CIDR 表示法 (例如 192.168.1.0/24) 。 它必须包含在虚拟网络的地址空间中。 |
   | **静态 IP 地址** | （可选）输入静态 IP 地址。 | 如果未指定静态 IP 地址，则会自动选择一个 IP 地址。 |

   > [!IMPORTANT]
   > Azure 会保留每个子网中的某些 IP 地址，不可以使用这些地址。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > 除了 Azure 虚拟网络基础结构使用的 IP 地址外，子网中的 Redis 实例的每个 Azure Cache 都使用每个分片的两个 IP 地址和一个负载均衡器的其他 IP 地址。 非聚集缓存被视为具有一个分片。
   > 

1. 选择下面的 " **高级** " 选项卡，或选择页面底部的 " **高级** " 按钮。

1. 在高级层缓存实例的 " **高级** " 选项卡上，配置非 TLS 端口、群集和数据暂留的设置。

1. 选择 " **下一步：标记** " 选项卡，或选择页面底部的 " **标记** " 按钮。

1. （可选）在 " **标记** " 选项卡上，如果要对资源进行分类，请输入名称和值。

1. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”选项卡，Azure 将在此处验证配置。

1. 显示绿色的“已通过验证”消息后，选择“创建” 。

创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。  创建缓存后，可以通过从 "**资源**" 菜单中选择 "**虚拟网络**" 查看虚拟网络的配置。

![虚拟网络][redis-cache-vnet-info]

若要在使用虚拟网络时连接到 Azure Cache for Redis 实例，请在连接字符串中指定缓存的主机名，如以下示例中所示：

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

## <a name="azure-cache-for-redis-virtual-network-faq"></a>适用于 Redis 虚拟网络的 Azure 缓存常见问题

以下列表包含有关 Azure Redis 缓存缩放的常见问题的解答。

* Azure Cache 对于 Redis 和虚拟网络有哪些常见的错误配置问题？
* [如何验证缓存是否在虚拟网络中运行？](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* 尝试连接到虚拟网络中的 Redis 实例的 Azure 缓存时，为什么会收到指出远程证书无效的错误？
* [是否可以将虚拟网络用于标准或基本缓存？](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* 为什么在某些子网中创建用于 Redis 实例的 Azure 缓存失败，而在其他子网中不会失败？
* [子网地址空间的要求是什么？](#what-are-the-subnet-address-space-requirements)
* [在虚拟网络中托管缓存时，是否可以使用所有缓存功能？](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Azure Cache 对于 Redis 和虚拟网络有哪些常见的错误配置问题？

在虚拟网络中托管 Redis 的 Azure 缓存时，将使用下表中的端口。

>[!IMPORTANT]
>如果下表中的端口被阻止，则缓存可能无法正常工作。 如果在虚拟网络中使用 Azure Cache for Redis，则会阻止其中一个或多个端口出现最常见的错误配置问题。
> 

- [出站端口要求](#outbound-port-requirements)
- [入站端口要求](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>出站端口要求

出站端口有九个要求。 这些范围内的出站请求要么出站到缓存运行所需的其他服务，要么在 Redis 子网内部进行节点间通信。 对于异地复制，主缓存和副本缓存的子网之间的通信存在其他出站要求。

| 端口 | 方向 | 传输协议 | 目的 | 本地 IP | 远程 IP |
| --- | --- | --- | --- | --- | --- |
| 80、443 |出站 |TCP |Redis Azure 存储/PKI 上的依赖关系 (internet)  | （Redis 子网） |* <sup>4</sup> |
| 443 | 出站 | TCP | Azure Key Vault 和 Azure Monitor 上的 Redis 依赖关系 | （Redis 子网） | AzureKeyVault、AzureMonitor <sup>1</sup> |
| 53 |出站 |TCP/UDP |Redis DNS (internet/虚拟网络) 的依赖关系 | （Redis 子网） | 168.63.129.16 和 169.254.169.254 <sup>2</sup> 以及子网的任何自定义 DNS 服务器 <sup>3</sup> |
| 8443 |出站 |TCP |Redis 的内部通信 | （Redis 子网） | （Redis 子网） |
| 10221-10231 |出站 |TCP |Redis 的内部通信 | （Redis 子网） | （Redis 子网） |
| 20226 |出站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |
| 13000-13999 |出站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |
| 15000-15999 |出站 |TCP |Redis 和异地复制的内部通信 | （Redis 子网） |（Redis 子网）（地域副本对等子网） |
| 6379-6380 |出站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |

<sup>1</sup> 可以将服务标记 AzureKeyVault 和 AzureMonitor 与资源管理器的网络安全组 (nsg) 结合使用。

<sup>2</sup> 这些 IP 地址用于处理 Azure DNS 的主机 VM。

<sup>3</sup> 对于没有自定义 dns 服务器或更新的 Redis 缓存（忽略自定义 dns）的子网，不需要此信息。

<sup>4</sup> 有关详细信息，请参阅 [其他虚拟网络连接要求](#additional-virtual-network-connectivity-requirements)。

#### <a name="geo-replication-peer-port-requirements"></a>异地复制对等端口要求

如果在 Azure 虚拟网络中的缓存之间使用异地复制，则会在入站 *和* 出站方向将整个子网的端口15000-15999 取消阻止到这两个缓存。 使用此配置时，子网中的所有副本组件都可以直接相互通信，即使将来发生了异地故障转移。

#### <a name="inbound-port-requirements"></a>入站端口要求

入站端口范围有八个要求。 这些范围内的入站请求是入站来自同一虚拟网络中托管的其他服务或 Redis 子网通信的内部入站请求。

| 端口 | 方向 | 传输协议 | 目的 | 本地 IP | 远程 IP |
| --- | --- | --- | --- | --- | --- |
| 6379、6380 |入站 |TCP |与 Redis 的客户端通信、Azure 负载均衡 | （Redis 子网） | （Redis 子网）、（客户端子网）、AzureLoadBalancer <sup>1</sup> |
| 8443 |入站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |
| 8500 |入站 |TCP/UDP |Azure 负载均衡 | （Redis 子网） | AzureLoadBalancer |
| 10221-10231 |入站 |TCP |与 Redis 群集的客户端通信、Redis 的内部通信 | （Redis 子网） |（Redis 子网）、AzureLoadBalancer、（客户端子网） |
| 13000-13999 |入站 |TCP |与 Redis 群集的客户端通信、Azure 负载均衡 | （Redis 子网） | （Redis 子网）、（客户端子网）、AzureLoadBalancer |
| 15000-15999 |入站 |TCP |与 Redis 群集的客户端通信、Azure 负载平衡和异地复制 | （Redis 子网） | （Redis 子网）、（客户端子网）、AzureLoadBalancer、（地域副本对等子网） |
| 16001 |入站 |TCP/UDP |Azure 负载均衡 | （Redis 子网） | AzureLoadBalancer |
| 20226 |入站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |

<sup>1</sup> 可以将服务标记 AzureLoadBalancer 用于经典部署模型的资源管理器或 AZURE_LOADBALANCER，以创作 NSG 规则。

#### <a name="additional-virtual-network-connectivity-requirements"></a>其他虚拟网络连接要求

Azure Cache for Redis 的网络连接要求在虚拟网络中最初可能不满足。 在虚拟网络中使用时，用于 Redis 的 Azure 缓存要求所有以下各项正常运行：

* 与全球 Azure 存储终结点建立的出站网络连接。 包括位于与 Azure Cache for Redis 实例相同的区域中的终结点，以及位于 *其他* Azure 区域的存储终结点。 Azure 存储终结点在以下 DNS 域下解析： *table.core.windows.net*、 *blob.core.windows.net*、 *queue.core.windows.net* 和 *file.core.windows.net*。
* 与 *ocsp.digicert.com*、 *crl4.digicert.com*、 *ocsp.msocsp.com*、 *mscrl.microsoft.com*、 *crl3.digicert.com*、 *cacerts.digicert.com*、 *oneocsp.microsoft.com* 和 *crl.microsoft.com* 的出站网络连接。 需要此连接才能支持 TLS/SSL 功能。
* 虚拟网络的 DNS 设置必须能够解析前面几点所提到的所有终结点和域。 确保已针对虚拟网络配置并维护有效的 DNS 基础结构即可符合这些 DNS 要求。
* 与以下 Azure Monitor 终结点的出站网络连接，这些终结点在以下 DNS 域下解析： *shoebox2-black.shoebox2.metrics.nsatc.net*、 *north-prod2.prod2.metrics.nsatc.net*、 *azglobal-black.azglobal.metrics.nsatc.net*、 *shoebox2-red.shoebox2.metrics.nsatc.net*、 *east-prod2.prod2.metrics.nsatc.net* 和 *azglobal-red.azglobal.metrics.nsatc.net*。

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>如何验证缓存是否在虚拟网络中运行？

>[!IMPORTANT]
>当你连接到虚拟网络中托管的 Redis 实例的 Azure 缓存时，缓存客户端必须位于同一虚拟网络中，或者位于同一 Azure 区域中启用了虚拟网络对等互连的虚拟网络中。 当前不支持全局虚拟网络对等互连。 此要求适用于任何测试应用程序或诊断 ping 工具。 无论客户端应用程序在哪里托管，都必须配置 Nsg 或其他网络层，以允许客户端的网络流量到达 Azure Cache for Redis 实例。
>

按上一部分所述配置端口要求后，可以按照以下步骤验证缓存是否正常工作：

- [重新启动](cache-administration.md#reboot)所有缓存节点。 如果无法访问所有必需的缓存依赖项（如 [入站端口要求](cache-how-to-premium-vnet.md#inbound-port-requirements) 和 [出站端口要求](cache-how-to-premium-vnet.md#outbound-port-requirements)中所述），缓存将无法成功重启。
- 重新启动缓存节点后（如 Azure 门户中的缓存状态报告），可以执行以下测试：
  - 使用 [tcping](https://www.elifulkerson.com/projects/tcping.php)在与缓存位于同一虚拟网络中的计算机上使用端口 6380 Ping 缓存终结点。 例如：
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    如果该 `tcping` 工具报告端口已打开，则可以从虚拟网络中的客户端连接缓存。

  - 另一种测试方法是创建一个与缓存连接的测试缓存客户端（可以是使用 StackExchange.Redis 的简单控制台应用程序），然后在缓存中添加和检索一些项。 将示例客户端应用程序安装到与缓存位于同一虚拟网络中的 VM 上。 然后运行它以验证与缓存的连接。


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>尝试连接到虚拟网络中的 Redis 实例的 Azure 缓存时，为什么会收到指出远程证书无效的错误？

尝试连接到虚拟网络中的 Redis 实例的 Azure 缓存时，会看到如下所示的证书验证错误：

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

原因可能是 IP 地址正在连接到主机。 建议使用主机名。 换言之，使用以下字符串：

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

避免使用类似于以下连接字符串的 IP 地址：

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

如果无法解析 DNS 名称，某些客户端库将包括配置选项（如 `sslHost` ），这是由 stackexchange.redis. Redis 客户端提供的。 此选项允许您覆盖用于证书验证的主机名。 例如：

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>是否可以将虚拟网络用于标准或基本缓存？

虚拟网络仅可用于高级层缓存。

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>为什么在某些子网中创建用于 Redis 实例的 Azure 缓存失败，而在其他子网中不会失败？

如果要将适用于 Redis 的 Azure Cache 实例部署到虚拟网络，则该缓存必须在不包含任何其他资源类型的专用子网中。 如果尝试将 Azure Cache for Redis 实例部署到包含其他资源的资源管理器虚拟网络子网（如 Azure 应用程序网关实例和出站 NAT），部署通常会失败。 必须先删除其他类型的现有资源，然后才能为 Redis 实例创建新的 Azure Cache。

子网中还必须有足够的可用 IP 地址。

### <a name="what-are-the-subnet-address-space-requirements"></a>子网地址空间的要求是什么？

Azure 会保留每个子网中的某些 IP 地址，不可以使用这些地址。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

除了 Azure 虚拟网络基础结构使用的 IP 地址外，子网中的 Redis 实例的每个 Azure Cache 都将使用每个群集分片的两个 IP 地址，以及其他副本的其他 IP 地址（如果有）。 一个额外的 IP 地址用于负载均衡器。 非聚集缓存被视为具有一个分片。

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>在虚拟网络中托管缓存时，是否可以使用所有缓存功能？

当缓存是虚拟网络的一部分时，只有虚拟网络中的客户端可以访问缓存。 因此，以下缓存管理功能目前不起作用：

* **Redis 控制台**：由于 Redis 控制台在本地浏览器中运行，这通常在未连接到虚拟网络的开发人员计算机上，因此它无法连接到缓存。

## <a name="use-expressroute-with-azure-cache-for-redis"></a>将 ExpressRoute 与 Azure Redis 缓存配合使用

客户可将 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 线路连接到虚拟网络基础结构。 通过这种方式，他们将本地网络扩展到 Azure。

默认情况下，新创建的 ExpressRoute 线路不会对虚拟网络上的默认路由 (的默认路由（0.0.0.0/0) 进行强制隧道。 因此，可以直接从虚拟网络建立出站 internet 连接。 客户端应用程序可以连接到其他 Azure 终结点，其中包括用于 Redis 实例的 Azure 缓存。

常见的客户配置是使用强制隧道 (播发默认路由) ，这会强制出站 internet 流量改为在本地流动。 如果在本地阻止出站流量，以便 Redis 实例的 Azure 缓存无法与其依赖项通信，则此流量将断开与 Azure Cache for Redis 的连接。

解决方案是在包含 Azure Cache for Redis 实例的子网上，定义一个或多个用户定义路由 (Udr) 。 UDR 定义了要遵循的子网特定路由，而不是默认路由。

如果可以，请使用以下配置：

* ExpressRoute 配置播发 0.0.0.0/0，并在默认情况下强制隧道本地所有出站流量。
* 应用于包含用于 Redis 的 Azure 缓存的子网的 UDR 将定义 0.0.0.0/0，并将 TCP/IP 流量路由到公共 internet。 例如，它将 "下一跃点类型" 设置为 " *internet*"。

这些步骤的组合效果是，子网级 UDR 优先于 ExpressRoute 强制隧道，从而确保来自 Azure Cache for Redis 实例的出站 internet 访问。

由于性能方面的原因，使用 ExpressRoute 从本地应用程序连接到 Azure Cache for Redis 实例不是典型的使用方案。 为获得最佳性能，适用于 Redis 客户端的 Azure 缓存应与 Azure Cache for Redis 实例位于同一区域。

>[!IMPORTANT]
>UDR 中定义的路由 *必须* 足够明确，以便优先于 ExpressRoute 配置所播发的任何路由。 下面的示例使用广泛 0.0.0.0/0 地址范围，因此可能会被使用更具体地址范围的路由播发意外重写。

>[!WARNING]
>未 *正确地交叉播发从公共对等路径到专用对等路径的路由* 的 ExpressRoute 配置不支持适用于 Redis 的 Azure Cache。 已配置公共对等互连的 ExpressRoute 配置会收到来自 Microsoft 的大量 Microsoft Azure IP 地址范围的路由播发。 如果这些地址范围在专用对等路径上未正确交叉播发，则结果是来自 Azure Redis 缓存实例子网的所有出站网络数据包都不会正确地使用强制隧道发送到客户的本地网络基础结构。 此网络流会破坏 Azure Redis 缓存。 此问题的解决方法是停止从公共对等路径到专用对等路径的交叉播发路由。

[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)中提供了有关 udr 的背景信息。

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)。

## <a name="next-steps"></a>后续步骤

了解有关 Azure Cache for Redis 功能的详细信息。

* [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
