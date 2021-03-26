---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: 7aee201b0419b65c7ea8ddcb6f2d42ffaff4711e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750691"
---
|  | **专用对等互连** | **Microsoft 对等互连** |  **公共对等互连**（新的线路已弃用） |
| --- | --- | --- | --- |
| **每个对等互连支持的最大前缀数** |默认情况下为 4000，而 ExpressRoute 高级版支持 10,000 |200 |200 |
| **支持的 IP 地址范围** |WAN 中任何有效的 IP 地址。 |由你或连接提供商拥有的公共 IP 地址。 |由你或连接提供商拥有的公共 IP 地址。 |
| **AS 编号要求** |专用和公共 AS 编号。 如果选择使用公共 AS 编号，必须拥有该编号。 |专用和公共 AS 编号。 但是，必须证明对公共 IP 地址的所有权。 |专用和公共 AS 编号。 但是，必须证明对公共 IP 地址的所有权。 |
| **支持的 IP 协议**| IPv4、IPv6（预览版） |  IPv4、IPv6 | IPv4 |
| **路由接口 IP 地址** |RFC1918 和公共 IP 地址 |在路由注册表中注册的公共 IP 地址。 |在路由注册表中注册的公共 IP 地址。 |
| **MD5 哈希支持** |是 |是 |是 |