---
title: 使用 Check Point Cloudguard Connect 保护 Azure 虚拟中心的安全
description: 了解用于保护 Azure 虚拟中心的 Check Point Cloudguard Connect
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 3c61dc689d19e1a7d6f9b6dbefae846e9458d750
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146852"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>使用 Check Point Cloudguard Connect 保护虚拟中心的安全

Check Point CloudGuard Connect 是 Azure 防火墙管理器中信任的安全合作伙伴。 它使用高级威胁防护来保护从全球分布的分支机构到 Internet (B2I) 的连接或从虚拟网络到 Internet (V2I) 的连接。 

使用 Azure 防火墙管理器中的简单配置，可以通过 CloudGuard Connect 安全即服务 (SECaaS) 将分支中心连接和虚拟网络连接路由到 Internet。 在 IPsec VPN 隧道中，流量在从中心传输到 Check Point 云服务时受到保护。

在 Check Point 门户中启用自动同步时，任何在 Azure 门户中标记为“受保护”的资源都会自动受到保护。 你无需两次管理资产。 只需选择在 Azure 门户中保护它们一次即可。

Check Point 将多个安全服务统一在一起。 集成的安全流量会被解密一次，并在单次传递中受到检查。 应用程序控制、URL 筛选和内容感知 (DLP) 强制实施安全的 Web 使用并保护你的数据。 IPS 和防病毒功能保护用户免受已知的网络攻击。 Anti-Bot 阻止与命令和控制服务器的连接，并在主机被感染时向你发出警报。

威胁模拟（沙盒）保护用户免受未知威胁和零日威胁。 Check Point SandBlast 零日防护是一种云托管的沙盒技术，可以快速隔离和检查文件。 它在虚拟沙盒中运行，在恶意行为进入网络之前发现恶意行为。 它在破坏发生之前阻止威胁，以节省员工响应威胁的宝贵时间。 

## <a name="deployment-example"></a>部署示例

观看以下视频，了解如何将 Check Point CloudGuard Connect 部署为受信任的 Azure 安全合作伙伴。

> [!VIDEO https://www.youtube.com/embed/C8AuN76DEmU]

## <a name="next-steps"></a>后续步骤

- [部署安全合作伙伴提供程序](deploy-trusted-security-partner.md)