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
ms.openlocfilehash: e139954e6550e33edb75d01ab9dbec0bba543ea6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92548040"
---
成功部署“对等互连”资源后，可以按照以下步骤进行查看。

1. 转到“资源组”，然后选择在创建“对等互连”资源时所选的资源组 。 如果资源组过多，请使用“筛选器”框。

    > [!div class="mx-imgBorder"]
    > ![资源组](../media/setup-direct-get-resourcegroup.png)

1. 选择所创建的“对等互连”资源。

    > [!div class="mx-imgBorder"]
    > ![显示所创建的对等互连资源的屏幕截图。](../media/setup-direct-get-open.png)

1. “概述”页显示了概要信息，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源“概述”窗格](../media/setup-exchange-get-overview.png)

1. 在左侧，选择“ASN 信息”以查看在创建 PeerAsn 时提交的信息。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源 ASN 信息](../media/setup-direct-get-asninfo.png)

1. 在左侧，选择“连接”。 在屏幕顶部，可以看到都市内不同设施中的 ASN 和 Microsoft 之间的对等互连连接摘要。 还可通过选择中间窗格中的“连接”，从“概述”页访问连接摘要，如下所示 。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源“连接”](../media/setup-exchange-get-connectionssummary.png)

    * “连接状态”对应于对等互连连接设置的状态。 此字段中显示的状态遵循 [Exchange 对等互连演练](../walkthrough-exchange-all.md)中显示的状态图。
    * “IPv4 会话状态”和“IPv6 会话状态”分别对应于 IPv4 和 IPv6 BGP 会话状态 。  
    * 选择屏幕顶部的某行时，底部的“连接”部分会显示每个连接的详细信息。 选择箭头以展开“配置”、“IPv4 地址”和“IPv6 地址”  。

    > [!div class="mx-imgBorder"]
    > ![突出显示展开节的箭头的屏幕截图。](../media/setup-exchange-get-connectionsipv4.png)
