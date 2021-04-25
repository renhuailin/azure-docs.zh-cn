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
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678559"
---
1. 在“创建对等互连”页的“配置”选项卡上，按如下所示填写相应的框 。

    > [!div class="mx-imgBorder"]
    > ![“创建对等互连”页的 Exchange 对等互连类型](../media/setup-exchange-conf-tab.png)

    * 对于“对等互连类型”，选择“Exchange” 。
    * 对于“SKU”，选择“基本免费” 。
    * 选择“地铁”位置以便在其中将对等互连转换为 Azure 资源。 如果所选“地铁”位置中有未转换为 Azure 资源的 Microsoft 对等互连连接，这样的连接将在“对等互连连接”部分中列出，如图所示 。 你现在可以将这些对等互连连接转换为 Azure 资源。

        > [!div class="mx-imgBorder"]
        > ![对等互连连接列表](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > 不能修改旧版对等互连连接的设置。 如果要在所选“地铁”位置中添加其他 Microsoft 对等互连连接，请选择“新建” 。 有关详细信息，请参阅[使用门户创建或修改 Exchange 对等互连](../howto-exchange-portal.md)。
        >

1. 选择“查看 + 创建”  。 请注意，门户会对你输入的信息运行基本的验证。 顶部的功能区会显示“正在运行最终验证...”消息。

    > [!div class="mx-imgBorder"]
    > ![“对等互连验证”选项卡](../media/setup-direct-review-tab-validation.png)

1. 当消息更改为“验证通过”后，请验证你的信息。 选择“创建”，以提交请求。 若需要修改请求，请选择“上一步”，然后重复上述步骤。

    > [!div class="mx-imgBorder"]
    > ![对等互连提交](../media/setup-exchange-review-tab-submit.png)

1. 提交请求后，等待部署完成。 如果部署失败，请联系 [Microsoft 对等互连](mailto:peering@microsoft.com)。 此时会显示成功的部署，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连成功](../media/setup-direct-success.png)
