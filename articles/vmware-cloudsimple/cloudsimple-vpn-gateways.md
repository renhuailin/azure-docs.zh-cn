---
title: Azure VMware Solution by CloudSimple - VPN 网关
description: 了解 CloudSimple 站点到站点和点到站点 VPN 网关，其用于在 CloudSimple 区域与其他资源之间发送加密流量。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e013bb96990a8f3a0ef7d3a58529b200919e276
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140626"
---
# <a name="vpn-gateways-overview"></a>VPN 网关概述

VPN 网关用于在本地位置的 CloudSimple 区域网络或通过公共 internet 的计算机之间发送加密流量。  每个区域可以有一个 VPN 网关，该网关可以支持多个连接。 与同一个 VPN 网关建立多个连接时，所有 VPN 隧道共享可用的网关带宽。

CloudSimple 提供了两种类型的 VPN 网关：

* 站点到站点 VPN 网关
* 点到站点 VPN 网关

## <a name="site-to-site-vpn-gateway"></a>站点到站点 VPN 网关

站点到站点 VPN 网关用于在 CloudSimple 区域网络和本地数据中心之间发送加密流量。 使用此连接定义子网/CIDR 范围，用于在本地网络与 CloudSimple 区域网络之间的网络流量。

使用 VPN 网关，可以在私有云上使用本地服务，也可以从本地网络使用私有云上的服务。  CloudSimple 提供基于策略的 VPN 服务器，用于从本地网络建立连接。

站点到站点 VPN 的用例：

* 本地网络中任何工作站的私有云 vCenter 的可访问性。
* 使用本地 Active Directory 作为 vCenter 标识源。
* 方便地将 VM 模板、ISO 和其他文件从本地资源传输到私有云 vCenter。
* 从本地网络在私有云上运行的工作负荷的可访问性。

![站点到站点 VPN 连接拓扑](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>加密参数

站点到站点 VPN 连接使用以下默认加密参数来建立安全连接。  从本地 VPN 设备创建连接时，请使用本地 VPN 网关支持的以下任何参数。

#### <a name="phase-1-proposals"></a>阶段 1 提议

| 参数 | 提议 1 | 提议 2 | 提议 3 |
|-----------|------------|------------|------------|
| SDK 版本 | IKEv1 | IKEv1 | IKEv1 |
| 加密 | AES 128 | AES 256 | AES 256 |
| 哈希算法| SHA 256 | SHA 256 | SHA 1 |
| Diffie-Hellman 组（DH 组） | 2 | 2 | 2 |
| 生存时间 | 28,800 秒 | 28,800 秒 | 28,800 秒 |
| 数据大小 | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>阶段 2 提议

| 参数 | 提议 1 | 提议 2 | 提议 3 |
|-----------|------------|------------|------------|
| 加密 | AES 128 | AES 256 | AES 256 |
| 哈希算法| SHA 256 | SHA 256 | SHA 1 |
| 完全向前保密组（PFS 组） | 无 | 无 | 无 |
| 生存时间 | 1,800 秒 | 1,800 秒 | 1,800 秒 |
| 数据大小 | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> 在 VPN 设备上将 TCP MSS 钳位设置为 1200。 或者，如果 VPN 设备不支持 MSS 钳位，则可以改为在隧道接口上将 MTU 设置为 1240 字节。

## <a name="point-to-site-vpn-gateway"></a>点到站点 VPN 网关

点到站点 VPN 用于在 CloudSimple 区域网络和客户端计算机之间发送加密流量。  点到站点 VPN 是访问私有云网络（包括私有云 vCenter 和工作负荷 VM）的最简单方法。  如果要远程连接到私有云，请使用点到站点 VPN 连接。

## <a name="next-steps"></a>后续步骤

* [设置 VPN 网关](vpn-gateway.md)
