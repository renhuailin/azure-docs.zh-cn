---
title: Azure VMware Solution by CloudSimple - 公共 IP 地址
description: 了解 Azure VMware Solution by CloudSimple 中的公共 IP 地址及其好处
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "77024969"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple 公共 IP 地址概述

Internet 资源可以通过公共 IP 地址与专用 IP 地址上的私有云资源进行入站通信。 专用 IP 地址是私有云 vCenter 上的虚拟机或软件负载均衡器。 可以使用公共 IP 地址向 Internet 公开在私有云上运行的服务。

公共 IP 地址将专用于专用 IP 地址，直到你取消分配该地址为止。 一个公共 IP 地址只能分配给一个专用 IP 地址。

与公共 IP 地址关联的资源始终使用公共 IP 地址进行 Internet 访问。 默认情况下，公共 IP 地址上只允许出站 Internet 访问。  公共 IP 地址上的传入流量会被拒绝。  若要允许入站流量，请为特定端口的公共 IP 地址创建防火墙规则。

## <a name="benefits"></a>优点

使用公共 IP 地址进行入站通信可提供：

* 分布式拒绝服务 (DDoS) 攻击防护。 此防护是自动为公共 IP 地址启用的。
* 针对常见网络级别攻击的始终启用的流量监视和实时缓解。 这些防御与 Microsoft 联机服务使用的防御相同。
* 完整规模的 Azure 全球网络。 该网络可用于跨区域分配和缓解攻击流量。  

## <a name="next-steps"></a>后续步骤

* 了解如何[分配公共 IP 地址](public-ips.md)