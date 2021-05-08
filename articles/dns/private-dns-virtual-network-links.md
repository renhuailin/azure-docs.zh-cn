---
title: 什么是 Azure DNS 专用区域的虚拟网络链接子资源
description: Azure DNS 专用区域虚拟网络链接子资源概述
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: b4c18403574f6c4772d0c6c50e3efa42a118f054
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108065111"
---
# <a name="what-is-a-virtual-network-link"></a>什么是虚拟网络链接？

在 Azure 中创建专用 DNS 区域后，需要将虚拟网络链接到该区域。 链接后，托管在该虚拟网络中的 VM 便可访问专用 DNS 区域。 每个专用 DNS 区域都有一组虚拟网络链接子资源。 这些资源中的每个资源都代表与虚拟网络的连接。 虚拟网络可以作为注册或解析虚拟网络链接到专用 DNS 区域。

## <a name="registration-virtual-network"></a>注册虚拟网络

在专用 DNS 区域和虚拟网络之间[创建链接](./private-dns-getstarted-portal.md#link-the-virtual-network)。 可以选择启用 [autoregistration](./private-dns-autoregistration.md)。 如果启用此设置，虚拟网络会成为专用 DNS 区域的注册虚拟网络。 系统将为要部署在网络中的虚拟机自动创建 DNS 记录。 系统将为已部署在虚拟网络中的虚拟机创建 DNS 记录。

从虚拟网络的角度来看，专用 DNS 区域成为该虚拟网络的注册区域。 专用 DNS 区域可以有多个注册虚拟网络。 但是，每个虚拟网络只能有一个与其关联的注册区域。

## <a name="resolution-virtual-network"></a>解析虚拟网络

如果你选择将虚拟网络与专用 DNS 区域链接，则不需要启用“autoregistion”。 虚拟网络仅作为解析虚拟网络处理。 系统不会在专用区域中为已部署此虚拟网络的虚拟机自动创建 DNS 记录。 但是，在虚拟网络中部署的虚拟机可以成功从专用区域查询 DNS 记录。 这些记录包括从链接到专用 DNS 区域的其他虚拟网络手动创建和自动注册的记录。

一个专用 DNS 区域可以具有多个解析虚拟网络，且一个虚拟网络可以具有与其关联的多个解析区域。

## <a name="limits"></a>限制

若要了解注册网络和解析网络的数量，可以链接到专用 DNS 区域，请参阅 [Azure DNS 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="other-considerations"></a>其他注意事项

* 不支持使用经典部署模型部署的虚拟网络。

* 仅可在专用 DNS 区域和虚拟网络之间创建一个链接。

* 专用 DNS 区域下的每个虚拟网络链接必须在专用 DNS 区域上下文中具有唯一的名称。 可在不同的专用 DNS 区域中拥有具有相同名称的链接。

* 创建虚拟网络链接后，请检查虚拟网络链接资源的“链接状态”字段。 根据虚拟网络的大小，链接可能需要几分钟才能运行，并且“链接状态”会更改为“已完成”。

* 删除虚拟网络时，所有虚拟网络链接及在其他专用 DNS 区域中自动注册的与之关联的 DNS 记录都将自动删除。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Azure 门户](./private-dns-getstarted-portal.md#link-the-virtual-network)将虚拟网络链接到专用 DNS 区域

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md) 在 Azure DNS 中创建专用区域。

* 了解可以通过 Azure DNS 中的专用区域实现的一些常见[专用区域方案](./private-dns-scenarios.md)。

* 有关 Azure DNS 专用区域的一些常见问题和解答，包括对于某些类型的操作可以期待的特定行为，请参阅[专用 DNS 常见问题解答](./dns-faq-private.md)。
