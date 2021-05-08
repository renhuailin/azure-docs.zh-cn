---
title: Microsoft Azure 中的双重加密
description: 本文介绍 Microsoft Azure 如何为静态数据和传输中的数据提供双重加密。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88227030"
---
# <a name="double-encryption"></a>双重加密
双重加密是指启用了两个或更多独立加密层，目的是在任一加密层遭到破坏的情况下提供保护。 使用两个加密层可减少加密数据带来的威胁。 例如： 。

- 数据加密中的配置错误
- 加密算法中的实现错误
- 单一加密密钥遭到泄露

Azure 为静态数据和传输中的数据提供双重加密。

## <a name="data-at-rest"></a>静态数据
Microsoft 针对静态数据启用双重加密的方法是：

- **使用客户管理的密钥进行磁盘加密**。 你提供自己的密钥用于加密磁盘。 你可将自己的密钥带到 Key Vault 中（BYOK - 创建自己的密钥），也可在 Azure Key Vault 中生成新的密钥来加密所需资源。
- **使用平台管理的密钥进行基础结构加密**。  默认情况下，使用平台管理的加密密钥自动对磁盘进行静态加密。

## <a name="data-in-transit"></a>传输中的数据
Microsoft 针对传输中的数据启用双重加密的方法是：

- **使用传输层安全性 (TLS) 协议 1.2 进行传输加密，在云服务与你之间传输数据时对数据进行保护。** 即使流量目标是同一区域中的另一个域控制器，仍会在传输过程中对离开数据中心的所有流量进行加密。 使用的默认安全协议是 TLS 1.2。 TLS 提供严格的身份验证，消息隐私性和完整性强（允许检测消息篡改、拦截和伪造），具有良好的互操作性，算法灵活，易于部署和使用。
- **在基础结构层提供的其他加密层**。 在底层网络硬件上点对点实施使用 IEEE 802.1AE MAC 安全标准（也称 MACsec）的数据链路层加密方法。 Azure 客户流量每次在数据中心之间（在不受 Microsoft 或代表 Microsoft 的某方控制的物理边界之外）移动时，数据包都会在发送之前在设备上进行加密和解密，以防止物理上的“中间人”攻击或窥探/窃听攻击。 由于此技术在网络硬件本身上集成，因此它会在网络硬件上提供线路速率加密，而不会增加可度量的链路延迟。 对于在区域内或区域之间传输的所有 Azure 流量，会默认启用此 MACsec 加密，客户无需执行任何操作。

## <a name="next-steps"></a>后续步骤
了解如何[在 Azure 中使用加密](encryption-overview.md)。
