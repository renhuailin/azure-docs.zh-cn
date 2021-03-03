---
title: 虚拟 WAN 点到站点 IPsec 策略
titleSuffix: Azure Virtual WAN
description: 了解 Azure 虚拟 WAN 点到站点 IPsec 连接策略。
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: wellee
ms.openlocfilehash: d64748bf7719af52b76c5e4141bfbbabe80bbae2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744526"
---
# <a name="point-to-site-ipsec-policies"></a>点到站点 IPsec 策略

本文介绍 Azure 虚拟 WAN 中的点到站点 VPN 连接支持的 IPsec 策略组合。

## <a name="default-ipsec-policies"></a>默认的 IPsec 策略

下表显示了点到站点 VPN 连接的默认 IPsec 参数。 当创建点到站点配置文件时，虚拟 WAN 点到站点 VPN 网关会自动选择这些参数。

| 设置 | parameters |
|--- |--- |
| 阶段 1 IKE 加密 | AES256 |
| 阶段 1 IKE 完整性 |  SHA256 |
| DH 组 | DHGroup24 |
| 阶段 2 IPsec 加密 | GCMAES256|
| 阶段 2 IPsec 完整性 | GCMAES25 |
| PFS 组 |PFS24|

## <a name="custom-ipsec-policies"></a>自定义 IPsec 策略

使用自定义 IPsec 策略时，请记住以下要求：

* **Ike** -对于阶段 1 ike，你可以从 ike 加密中选择任何参数，并从 ike 完整性中选择任何参数，以及从 DH 组中选择任何参数。
* **Ipsec** -对于阶段 2 ipsec，可以从 ipsec 加密中选择任何参数，并从 ipsec 完整性和 PFS 中选择任何参数。 如果 IPsec 加密或 IPsec 完整性的任何参数都是 GCM，则 IPsec 加密和完整性都必须使用相同的算法。 例如，如果为 IPsec 加密选择了 GCMAES128，则还必须为 IPsec 完整性选择 GCMAES128。  

下表显示了点到站点 VPN 连接的可用 IPsec 参数。

| 设置 | parameters |
|--- |--- |
| 阶段 1 IKE 加密 | AES128、AES256、GCMAES128、GCMAES256 |
| 阶段 1 IKE 完整性 |  SHA256，SHA384 |
| DH 组 | DHGroup14、DHGroup24、ECP256、ECP384 |
| 阶段 2 IPsec 加密 | GCMAES128、GCMAES256、SHA256|
| 阶段 2 IPsec 完整性 | GCMAES128、GCMAES256 |
| PFS 组 |PFS14、PFS24、ECP256、ECP384|

## <a name="next-steps"></a>后续步骤

请参阅 [如何创建点到站点连接](virtual-wan-point-to-site-portal.md)

有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 常见问题解答](virtual-wan-faq.md)。
