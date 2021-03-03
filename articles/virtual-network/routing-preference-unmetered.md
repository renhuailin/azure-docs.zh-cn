---
title: Azure 中的路由首选项不按顺序进行
description: 了解如何为资源传出数据到 CDN 提供程序配置路由首选项。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about enabling routing preference for my CDN origin resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 1fcc918e5b4b54a415fed0f38d210aeeaa62c008
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679165"
---
# <a name="what-is-routing-preference-unmetered"></a>什么是路由首选项不按顺序？

 (CDN) 提供商的客户在 Azure 中托管其源内容的内容交付网络，则可以使用路由首选项。 此服务允许 CDN 提供程序在不同位置与 Microsoft 全局网络边缘路由器建立直接对等连接。

![路由首选项不按顺序](media/routing-preference-unmetered/unmetered.png)

从 Azure 发往 CDN 提供商的网络流量传出来源直接连接。
* 通过这些直接链接路由的来自 Azure 资源的流量传出的数据传输帐单是免费的。
* 在 Azure 中，CDN 提供程序与源之间的直接连接提供最佳性能，因为之间没有跃点。 这会带来经常从源中提取数据的 CDN 工作负载。

## <a name="configuring-routing-preference-unmetered"></a>配置路由首选项不按顺序

若要利用路由首选项不按顺序进行的，CDN 提供程序需要成为此程序的一部分。 如果 CDN 提供程序不是程序的一部分，请与 CDN 提供商联系。

接下来，为资源配置路由首选项，并将路由首选项类型设置为 **Internet**。 你可以在创建公共 IP 地址时配置路由首选项，然后将公共 IP 与资源（例如虚拟机、面向 internet 的负载均衡器等）相关联。 [了解如何使用 Azure 门户配置公共 IP 地址的路由首选项](routing-preference-portal.md)

你还可以为存储帐户启用路由首选项，并发布第二个终结点，CDN 提供程序需要使用该终结点从存储源提取数据。 例如，发布存储帐户的特定于 internet 路由的终结点时， *StorageAccountA* 将发布存储服务的第二个终结点，如下所示：

![存储帐户的路由首选项](media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>后续步骤

* [使用 Azure PowerShell 为 VM 配置路由首选项](configure-routing-preference-virtual-machine-powershell.md)
* [使用 Azure CLI 为 VM 配置路由首选项](configure-routing-preference-virtual-machine-cli.md)
* [为存储帐户配置路由首选项](/azure/storage/common/network-routing-preference)