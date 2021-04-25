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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680963"
---
1. 在“创建对等互连”页的“配置”选项卡上，按如下所示填写相应的框 。

    > [!div class="mx-imgBorder"]
    > ![“创建对等互连”页的 Exchange 对等互连类型](../media/setup-exchange-conf-tab.png)

    * 对于“对等互连类型”，选择“Exchange” 。
    * 对于“SKU”，选择“基本免费” 。
    * 选择要设置对等互连的“地铁”位置。

        > [!NOTE]
        > 如果所选“地铁”位置已有 Microsoft 对等互连连接，并且你是第一次使用门户在该位置设置对等互连，现有的对等互连连接将会在“对等互连连接”部分中列出，如下所示 。 Microsoft 将自动将这些对等互连连接转换为 Azure 资源，以便你可以在一个位置管理所有这些连接和新连接。 有关详细信息，请参阅[使用门户将旧版 Exchange 对等互连转换为 Azure 资源](../howto-legacy-exchange-portal.md)。
        >

1. 在“对等互连连接”下，选择“新建”，为要设置的每个新连接添加一行 。

    * 若要配置或修改连接设置，请选择行的“编辑”按钮。

        > [!div class="mx-imgBorder"]
        > ![“编辑”按钮](../media/setup-exchange-conf-tab-edit.png)

    * 若要删除行，请选择“...” > “删除” 。

        > [!div class="mx-imgBorder"]
        > ![“删除”按钮](../media/setup-exchange-conf-tab-delete.png)

    * 需要提供连接的所有设置，如下所示。

         > [!div class="mx-imgBorder"]
         > ![Exchange 对等互连连接页](../media/setup-exchange-conf-tab-connection.png)

        1. 选择需要设置连接的“对等互连设备”。
        1. 在“IPv4 地址”和“IPv6 地址”框中，分别输入 IPv4 和 IPv6 地址，这些地址将使用 neighbor 命令在 Microsoft 路由器中配置 。
        1. 在“播发的最大 IPv4 地址数”和“播发的最小 IPv6 地址数”框中，分别输入要播发的 IPv4 和 IPv6 前缀数量 。
        1. 选择“确定”保存连接设置。

1. 重复此步骤，以在 Microsoft 与你的网络（在之前所选的“地铁”）中的任何设备上添加更多连接。

1. 添加所有所需的连接后，选择“查看 + 创建”。

    > [!div class="mx-imgBorder"]
    > ![对等互连配置选项卡](../media/setup-exchange-conf-tab-final.png)

1. 请注意，门户会对你输入的信息运行基本的验证。 顶部的功能区会显示“正在运行最终验证...”消息。

    > [!div class="mx-imgBorder"]
    > ![“对等互连验证”选项卡](../media/setup-direct-review-tab-validation.png)

1. 当消息更改为“验证通过”后，请验证你的信息。 选择“创建”，以提交请求。 若要修改请求，请选择“上一步”，然后重复上述步骤。

    > [!div class="mx-imgBorder"]
    > ![对等互连提交](../media/setup-exchange-review-tab-submit.png)

1. 提交请求后，等待部署完成。 如果部署失败，请联系 [Microsoft 对等互连](mailto:peering@microsoft.com)。 此时会显示部署已成功的消息，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连成功](../media/setup-direct-success.png)
