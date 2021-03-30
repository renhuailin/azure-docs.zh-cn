---
title: 请求 ExpressRoute 的授权密钥
description: 请求 ExpressRoute 授权密钥的步骤。
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 54a610c8b0f3f3fe9d3ebe39291bba7767007839
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491822"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. 在 Azure 门户的“连接”部分的“ExpressRoute”选项卡上，选择“+ 请求授权密钥”。 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="屏幕截图显示了如何请求 ExpressRoute 授权密钥。" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. 为该授权密钥提供一个名称，然后选择“创建”。 
      
   创建该密钥可能需要大约 30 秒。 创建后，新密钥会出现在私有云的授权密钥列表中。

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="屏幕截图显示了 ExpressRoute Global Reach 授权密钥。":::
  
1. 记下此授权密钥和 ExpressRoute ID。 你将使用它们来完成对等互连。  

   > [!NOTE]
   > 授权密钥会在一段时间后消失，因此请在它出现时立即复制它。