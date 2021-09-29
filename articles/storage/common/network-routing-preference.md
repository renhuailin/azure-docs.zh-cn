---
title: 网络路由首选项
titleSuffix: Azure Storage
description: 通过网络路由首选项可以指定网络流量如何通过 Internet 从客户端路由到你的帐户。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: f3a665df26e504eee1c9bea77f8bf6bf5631193f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591019"
---
# <a name="network-routing-preference-for-azure-storage"></a>Azure 存储的网络路由首选项

可以为 Azure 存储帐户配置网络[路由首选项](../../virtual-network/routing-preference-overview.md)，以指定网络流量如何通过 Internet 从客户端路由到你的帐户。 默认情况下，来自 Internet 的流量通过 [Microsoft 全球网络](../../networking/microsoft-global-network.md)路由到存储帐户的公共终结点。 Azure 存储提供额外的选项，用于配置将流量路由到存储帐户的方式。

配置路由首选项使你可以灵活地优化流量，既可以优化网络性能，又可以降低成本。 配置路由首选项时，请指定默认情况下流量如何定向到存储帐户的公共终结点。 还可以为存储帐户发布特定于路由的终结点。

> [!NOTE]
> 在配置来使用高级性能层或区域冗余存储 (ZRS) 的存储帐户中，不支持此功能。

## <a name="microsoft-global-network-versus-internet-routing"></a>Microsoft 全球网络与 Internet 路由

默认情况下，Azure 环境外部的客户端通过 Microsoft 全球网络访问存储帐户。 Microsoft 全球网络针对低延迟路径选择进行了优化，以提供高可靠性的优质网络性能。 入站和出站流量均通过最接近客户端的接入点 (POP) 进行路由。 此默认路由配置可确保进出存储帐户的流量将通过 Microsoft 全球网络遍历其大部分路径，从而最大限度地提高网络性能。

你可以更改存储帐户的路由配置，以便入站和出站流量都可以通过离存储帐户最近的 POP 路由到 Azure 环境之外的客户端，或从中向外路由。 此路由将通过 Microsoft 全球网络的流量遍历降到最低，从而尽早将流量交付给传输 ISP。 利用此路由配置可降低网络成本。

下图显示了每个路由首选项的客户端和存储帐户之间流量的传输方式：

![Azure 存储的路由选项概述](media/network-routing-preference/routing-options-diagram.png)

有关 Azure 中的路由首选项的详细信息，请参阅[什么是路由首选项？](../../virtual-network/routing-preference-overview.md)。

## <a name="routing-configuration"></a>路由配置

有关如何配置路由首选项和特定于路由的终结点的分步指南，请参阅[配置 Azure 存储的网络路由首选项](configure-network-routing-preference.md)。

可以在 Microsoft 全球网络和 Internet 路由之间进行选择，作为存储帐户公共终结点的默认路由首选项。 默认路由首选项适用于来自 Azure 外部的客户端的所有流量，并影响 Azure Data Lake Storage Gen2、Blob 存储、Azure 文件存储和静态网站的终结点。 Azure 队列或 Azure 表不支持配置路由首选项。

还可以为存储帐户发布特定于路由的终结点。 当你发布特定于路由的终结点时，Azure 存储会为你的存储帐户创建新的公共终结点，以便将流量路由到所需的路径。 这种灵活性使你可以通过特定路由将流量定向到存储帐户，而无需更改默认路由首选项。

例如，针对“StorageAccountA”发布 Internet 路由特定的终结点，将为存储帐户发布以下终结点：

| 存储服务        | 特定于路由的终结点                                  |
| :--------------------- | :------------------------------------------------------- |
| Blob 服务           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| 文件服务           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| 静态网站        | `StorageAccountA-internetrouting.web.core.windows.net`   |

如果你具有读取访问权限异地冗余存储 (RA-GRS) 或读取访问权限地理区域冗余存储 (RA-GZRS) 存储帐户，则发布特定于路由的终结点也会自动在次要区域中创建相应的终结点，以进行读取访问。

| 存储服务        | 特定于路由的只读辅助终结点                        |
| :--------------------- | :----------------------------------------------------------------- |
| Blob 服务           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| 文件服务           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| 静态网站        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

可以通过 [Azure 门户](https://portal.azure.com)复制已发布的特定于路由的终结点的连接字符串。 这些连接字符串可用于使用所有现有 Azure 存储 SDK 和 API 进行共享密钥授权。

## <a name="regional-availability"></a>区域可用性

Azure 存储的路由首选项在以下区域提供：

- 美国中部 
- 美国中部 EUAP
- 美国东部 
- 美国东部 2
- 美国东部 2 
- 美国东部 2 EUAP
- 美国中南部
- 美国中西部
- 美国西部 
- 美国西部 2 
- 法国中部 
- 法国南部 
- 德国北部 
- 德国中西部 
- 美国中北部
- 北欧 
- 挪威东部 
- 瑞士北部
- 瑞士西部
- 英国南部 
- 英国西部 
- 西欧 
- 阿联酋中部
- 东亚 
- 东南亚 
- 日本东部 
- 日本西部 
- 印度西部
- 澳大利亚东部 
- 澳大利亚东南部 

以下已知问题会影响 Azure 存储的路由首选项：

- Microsoft 全球网络特定于路由的终结点的访问请求失败，出现 HTTP 错误 404 或类似错误。 如果将 Microsoft 全球网络设置为公共终结点的默认路由首选项，则通过 Microsoft 全球网络路由会按预期工作。

## <a name="pricing-and-billing"></a>定价和计费

有关定价和计费的详细信息，请参阅[什么是路由首选项？](../../virtual-network/routing-preference-overview.md#pricing)中的“定价”部分。

## <a name="next-steps"></a>后续步骤

- [什么是路由首选项？](../../virtual-network/routing-preference-overview.md)
- [配置网络路由首选项](configure-network-routing-preference.md)
- [配置 Azure 存储防火墙和虚拟网络](storage-network-security.md)
- [有关 Blob 存储的安全性建议](../blobs/security-recommendations.md)
