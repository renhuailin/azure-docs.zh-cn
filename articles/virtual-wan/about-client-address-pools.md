---
title: 关于 P2S 用户 VPN 的客户端地址池
titleSuffix: Azure Virtual WAN
description: 了解用户 VPN P2S 的客户端地址池。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: cherylmc
ms.openlocfilehash: 58b642eeff7b20ccb04e2b5817f8f5a8ee5bcc2c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778714"
---
# <a name="about-client-address-pools-for-point-to-site-configurations"></a>关于点到站点配置的客户端地址池

本文介绍了在虚拟中心的点到站点网关缩放单元数为 40 或更高时分配客户端地址空间的虚拟 WAN 准则和要求。

虚拟 WAN 中心的点到站点 VPN 网关是使用多个实例部署的。 点到站点 VPN 网关的每个实例最多可支持 10,000 个并发的点到站点用户连接。 因此，如果缩放单位大于 40，虚拟 WAN 需要部署额外的容量，这需要为不同的缩放单位分配最少数量的地址池。

例如，如果选择的缩放单元为 100，则为虚拟中心的点到站点 VPN 网关部署 5 个实例。 此部署可支持 50,000 个并发连接和至少 5 个不同的地址池。

可用缩放单元

| 缩放单元 | 支持的最大客户端数 | 最小地址池数 |
|--- |--- |--- |
| 40 | 20000 | 2 |
| 60 | 30000 | 3 |
| 80 | 40000 | 4 |
| 100 | 50000 | 5 |
| 120 | 60000 | 6 |
| 140 | 70000 | 7 |
| 160 | 80000 | 8 |
| 180 | 90000 | 9 |
| 200 | 100000 | 10 |

## <a name="specifying-address-pools"></a><a name="specify-address-pools"></a>指定地址池

点到站点 VPN 地址池分配由虚拟 WAN 自动完成。 请参阅以下有关如何指定地址池的基本准则。

* 一个网关实例最多允许 10,000 个并发连接。 因此，每个地址池应至少包含 10,000 个唯一 RFC1918 IP 地址。
* 多个地址池范围会自动组合并分配给单个网关实例。 对于 IP 地址少于 10,000 个的任何网关实例，此过程以循环方式完成。 例如，一个具有 5,000 个地址的池可通过虚拟 WAN 与另一个具有 8,000 个地址且分配给单个网关实例的池自动合并。
* 单个地址池仅通过虚拟 WAN 分配给单个网关实例。
* 地址池必须是唯一的。 地址池之间不能有重叠。

> [!NOTE] 
> 如果地址池与正在进行维护的网关实例关联，则无法将该地址池重新分配给其他实例。

### <a name="example"></a>示例 

下面的示例描述了这样一种情况：60 个缩放单位最多支持 30,000 个连接，但分配的地址池导致并发连接数少于 30,000 个。

此设置支持的并发连接总数为 28,192。 第一个网关实例支持 10,000 个地址，第二个实例支持 8,192 个连接，第三个实例也支持 10,000 个地址。

| 地址池编号 | 地址池 | 支持的连接数 |
|--- |--- |--- |
| 1 | 10.12.0.0/15 | 10000 |
| 2 | 10.13.0.0/19 | 8192 |
| 3 | 10.14.0.0/15 | 10000|

#### <a name="recommendations"></a>建议

* 请确保地址池 2 至少有 10,000 个不同的 IP 地址。 （示例：10.13.0.0/15）
* 再添加一个地址池。 （示例：地址池 4 10.15.0.0/21，包含 2048 个地址）。 地址池 2 和 4 将自动合并，允许网关实例支持 10,000 个并发连接。

## <a name="configure-or-modify-this-setting"></a>配置或修改此设置

创建虚拟中心时，将在“点到站点”页上配置此设置。 以后可以修改此设置。

若要修改此设置，请执行以下操作：

[!INCLUDE [Modify client address pool](../../includes/virtual-wan-client-address-pool-include.md)]

## <a name="next-steps"></a>后续步骤

有关配置步骤，请参阅[配置 P2S 用户 VPN](virtual-wan-point-to-site-portal.md)。
