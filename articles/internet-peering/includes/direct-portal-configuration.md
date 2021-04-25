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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "81681072"
---
1. 在“创建对等互连”页的“配置”选项卡上，按如下所示填写相应的框 。

    * 对于“对等互连类型”，选择“直接” 。
    * 对于“Microsoft 网络”，选择“AS8075” 。 不要通过 ASN 8069 创建对等互连。 它是为特殊应用程序预留的，仅供 [Microsoft 对等互连](mailto:peering@microsoft.com)使用。
    * 对于“SKU”，选择“基本免费” 。 请勿选择“高级免费”，因为它是为特殊应用程序预留的。
    * 选择要设置对等互连的“地铁”位置。

        > [!NOTE]
        > 如果所选的“地铁”位置已建立 Microsoft 对等互连连接，并且你是第一次使用 Azure 门户在该位置设置对等互连，则现有的对等互连连接将会在“对等互连连接”部分中列出，如下所示 。 Microsoft 会自动将这些对等互连连接转换为 Azure 资源，以便你可以在同一位置管理所有的这些连接和新连接。 有关详细信息，请参阅[使用门户将旧版直接对等互连转换为 Azure 资源](../howto-legacy-direct-portal.md)。
        >

1. 在“对等互连连接”下选择“新建”，为要设置的每个新连接添加一行 。

    * 若要配置或修改连接设置，请选择行的“编辑”按钮。

        > [!div class="mx-imgBorder"]
        > ![“编辑”按钮](../media/setup-direct-conf-tab-edit.png)
    
    * 若要删除行，请选择“…” > “删除” 。

        > [!div class="mx-imgBorder"]
        > ![“删除”按钮](../media/setup-direct-conf-tab-delete.png)

    * 你需要提供连接的所有设置，如下所示。

         > [!div class="mx-imgBorder"]
         > ![“直接对等互连连接”页](../media/setup-direct-conf-tab-connection.png)

        1. 选择需要设置连接的“对等互连设施”。
        1. “会话地址提供商”用于确定由谁提供在网络和 Microsoft 之间设置 BGP 会话所需的子网。 如果可以提供子网，请选择“对等”。 否则，请选择“Microsoft”，[Microsoft 对等互连](mailto:peering@microsoft.com)团队将与你联系。 如果选择此选项，Microsoft 将需要更长的时间来处理对等互连请求。 在某些情况下，Microsoft 可能无法提供子网，这会导致拒绝请求。
        1. 如果“会话地址提供商”选项选择的是“对等”，请在“会话 IPv4 前缀”和“会话 IPv6 前缀”框中分别输入 IPv4 和 IPv6 地址以及前缀掩码   。
        1. 在“最大播发 IPv4 地址数”和“最大播发 IPv6 地址数”框中分别输入将播发的 IPv4 和 IPv6 前缀数目 。
        1. 调整“总带宽”滑块来反映连接的带宽。
        1. 选择“保存”以保存连接设置。

1. 重复上一步，在之前选择的“地铁”中，向 Microsoft 与你的网络同时所在的所有设施添加更多连接。

1. 添加所有所需的连接后，请选择“查看 + 创建”。

    > [!div class="mx-imgBorder"]
    > ![对等互连“配置”选项卡（最终）](../media/setup-direct-conf-tab-final.png)

1. 请注意，门户会对你输入的信息运行基本的验证。 顶部的功能区会显示“正在运行最终验证…”消息。

    > [!div class="mx-imgBorder"]
    > ![“对等互连验证”选项卡](../media/setup-direct-review-tab-validation.png)

1. 当消息更改为“验证通过”后，请验证你的信息。 选择“创建”，以提交请求。 若要修改请求，请选择“上一步”，然后重复上述步骤。

    > [!div class="mx-imgBorder"]
    > ![对等互连提交](../media/setup-direct-review-tab-submit.png)

1. 提交请求后，等待部署完成。 如果部署失败，请联系 [Microsoft 对等互连](mailto:peering@microsoft.com)。 此时会显示部署已成功的消息，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连成功](../media/setup-direct-success.png)
