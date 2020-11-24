---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: fd7124ad83a446d7dde39a836c337a97b12a0a0a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559777"
---
现在可以使用[专用终结点](../articles/private-link/private-endpoint-overview.md)将数据从虚拟网络内的服务器安全地备份到恢复服务保管库。 专用终结点为保管库使用 VNET 地址空间中的 IP。 虚拟网络中的资源与保管库之间的网络流量将通过虚拟网络和 Microsoft 主干网络上的专用链接传输。 这样就不会从公共 Internet 泄露信息。 专用终结点可用于备份和还原在 Azure VM 中运行的 SQL 数据库和 SAP HANA 数据库。 它还可以通过 MARS 代理用于本地服务器。

Azure VM 备份不需要 Internet 连接，因此不需要专用终结点允许网络隔离。

在[此处](../articles/backup/private-endpoints.md)详细了解 Azure 备份的专用终结点。