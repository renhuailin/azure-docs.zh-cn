---
title: 什么是 Azure DNS 专用区域
description: 专用 DNS 区域概述
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 3a8ec174cb1be02389487c442b4e1b3ff35dbf27
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746500"
---
# <a name="what-is-a-private-azure-dns-zone"></a>什么是专用 Azure DNS 区域

Azure 专用 DNS 提供可靠、安全的 DNS 服务来管理和解析虚拟网络中的域名，无需添加自定义 DNS 解决方案。 借助专用 DNS 区域，可以使用自定义域名而不使用当前可用的由 Azure 提供的名称。 

专用 DNS 区域中包含的记录无法通过 Internet 进行解析。 针对专用 DNS 区域的 DNS 解析只能在链接到它的虚拟网络中进行。

可以通过创建[虚拟网络链接](./private-dns-virtual-network-links.md)，将专用 DNS 区域链接到一个或多个虚拟网络。
还可以启用[自动注册](./private-dns-autoregistration.md)功能，以便为虚拟网络中部署的虚拟机自动管理 DNS 记录的生命周期。

## <a name="limits"></a>限制

若要了解可以在订阅中创建的专用 DNS 区域数量以及专用 DNS 区域中支持的记录集数量，请参阅 [Azure DNS 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="restrictions"></a>限制

* 不支持带单一标签的专用 DNS 区域。 专用 DNS 区域必须有两个或两个以上的标签。 例如，contoso.com 包含两个标签，使用句点分隔。 专用 DNS 区域最多可以有 34 个标签。
* 无法在专用 DNS 区域中创建区域委派（NS 记录）。 如果要使用子域，可以直接将该域创建为专用 DNS 区域。 然后，你可以将其链接到虚拟网络，而不需要从父区域设置名称服务器委派。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md) 在 Azure DNS 中创建专用区域。

* 了解可以通过 Azure DNS 中的专用区域实现的一些常见[专用区域方案](./private-dns-scenarios.md)。

* 有关 Azure DNS 中专用区域的常见问题和解答，请参阅[专用 DNS 常见问题解答](./dns-faq-private.yml)。
