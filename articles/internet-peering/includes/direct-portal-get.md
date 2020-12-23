---
title: include 文件
titleSuffix: Azure
description: include 文件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3507aacc68de25f7368cbe3cda917077564c56eb
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356238"
---
1. 中转到 " **资源组**"，并选择在创建对 **等互连** 资源时选择的资源组。 如果资源组太多，请使用 " **筛选器** " 框。

    > [!div class="mx-imgBorder"]
    > ![资源组](../media/setup-direct-get-resourcegroup.png)

1. 选择创建的对 **等互连** 资源。

    > [!div class="mx-imgBorder"]
    > ![将在左窗格中选择 "概述" 页。 其中显示了有关 PeeringResourceGroup 的信息。 在对等列表中，将突出显示 AshburnPeering。](../media/setup-direct-get-open.png)

1. **概述** 页显示了高级信息，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源概述窗格](../media/setup-direct-get-overview.png)

1. 在左侧，选择 " **ASN 信息** "，查看在创建 PeerAsn 时提交的信息。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源 ASN 信息](../media/setup-direct-get-asninfo.png)

1. 在左侧，选择 " **连接**"。 在屏幕顶部，可以看到你的 ASN 和 Microsoft 之间的对等互连连接的摘要，以及大都市中不同设施间的连接。 你还可以通过在 " **概述** " 页的中心选择 " **连接** " 来访问 "连接摘要"，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源连接](../media/setup-direct-get-connectionssummary.png)

    * **连接状态** 对应于对等互连连接设置的状态。 此字段中显示的状态遵循 [直接对等演练](../walkthrough-direct-all.md)中显示的状态图。
    * **Ipv4 会话状态** 和 **ipv6 会话状态** 分别对应于 ipv4 和 ipv6 BGP 会话状态。 
    * 选择屏幕顶部的行时，底部的 " **连接** " 部分会显示每个连接的详细信息。 选择箭头以展开 " **配置**"、" **IPv4 地址**" 和 " **IPv6 地址**"。
