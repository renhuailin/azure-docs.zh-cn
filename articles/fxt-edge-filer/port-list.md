---
title: Azure FXT Edge Filer 的端口列表
description: FXT 群集环境使用的 TCP/UDP 端口列表
author: ekpgh
ms.author: v-erkel
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 28de9732f6a22f730059c08b5be939c23e52ebaa
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111414753"
---
# <a name="required-network-ports"></a>所需的网络端口

此列表显示 FXT 群集环境所需的 TCP/UDP 端口。 配置使用的任何防火墙，以确保这些端口可访问。

系统的具体要求因使用的后端存储类型而异。

有关详细信息，请与 Microsoft 服务和支持部门联系。

## <a name="api-ports"></a>API 端口

| 方向 | 类型 | 端口号 | 协议 |
|-----------|------|-------------|----------|
| 入站   | TCP  | 22          | SSH      |
| 出站  | TCP  | 80          | HTTP     |
| 入站和出站  | TCP  | 443         | HTTPS    |

## <a name="nfs-ports"></a>NFS 端口

入站 NFS 端口：

| 类型    | 端口号 | 服务  |
|---------|-------------|----------|
| TCP/UDP | 111         | RPCBIND  |
| TCP/UDP | 2049        | NFS      |
| TCP/UDP | 4045        | NLOCKMGR |
| TCP/UDP | 4046        | 装载   |
| TCP/UDP | 4047        | 状态   |

出站 NFS 端口：

| 类型    | 端口号 | 服务  |
|---------|-------------|----------|
| TCP/UDP | 111         | RPCBIND  |
| TCP/UDP | 2049        | NFS      |

出站 NFS 端口流量因用作核心文件管理器的存储类型而异。 （尽管此处列出了常用的端口 2049，但某些系统不使用该端口。 所有群集都需要访问端口 111。）请查看存储系统的文档或使用下面的[其他端口要求](#additional-port-requirements)中所描述的查询技术。

来自 FXT 节点的一些出站 NFS 流量使用临时端口。 不应在传输级别限制上述已知端口的出站 FXT 流量。

## <a name="smb-ports"></a>SMB 端口

| 方向 | 类型 | 端口号 | 协议 |
|-----------|------|-------------|----------|
| 入站和出站  | UDP  | 137         | NetBIOS  |
| 入站   | UDP  | 138         | NetBIOS  |
| 入站和出站  | TCP  | 139         | SMB      |
| 入站和出站  | TCP  | 445         | SMB      |

<!--| Outbound  | UDP  | 137         | NetBIOS  | 
| Outbound  | TCP  | 139         | SMB      |
| Outbound  | TCP  | 445         | SMB      |
-->

## <a name="general-traffic-ports"></a>常规流量端口

| 方向 | 类型    | 端口号 | 协议 |
|-----------|---------|-------------|----------|
| 出站  | TCP/UDP | 53          | DNS      |
| 出站  | TCP/UDP | 88          | Kerberos |
| 出站  | UDP     | 123         | NTP      |
| 出站  | TCP/UDP | 389         | LDAP     |
| 出站  | TCP     | 686         | LDAPS    |

## <a name="additional-port-requirements"></a>其他端口要求

核心文件管理器可能需要访问其他端口。 此要求因所使用的存储类型而异。

可以使用 `rpcinfo` 命令了解特定服务器使用的端口。 从未使用防火墙的客户端系统发出以下命令：

`rpcinfo -p <server_IP_address>`

## <a name="next-steps"></a>后续步骤

* 了解如何向 Azure FXT Edge Filer 群集[添加存储](add-storage.md)
* [联系支持部门](support-ticket.md)，了解有关端口配置的详细信息
