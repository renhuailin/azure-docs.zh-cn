---
title: Azure 中不按流量计费的路由首选项
description: 了解如何为将数据流出到 CDN 提供商的资源配置路由首选项。
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: 5d459c4a61518aae349184773561448148877afc
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367530"
---
# <a name="what-is-routing-preference-unmetered"></a>什么是不按流量计费的路由首选项？

不按流量计费的路由首选项适用于客户将其源内容托管在 Azure 中的内容分发网络 (CND) 提供商。 此服务允许 CDN 提供商在不同位置与 Microsoft 全局网络边缘路由器建立直接对等互连。

![不按流量计费的路由首选项](./media/routing-preference-unmetered/unmetered.png)

从 Azure 流出到 CDN 提供商的网络流量受益于直接连接。
* 通过这些直接链接路由的 Azure 资源中流出的流量的数据传输帐单是免费的。
* CDN 提供商和 Azure 中的原点之间的直接连接可提供最佳性能，因为之间没有跃点。 这有利于经常从原点提取数据的 CDN 工作负荷。

## <a name="configuring-routing-preference-unmetered"></a>配置不按流量计费的路由首选项

要充分利用不按流量计费的路由首选项，CDN 提供商需要参与此计划。 如果 CDN 提供商未参与此计划，请与 CDN 提供商联系。

接下来，为你的资源配置路由首选项，并将路由首选项类型设置为 Internet。 可以在创建公共 IP 地址的同时配置路由首选项，然后将公共 IP 与资源（例如虚拟机、面向 internet 的负载均衡器等）相关联。 [了解如何使用 Azure 门户配置公共 IP 地址的路由首选项](../../virtual-network/routing-preference-portal.md)

还可以为存储帐户启用路由首选项，并发布第二个终结点，CDN 提供商需要使用该终结点从存储源提取数据。 例如，为存储帐户 StorageAccountA 发布特定于 Internet 路由的终结点时，将发布存储服务的第二个终结点，如下所示：

![存储帐户的路由首选项](./media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>后续步骤

* [使用 Azure PowerShell 为 VM 配置路由首选项](../../virtual-network/configure-routing-preference-virtual-machine-powershell.md)
* [使用 Azure CLI 为 VM 配置路由首选项](../../virtual-network/configure-routing-preference-virtual-machine-cli.md)
* [为存储帐户配置路由首选项](../../storage/common/network-routing-preference.md)