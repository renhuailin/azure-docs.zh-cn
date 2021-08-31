---
title: 使用 Azure 专用链接的 Azure Cache for Redis
description: Azure 专用终结点是一个网络接口，可以通过私密且安全的方式将你连接到 Azure 专用链接支持的 Azure Cache for Redis。 在本文中，你将了解如何使用 Azure 门户创建 Azure Cache、Azure 虚拟网络和专用终结点。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 3/31/2021
ms.openlocfilehash: 37955c748135768701fd3429c2e4182f5d18e70c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722998"
---
# <a name="azure-cache-for-redis-with-azure-private-link"></a>使用 Azure 专用链接的 Azure Cache for Redis

在本文中，你将了解如何通过 Azure 门户创建虚拟网络、Azure Cache for Redis 实例和专用终结点。 你还将学习如何向现有的 Azure Cache for Redis 实例添加专用终结点。

Azure 专用终结点是一个网络接口，可以通过私密且安全的方式将你连接到 Azure 专用链接支持的 Azure Cache for Redis。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> 目前不支持门户控制台和防火墙存储帐户持久性。 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>创建专用终结点和新的 Azure Cache for Redis 实例 

本部分将创建新的 Azure Cache for Redis 实例和专用终结点。

### <a name="create-a-virtual-network"></a>创建虚拟网络 

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“创建资源”。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="选择“创建资源”。":::

2. 在“新建”页面上，选择“网络”，然后选择“虚拟网络”  。

3. 选择“添加”，创建虚拟网络。

4. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此虚拟网络的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建虚拟网络和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **Name** | 输入虚拟网络名称。 | 该名称必须满足以下要求：以字母或数字开头；以字母、数字或下划线结尾；只包含字母、数字、下划线、句点或连字符。 | 
   | **区域** | 下拉并选择一个区域。 | 选择将使用虚拟网络的其他服务附近的[区域](https://azure.microsoft.com/regions/)。 |

5. 选择“IP 地址”选项卡  ，或选择页面底部的“下一步:  IP 地址”按钮。

6. 在“IP 地址”选项卡中，将“IPv4 地址空间”指定为采用 CIDR 表示法的一个或多个地址前缀（例如 192.168.1.0/24） 。

7. 在“子网名称”下，选择“默认”以编辑子网的属性 。

8. 在“编辑子网”窗格中，指定“子网名称”和“子网地址范围”  。 应以 CIDR 表示法表示子网的地址范围（例如 192.168.1.0/24）。 它必须包含在虚拟网络的地址空间中。

9. 选择“保存”。

10. 选择“查看 + 创建”  选项卡，或选择“查看 + 创建”  按钮。

11. 确认所有信息是否正确，然后选择“创建”以预配虚拟网络。

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>创建 Azure Cache for Redis 实例和专用终结点

若要创建缓存实例，请执行以下步骤。

1. 返回到 Azure 门户主页或打开边栏菜单，然后选择“创建资源”。 
   
1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="选择 Azure Cache for Redis。":::
   
1. 在“新建 Redis 缓存”页上配置新缓存的设置。
   
   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是介于 1 到 63 个字符之间的字符串。 该字符串只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 | 
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **位置** | 单击下拉箭头并选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
   | **定价层** | 单击下拉箭头并选择一个[定价层](https://azure.microsoft.com/pricing/details/cache/)。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |

1. 选择“网络”选项卡，或选择“网络”按钮（位于页面底部） 。

1. 在“网络”选项卡中，选择“专用终结点”作为连接方法 。

1. 选择“添加”按钮以创建专用终结点。

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="在网络中，添加一个专用终结点。":::

1. 在“创建专用终结点”页面上，使用上一部分中创建的虚拟网络和子网配置专用终结点的设置，然后选择“确定” 。 

1. 选择“下一步:高级”选项卡，或者选择页面底部的“下一步:高级”按钮 。

1. 在基本或标准缓存实例的“高级”选项卡中，如果想要启用非 TLS 端口，请选择启用开关。

1. 在高级缓存实例的“高级”选项卡中，配置非 TLS 端口、群集和数据持久性的设置。

1. 选择“下一步: 标记”选项卡，或选择页面底部的“下一步: 标记”按钮 。

1. 或者，在“标记”选项卡中，如果希望对资源分类，请输入名称或值。 

1. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”选项卡，Azure 将在此处验证配置。

1. 显示绿色的“已通过验证”消息后，选择“创建”。

创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。  
    
> [!IMPORTANT]
> 
> 有一个 `publicNetworkAccess` 标志，默认情况下为 `Disabled`。 
> 如果缓存设置为 `Enabled`，此标志旨在允许你选择性地同时允许公共和专用终结点访问缓存。 如果设置为 `Disabled`，它将只允许专用终结点访问。 可将该值设置为 `Disabled` 或 `Enabled`。 有关如何更改该值的详细信息，请查看[常见问题解答](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)
>
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>在现有 Azure Cache for Redis 实例中创建专用终结点 

本部分将介绍如何向现有的 Azure Cache for Redis 实例添加专用终结点。 

### <a name="create-a-virtual-network"></a>创建虚拟网络

若要创建虚拟网络，请执行以下步骤。

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“创建资源”。

2. 在“新建”页面上，选择“网络”，然后选择“虚拟网络”  。

3. 选择“添加”，创建虚拟网络。

4. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此虚拟网络的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建虚拟网络和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **Name** | 输入虚拟网络名称。 | 该名称必须满足以下要求：以字母或数字开头；以字母、数字或下划线结尾；只包含字母、数字、下划线、句点或连字符。 | 
   | **区域** | 下拉并选择一个区域。 | 选择将使用虚拟网络的其他服务附近的[区域](https://azure.microsoft.com/regions/)。 |

5. 选择“IP 地址”选项卡  ，或选择页面底部的“下一步:  IP 地址”按钮。

6. 在“IP 地址”选项卡中，将“IPv4 地址空间”指定为采用 CIDR 表示法的一个或多个地址前缀（例如 192.168.1.0/24） 。

7. 在“子网名称”下，选择“默认”以编辑子网的属性 。

8. 在“编辑子网”窗格中，指定“子网名称”和“子网地址范围”  。 应以 CIDR 表示法表示子网的地址范围（例如 192.168.1.0/24）。 它必须包含在虚拟网络的地址空间中。

9. 选择“保存”。

10. 选择“查看 + 创建”  选项卡，或选择“查看 + 创建”  按钮。

11. 确认所有信息是否正确，然后选择“创建”以预配虚拟网络。

### <a name="create-a-private-endpoint"></a>创建专用终结点 

若要创建专用终结点，请执行以下步骤。

1. 在 Azure 门户中，搜索“Azure Cache for Redis”。 然后，按 Enter 或者在搜索建议中选择该服务。

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="搜索 Azure Cache for Redis。":::

2. 选择要向其中添加专用终结点的缓存实例。

3. 在屏幕左侧选择“专用终结点”。

4. 选择“专用终结点”按钮以创建专用终结点。

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="添加专用终结点":::

5. 在“创建专用终结点”页面上，配置专用终结点的设置。

   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此专用终结点的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建专用终结点和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **Name** | 输入专用终结点名称。 | 该名称必须满足以下要求：以字母或数字开头；以字母、数字或下划线结尾；只能包含字母、数字、下划线、句点或连字符。 | 
   | **区域** | 下拉并选择一个区域。 | 选择将使用该专用终结点的其他服务附近的[区域](https://azure.microsoft.com/regions/)。 |

6. 选择页面底部的“下一步: 资源”按钮。

7. 在“资源”选项卡中，选择你的订阅，选择资源类型为 `Microsoft.Cache/Redis`，然后选择要将专用终结点连接到的缓存。

8. 选择页面底部的“下一步: 配置”按钮。

9. 在“配置”选项卡中，选择你在上一部分中创建的虚拟网络和子网。

10. 选择页面底部的“下一步:  标记”按钮。

11. 或者，在“标记”选项卡中，如果希望对资源分类，请输入名称或值。

12. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”选项卡，Azure 将在此处验证配置。

13. 显示绿色的“已通过验证”消息后，选择“创建” 。

> [!IMPORTANT]
> 
> 有一个 `publicNetworkAccess` 标志，默认情况下为 `Disabled`。 
> 如果缓存设置为 `Enabled`，此标志旨在允许你选择性地同时允许公共和专用终结点访问缓存。 如果设置为 `Disabled`，它将只允许专用终结点访问。 可将该值设置为 `Disabled` 或 `Enabled`。 有关如何更改该值的详细信息，请查看[常见问题解答](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)
>
>


## <a name="faq"></a>常见问题解答

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>为什么无法连接到专用终结点？

如果缓存已是 VNet 注入缓存，则专用终结点不能与缓存实例一起使用。 如果缓存实例使用的是不受支持的功能（如下所列），则你无法连接到专用终结点实例。

### <a name="what-features-arent-supported-with-private-endpoints"></a>专用终结点不支持哪些功能？

目前不支持门户控制台和防火墙存储帐户持久性。 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>如何将专用终结点更改为禁用或启用公用网络访问？

有一个默认处于 `Disabled` 状态的 `publicNetworkAccess` 标志。 此标志意味着，你可以选择性地允许公共和专用终结点访问缓存（如果它设置为 `Enabled`）。 如果设置为 `Disabled`，它将只允许专用终结点访问。 可在 Azure 门户中或使用 Restful API PATCH 请求将该值设置为 `Disabled` 或 `Enabled`。 

若要在 Azure 门户中更改此值，请执行以下步骤。

1. 在 Azure 门户中，搜索“Azure Cache for Redis”。  然后，按 Enter 或者在搜索建议中选择该服务。

2. 选择要更改其公用网络访问值的缓存实例。

3. 在屏幕左侧选择“专用终结点”。

4. 选择“启用公用网络访问”按钮。

若要通过 Restful API PATCH 请求更改该值，请查看下列内容，并编辑该值以反映你要对缓存使用的标志。

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-link-cache"></a>如何将 VNet 注入缓存迁移到专用链接缓存？

请参阅我们的[迁移指南](cache-vnet-migration.md)，了解如何将 VNet 注入缓存迁移到专用链接缓存的不同方法。 

### <a name="how-can-i-have-multiple-endpoints-in-different-virtual-networks"></a>如何在不同的虚拟网络中拥有多个终结点？

若要在不同的虚拟网络中拥有多个专用终结点，必须在创建专用终结点之前，将专用 DNS 区域手动配置到多个虚拟网络。 有关详细信息，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)。 

### <a name="what-happens-if-i-delete-all-the-private-endpoints-on-my-cache"></a>如果删除缓存上的所有专用终结点，会发生什么情况？

删除缓存上的专用终结点后，在显式启用公用网络访问或添加另一个专用终结点之前，可能无法访问缓存实例。 可在 Azure 门户上或通过 Restful API PATCH 请求来更改 `publicNetworkAccess` 标志。 有关如何更改该值的详细信息，请查看[常见问题解答](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>是否对专用终结点启用了网络安全组 (NSG)？

否，已对专用终结点禁用了 NSG。 尽管包含专用终结点的子网可以有关联的 NSG，但这些规则不会针对专用终结点处理的流量生效。 必须[禁用网络策略的强制实施](../private-link/disable-private-endpoint-network-policy.md)，才能在子网中部署专用终结点。 NSG 仍会在同一子网中托管的其他工作负荷上强制实施。 任何客户端子网上的路由将使用 /32 前缀，更改默认路由行为需要类似的 UDR。 

对源客户端上的出站流量使用 NSG 规则来控制流量。 部署具有 /32 前缀的单个路由，以替代专用终结点路由。 仍支持出站连接的 NSG 流日志和监视信息，并且可以使用这些信息

### <a name="my-private-endpoint-instance-isnt-in-my-vnet-so-how-is-it-associated-with-my-vnet"></a>我的专用终结点实例不在我的 VNet 中，它如何与我的 VNet 关联？

它只会链接到你的 VNet。 由于它不在你的 VNet 中，因此不需要修改依赖终结点的 NSG 规则。


## <a name="next-steps"></a>后续步骤

* 若要详细了解 Azure 专用链接，请参阅 [Azure 专用链接文档](../private-link/private-link-overview.md)。
* 若要比较缓存实例的各种网络隔离选项，请参阅 [Azure Cache for Redis 网络隔离选项文档](cache-network-isolation.md)。
