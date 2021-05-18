---
title: Internet 对等互连 - 常见问题解答
titleSuffix: Azure
description: Internet 对等互连 - 常见问题解答
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "75775493"
---
# <a name="internet-peering---faqs"></a>Internet 对等互连 - 常见问题解答

对于一般问题，可以查看以下信息。

**Internet 对等互连服务与对等互连服务有何区别？**

对等互连服务旨在为 Microsoft 企业客户提供与 Microsoft 的企业级公共 IP 连接。 企业级 Internet 包括通过 ISP 的连接，ISP 具有与 Microsoft 的高吞吐量连接和用于实现 HA 连接的冗余。 此外，还优化了用户流量来应对流向最靠近的 Microsoft Edge 时的延迟。 对等互连服务建立在与合作伙伴运营商的对等互连连接的基础之上。 与合作伙伴的对等互连连接必须是直接对等互连，不能是交换对等互。 直接对等互连必须具有本地和异地冗余。

**什么是旧式对等互连？**

使用 Azure PowerShell 设置的对等互连连接是作为 Azure 资源进行管理的。 过去设置的对等互连连接作为旧式对等互连存储在系统中，你可选择将其转换为 Azure 资源进行管理。

**调用 New-AzPeeringDirectConnectionObject 时，向 Microsoft 和对等设备提供哪些 IP 地址？**

在调用 New-AzPeeringDirectConnectionObject cmdlet 时，输入 /31 地址 (a.b.c.d/31) 或 /30 地址 (a.b.c.d/30)。 第一个 IP 地址 (a.b.c.d+0) 提供给对等设备，第二个 IP 地址 (a.b.c.d+1) 提供给 Microsoft 设备。

**New-AzPeeringDirectConnectionObject cmdlet 中的 MaxPrefixesAdvertisedIPv4 和 MaxPrefixesAdvertisedIPv6 参数表示什么？**

MaxPrefixesAdvertisedIPv4 和 MaxPrefixesAdvertisedIPv6 参数表示对等方希望 Microsoft 接受的 IPv4 和 IPv6 前缀的最大数目。 可随时修改这些参数。