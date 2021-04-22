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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96356238"
---
1. 转到“资源组”，然后选择在创建“对等互连”资源时选择的资源组 。 如果资源组过多，请使用“筛选器”框。

    > [!div class="mx-imgBorder"]
    > ![资源组](../media/setup-direct-get-resourcegroup.png)

1. 选择所创建的“对等互连”资源。

    > [!div class="mx-imgBorder"]
    > ![已选中左窗格中的“概述”页。 其中显示了有关 PeeringResourceGroup 的信息。 在“对等互连”列表中，AshburnPeering 已突出显示。](../media/setup-direct-get-open.png)

1. “概述”页显示了概要信息，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源“概述”窗格](../media/setup-direct-get-overview.png)

1. 在左侧，选择“ASN 信息”以查看在创建 PeerAsn 时提交的信息。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源 ASN 信息](../media/setup-direct-get-asninfo.png)

1. 在左侧，选择“连接”。 在屏幕顶部，可以看到都市内不同设施中的 ASN 和 Microsoft 之间的对等互连连接摘要。 还可通过选择窗格中间的“连接”，从“概述”页访问连接摘要，如下所示 。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源“连接”](../media/setup-direct-get-connectionssummary.png)

    * “连接状态”对应于对等互连连接设置的状态。 此字段中显示的状态遵循[直接对等互连演练](../walkthrough-direct-all.md)中显示的状态图。
    * “IPv4 会话状态”和“IPv6 会话状态”分别对应于 IPv4 和 IPv6 BGP 会话状态 。 
    * 选择屏幕顶部的某行时，底部的“连接”部分会显示每个连接的详细信息。 选择箭头以展开“配置”、“IPv4 地址”和“IPv6 地址”  。
