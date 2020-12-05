---
title: 使用 Azure 专用链接的 Azure Cache for Redis（预览版）
description: Azure 专用终结点是一个网络接口，可以通过私密且安全的方式将你连接到 Azure 专用链接支持的 Azure Cache for Redis。 在本文中，你将了解如何使用 Azure 门户创建 Azure Cache、Azure 虚拟网络和专用终结点。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 31ae4605b6cc9e26c89beea692fe61fcbda49c4c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621495"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>使用 Azure 专用链接的 Azure Cache for Redis（公共预览版）
在本文中，你将了解如何通过 Azure 门户创建虚拟网络、Azure Cache for Redis 实例和专用终结点。 你还将学习如何向现有的 Azure Cache for Redis 实例添加专用终结点。

Azure 专用终结点是一个网络接口，可以通过私密且安全的方式将你连接到 Azure 专用链接支持的 Azure Cache for Redis。 

## <a name="prerequisites"></a>先决条件
* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> 若要使用专用终结点，需要在 2020 年 7 月 28 日之后创建 Azure Cache for Redis 实例。
> 目前，不支持异地复制、防火墙规则、门户控制台支持、每个群集缓存多个终结点、防火墙的持久性连接和 VNet 注入的缓存。 
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

   | 设置      | 建议的值  | 描述 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此虚拟网络的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建虚拟网络和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **Name** | 输入虚拟网络名称。 | 名称必须以字母或数字开头，以字母、数字或下划线结尾，并且只能包含字母、数字、下划线、句点或连字符。 | 
   | **区域** | 下拉并选择一个区域。 | 选择将使用虚拟网络的其他服务附近的[区域](https://azure.microsoft.com/regions/)。 |

5. 选择“IP 地址”选项卡，或单击页面底部的“下一页: IP 地址”按钮。

6. 在“IP 地址”选项卡中，将“IPv4 地址空间”指定为 CIDR 表示法中的一个或多个地址前缀（例如 192.168.1.0/24） 。

7. 在“子网名称”下，单击“默认”以编辑子网的属性 。

8. 在“编辑子网”窗格中，指定“子网名称”以及“子网地址范围”  。 应以 CIDR 表示法表示子网的地址范围（例如 192.168.1.0/24）。 它必须包含在虚拟网络的地址空间中。

9. 选择“保存”。

10. 选择“查看 + 创建”选项卡，或单击“查看 + 创建”按钮 。

11. 验证所有信息是否正确，然后单击“创建”以配置虚拟网络。

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>创建 Azure Cache for Redis 实例和专用终结点
若要创建缓存实例，请执行以下步骤。

1. 返回到 Azure 门户主页或打开边栏菜单，然后选择“创建资源”。 
   
1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="选择 Azure Cache for Redis。":::
   
1. 在“新建 Redis 缓存”页上配置新缓存的设置。
   
   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 | 
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **位置** | 单击下拉箭头并选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
   | **定价层** | 单击下拉箭头并选择一个[定价层](https://azure.microsoft.com/pricing/details/cache/)。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |

1. 选择“网络”选项卡，或单击页面底部的“网络”按钮 。

1. 在“网络”选项卡中，选择“专用终结点”作为连接方法 。

1. 单击“添加”按钮，创建专用终结点。

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="在网络中，添加一个专用终结点。":::

1. 在“创建专用终结点”页面上，使用上一部分中创建的虚拟网络和子网配置专用终结点的设置，然后选择“确定” 。 

1. 选择页面底部的“下一步:高级”选项卡，或者单击页面底部的“下一步:高级”按钮。

1. 在基本或标准缓存实例的“高级”选项卡中，如果想要启用非 TLS 端口，请选择启用开关。

1. 在高级缓存实例的“高级”选项卡中，配置非 TLS 端口、群集和数据持久性的设置。

1. 选择页面底部的“下一步:标记”选项卡，或者单击“下一步:标记”按钮。

1. 或者，在“标记”选项卡中，如果希望对资源分类，请输入名称或值。 

1. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”选项卡，Azure 将在此处验证配置。

1. 显示绿色的“已通过验证”消息后，选择“创建”。

创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。  
    
> [!IMPORTANT]
> 
> 有一个 `publicNetworkAccess` 标志，默认情况下为 `Disabled`。 
> 如果缓存设置为 `Enabled`，此标志旨在允许你选择性地同时允许公共和专用终结点访问缓存。 如果设置为 `Disabled`，它将只允许专用终结点访问。 你可以将值设置为 `Disabled` 或 `Enabled` ，并在下面提供修补程序请求。 编辑值，以反映要缓存的标志。
> ```http
> PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
> {    "properties": {
>        "publicNetworkAccess":"Disabled"
>    }
> }
> ```
>

> [!IMPORTANT]
> 
> 若要连接到群集缓存，需要将 `publicNetworkAccess` 设置为 `Disabled` 并且只能有一个专用终结点连接。 
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>在现有 Azure Cache for Redis 实例中创建专用终结点 

本部分将介绍如何向现有的 Azure Cache for Redis 实例添加专用终结点。 

### <a name="create-a-virtual-network"></a>创建虚拟网络 
若要创建虚拟网络，请执行以下步骤。

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“创建资源”。

2. 在“新建”页面上，选择“网络”，然后选择“虚拟网络”  。

3. 选择“添加”，创建虚拟网络。

4. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

   | 设置      | 建议的值  | 描述 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此虚拟网络的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建虚拟网络和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **Name** | 输入虚拟网络名称。 | 名称必须以字母或数字开头，以字母、数字或下划线结尾，并且只能包含字母、数字、下划线、句点或连字符。 | 
   | **区域** | 下拉并选择一个区域。 | 选择将使用虚拟网络的其他服务附近的[区域](https://azure.microsoft.com/regions/)。 |

5. 选择“IP 地址”选项卡，或单击页面底部的“下一页: IP 地址”按钮。

6. 在“IP 地址”选项卡中，将“IPv4 地址空间”指定为 CIDR 表示法中的一个或多个地址前缀（例如 192.168.1.0/24） 。

7. 在“子网名称”下，单击“默认”以编辑子网的属性 。

8. 在“编辑子网”窗格中，指定“子网名称”以及“子网地址范围”  。 应以 CIDR 表示法表示子网的地址范围（例如 192.168.1.0/24）。 它必须包含在虚拟网络的地址空间中。

9. 选择“保存”。

10. 选择“查看 + 创建”选项卡，或单击“查看 + 创建”按钮 。

11. 验证所有信息是否正确，然后单击“创建”以配置虚拟网络。

### <a name="create-a-private-endpoint"></a>创建专用终结点 

若要创建专用终结点，请执行以下步骤。

1. 在 Azure 门户中，搜索“Azure Cache for Redis”并按 Enter 或从搜索建议中选择它。

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="搜索 Azure Cache for Redis。":::

2. 选择要向其中添加专用终结点的缓存实例。

3. 在屏幕左侧选择“(预览版)专用终结点”。

4. 单击“专用终结点”按钮，创建专用终结点。

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="添加专用终结点":::

5. 在“创建专用终结点”页面上，配置专用终结点的设置。

   | 设置      | 建议的值  | 描述 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此专用终结点的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建专用终结点和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **Name** | 输入专用终结点名称。 | 名称必须以字母或数字开头，以字母、数字或下划线结尾，并且只能包含字母、数字、下划线、句点或连字符。 | 
   | **区域** | 下拉并选择一个区域。 | 选择将使用该专用终结点的其他服务附近的[区域](https://azure.microsoft.com/regions/)。 |

6. 单击“下一步:资源”按钮。

7. 在“资源”选项卡中，选择你的订阅，选择资源类型为 `Microsoft.Cache/Redis`，然后选择要将专用终结点连接到的缓存。

8. 单击“下一步:**配置”按钮，该按钮位于页面底部**。

9. 在“配置”选项卡中，选择你在上一部分中创建的虚拟网络和子网。

10. 单击“下一步:标记”按钮。

11. 或者，在“标记”选项卡中，如果希望对资源分类，请输入名称或值。

12. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”选项卡，Azure 将在此处验证配置。

13. 显示绿色的“已通过验证”消息后，选择“创建” 。

## <a name="faq"></a>常见问题解答

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>为什么无法连接到专用终结点？
如果缓存已是 VNet 注入缓存，则专用终结点不能与缓存实例一起使用。 如果缓存实例使用的是不受支持的功能（如下所列），则无法连接到专用终结点实例。 此外，需要在 7 月 27 日之后创建缓存实例才能使用专用终结点。

### <a name="what-features-are-not-supported-with-private-endpoints"></a>专用终结点不支持哪些功能？
异地复制、防火墙规则、门户控制台支持、每个群集缓存多个终结点、防火墙规则的持久性和区域冗余。 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>如何从公共网络访问中禁用或启用要禁用或启用的专用终结点？
有一个 `publicNetworkAccess` 标志，默认情况下为 `Disabled`。 如果缓存设置为 `Enabled`，此标志旨在允许你选择性地同时允许公共和专用终结点访问缓存。 如果设置为 `Disabled`，它将只允许专用终结点访问。 你可以将值设置为 `Disabled` 或 `Enabled` ，并在下面提供修补程序请求。 编辑值，以反映要缓存的标志。

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>是否对专用终结点启用了网络安全组 (NSG)？
否，已对专用终结点禁用了 NSG。 但是，如果子网上还有其他资源，则 NSG 强制将应用于这些资源。

### <a name="how-can-i-connect-to-a-clustered-cache"></a>如何连接到群集缓存？
需要将 `publicNetworkAccess` 设置为 `Disabled`，并且只能有一个专用终结点连接。

### <a name="since-my-private-endpoint-instance-is-not-in-my-vnet-how-is-it-associated-with-my-vnet"></a>由于我的专用终结点实例不在我的 VNet 中，它如何与我的 VNet 关联？
它将仅链接到 VNet。 由于它不在 VNet 中，因此不需要为依赖终结点修改 NSG 规则。

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-endpoint-cache"></a>如何将 VNet 注入缓存迁移到专用终结点缓存？
需要删除 VNet 注入缓存，并使用专用终结点创建新的缓存实例。

## <a name="next-steps"></a>后续步骤

* 若要详细了解 Azure 专用链接，请参阅 [Azure 专用链接文档](../private-link/private-link-overview.md)。
* 若要比较缓存实例的各种网络隔离选项，请参阅 [Azure Cache for Redis 网络隔离选项文档](cache-network-isolation.md)。