---
title: Azure DNS 专用区域中的自动注册功能是什么？
description: Azure DNS 专用区域中自动注册功能的概述。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: 0854412a83f941e43e79ebcde2f70ae62dabdf15
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965156"
---
# <a name="what-is-the-auto-registration-feature-in-azure-dns-private-zones"></a>Azure DNS 专用区域中的自动注册功能是什么？

Azure DNS 专用区域自动注册功能管理部署在虚拟网络中的虚拟机的 DNS 记录。 在启用此设置的情况下，将[虚拟网络](./private-dns-virtual-network-links.md)与专用 DNS 区域链接。 系统将为部署在虚拟网络中的每一台虚拟机创建一条 DNS 记录。 

系统将为每一台虚拟机创建一条 A 记录和一条 PTR 记录。 新部署的虚拟机的 DNS 记录也会在链接的专用 DNS 区域中自动创建。 删除虚拟机时，也会从专用 DNS 区域中删除任何关联的 DNS 记录。

若要启用自动注册，请在创建虚拟网络链接时选中“Enable auto registration（启用自动注册）”复选框。

:::image type="content" source="./media/privatedns-concepts/enable-autoregistration.png" alt-text="在添加虚拟网络链接的页面上启用自动注册的屏幕截图。":::

## <a name="restrictions"></a>限制

* 自动注册仅适用于虚拟机。 对于所有其他资源（如内部负载均衡器），可以在链接到虚拟网络的专用 DNS 区域中手动创建 DNS 记录。
* 仅为主虚拟机 NIC 自动创建 DNS 记录。 如果虚拟机有多个 NIC，则可以手动为其他网络接口创建 DNS 记录。
* 只有在主虚拟机 NIC 使用 DHCP 的情况下，才自动创建 DNS 记录。 如果使用静态 IP，此类配置[在 Azure 中具有多个 IP 地址](../virtual-network/virtual-network-multiple-ip-addresses-portal.md#os-config)，则自动注册不会为该虚拟机创建记录。
* 不支持 IPv6 自动注册（AAAA 记录）。
* 专用 DNS 区域的自动注册仅限于单个 VNET。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md) 在 Azure DNS 中创建专用区域。

* 了解可以通过 Azure DNS 中的专用区域实现的一些常见[专用区域方案](./private-dns-scenarios.md)。

* 有关 Azure DNS 专用区域的一些常见问题和解答，包括对于某些类型的操作可以期待的特定行为，请参阅[专用 DNS 常见问题解答](./dns-faq-private.yml)。
