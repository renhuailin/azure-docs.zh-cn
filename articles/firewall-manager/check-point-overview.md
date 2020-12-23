---
title: 使用检查点 Cloudguard 连接来保护 Azure 虚拟中心
description: 了解检查点 CloudGuard 连接到 secure Azure 虚拟中心的相关信息
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 3c61dc689d19e1a7d6f9b6dbefae846e9458d750
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146852"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>使用 Check Point Cloudguard Connect 保护虚拟中心

Check Point CloudGuard Connect 是 Azure 防火墙管理器中的受信任安全合作伙伴。 它通过高级威胁防护，保护跨 internet (B2I) 或虚拟网络到 Internet (V2I) 连接的 Internet。 

使用 Azure 防火墙管理器中的简单配置，可以通过 CloudGuard Connect security as service (SECaaS) 将分支中心和虚拟网络连接路由到 Internet。 流量在从中心传输到 IPsec VPN 隧道中的检查点云服务时受到保护。

在检查点门户中启用自动同步时，会自动保护在 Azure 门户中标记为 *受保护* 的任何资源。 你无需两次管理你的资产。 只需选择在 Azure 门户中保护一次它们即可。

检查点在一个伞下统一了多个安全服务。 集成的安全流量将解密一次，并在单个传递中检查。 应用程序控制、URL 筛选和内容感知 (DLP) 强制安全 web 使用和保护数据。 IPS 和防病毒保护用户免受已知的网络攻击。 防机器人会阻止与命令和控制服务器的连接，并在主机受到感染时发出警报。

威胁模拟 (沙箱处理) 保护用户免受未知和零天的威胁。 Check Point SandBlast Zero-Day 保护是一种云托管的沙滩-装箱技术，其中的文件已快速隔离和检查。 它在虚拟沙盒中运行，在进入网络之前发现恶意行为。 它可防止在完成损坏之前面临的威胁，从而节省员工宝贵的响应威胁的时间。 

## <a name="deployment-example"></a>部署示例

观看以下视频，了解如何将 Check Point CloudGuard Connect 部署为受信任的 Azure 安全合作伙伴。

> [!VIDEO https://www.youtube.com/embed/C8AuN76DEmU]

## <a name="next-steps"></a>后续步骤

- [部署安全合作伙伴提供程序](deploy-trusted-security-partner.md)