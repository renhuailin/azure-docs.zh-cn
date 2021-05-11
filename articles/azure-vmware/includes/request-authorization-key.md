---
title: 请求 ExpressRoute 的授权密钥
description: 请求 ExpressRoute 授权密钥的步骤。
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 44ca81e25dda61ab32ea3455a1f228e134952582
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945551"
---
<!-- used in tutorial-expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. 在 Azure 门户中，导航到 Azure VMware 解决方案私有云。 选择“管理” > “连接” > “ExpressRoute”，然后选择“+ 请求授权密钥”   。

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="屏幕截图显示了如何请求 ExpressRoute 授权密钥。" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. 为该授权密钥提供一个名称，然后选择“创建”。

   创建该密钥可能需要大约 30 秒。 创建后，新密钥会出现在私有云的授权密钥列表中。

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="屏幕截图显示了 ExpressRoute Global Reach 授权密钥。":::
  
1. 复制授权密钥和 ExpressRoute ID。 你需要使用它们来完成对等互连。 授权密钥会在一段时间后消失，因此请在它出现时立即复制它。

