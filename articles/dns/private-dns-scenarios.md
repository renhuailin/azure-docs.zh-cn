---
title: 专用区域的场景 - Azure DNS
description: 本文介绍使用 Azure DNS 专用区域的常见场景。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/27/2021
ms.author: rohink
ms.openlocfilehash: 5a2572af0fd312efeacb8544b653db1b35809244
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108127814"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS 专用区域场景

Azure DNS 专用区域在虚拟网络内或虚拟网络之间提供名称解析功能。 在本文中，我们将查看可以从使用此功能受益的一些常见场景。

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>场景：在单个虚拟网络范围内的名称解析

在这一场景中，你在 Azure 中有一个包含大量资源的虚拟网络，包括虚拟机。 你的需求是使用特定域名（DNS 区域）解析虚拟网络中的所有资源。 你还需要使命名解析是专用解析，且无法从 Internet 进行访问。 最后，需要 Azure 将 VM 自动注册到 DNS 区域。

下面演示了上述场景。 虚拟网络名为“A”，其中包含两个 VM（VNETA-VM1 和 VNETA-VM2）。 每个 VM 都有一个关联的专用 IP。 创建专用区域后（例如 `contoso.com`），将虚拟网络“A”作为注册虚拟网络进行链接。 Azure DNS 将自动在引用两个 VM 的区域中创建两个 A 记录。 现在，从 VNETA-VM1 发出的 DNS 查询现在可以解析 `VNETA-VM2.contoso.com` 并且将收到包含 VNETA-VM2 的专用 IP 地址的 DNS 响应。
还可以从 VNETA-VM2 对 VNETA-VM1 的专用 IP (10.0.0.1) 进行反向 DNS 查询 (PTR)。 DNS 响应会按预期包含名称 VNETA-VM1。 

![单虚拟网络解析](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>方案：跨虚拟网络的名称解析

在此场景中，需要将一个专用区域与多个虚拟网络相关联。 可以在不同的网络体系结构（例如中心辐射型模型）中实现此解决方案。 此配置表示何时使用中心虚拟网络将多个辐射虚拟网络连接在一起。 中央的中心虚拟网络可以作为注册虚拟网络进行链接，辐射虚拟网络可以作为解析虚拟网络进行链接。 

下图显示了此场景的一个简单版本，其中只有两个虚拟网络（A 和 B）。A 定义为注册虚拟网络，而 B 定义为解析虚拟网络。 目的是使两个虚拟网络共享公用区域 `contoso.com`。 创建区域后，定义为注册的虚拟网络将自动为虚拟网络中的 VM（VNETA-VM1 和 VNETA-VM2）注册 DNS 记录。 还可以为解析虚拟网络 B 中的 VM 手动将 DNS 记录添加到该区域。使用此设置时，对于正向和反向 DNS 查询，你将会看到以下行为：
* 从“解析”虚拟网络 B 中的 VNETB-VM1 发出的针对 VNETA-VM1.contoso.com 的 DNS 查询将收到包含 VNETA-VM1 的专用 IP 的 DNS 响应。
* 从“解析”虚拟网络 B 中的 VNETB-VM2 发出的针对 10.1.0.1 的反向 DNS (PTR) 查询将收到包含 FQDN VNETB-VM1.contoso.com 的 DNS 响应。  
* 从“解析”虚拟网络 B 中的 VNETB-VM3 发出的针对 10.0.0.1 的反向 DNS (PTR) 查询将收到 NXDOMAIN。 原因是反向 DNS 查询的范围仅限于同一虚拟网络。 

![多虚拟网络解析](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>方案：水平分割功能

在此场景中，需要不同的名称解析，具体取决于客户端在同一 DNS 区域中的位置。 你的应用程序可能有专用和公共版本，各自具有不同功能或行为。 你需要为两个版本使用同一域名。 对于此场景，可以通过在 Azure DNS 中创建同名的公用和专用区域来完成。 

下图演示了此方案。 虚拟网络名为“A”，其中包含两个 VM（VNETA-VM1 和 VNETA-VM2）。 两个 VM 都配置了专用 IP 和公共 IP。 创建了名为 `contoso.com` 的公共 DNS 区域，并将这些 VM 的公共 IP 注册为该区域中的 DNS 记录。 还创建了名为 `contoso.com` 的专用 DNS 区域。 已将虚拟网络 A 定义为注册虚拟网络。 然后，Azure 会自动将 VM 作为 A 记录注册到专用区域中并指向其专用 IP。

现在，当 Internet 客户端对 `VNETA-VM1.contoso.com` 执行 DNS 查询时，Azure 将返回来自公用区域的公共 IP 记录。 如果同一虚拟网络 A 中的另一 VM（例如 VNETA-VM2）发出同一 DNS 查询，则 Azure 将返回来自专用区域的专用 IP 记录。 

![裂脑解析](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>后续步骤
若要详细了解专用 DNS 区域，请参阅[将 Azure DNS 用于专用域](private-dns-overview.md)。

了解如何在 Azure DNS 中[创建专用 DNS 区域](./private-dns-getstarted-powershell.md)。

若要了解 DNS 区域和记录，请访问 [DNS 区域和记录概述](dns-zones-records.md)。

了解 Azure 的一些其他关键[网络功能](../networking/fundamentals/networking-overview.md)。
