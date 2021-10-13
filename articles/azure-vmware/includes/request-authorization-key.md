---
title: 请求 ExpressRoute 的授权密钥
description: 请求 ExpressRoute 授权密钥的步骤。
ms.topic: include
ms.date: 03/15/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 1ab25c1c22dd2697b9101d76602e81d22adc5b37
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638284"
---
<!-- used in tutorial-expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. 在 Azure 门户中，导航到 Azure VMware 解决方案私有云。 选择“管理” > “连接” > “ExpressRoute”，然后选择“+ 请求授权密钥”   。

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="屏幕截图显示了如何请求 ExpressRoute 授权密钥。" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. 为该授权密钥提供一个名称，然后选择“创建”。

   创建该密钥可能需要大约 30 秒。 创建后，新密钥会出现在私有云的授权密钥列表中。

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="屏幕截图显示了 ExpressRoute Global Reach 授权密钥。" lightbox="../media/expressroute-global-reach/show-global-reach-auth-key.png":::
  
1. 复制授权密钥和 ExpressRoute ID。 你需要使用它们来完成对等互连。 授权密钥会在一段时间后消失，因此请在它出现时立即复制它。

