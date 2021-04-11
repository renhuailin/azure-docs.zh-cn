---
author: v-amallick
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: v-amallick
ms.openlocfilehash: d20ed4d39921f8000f77f947c4372bd8b10ca642
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294091"
---
现在可以使用[专用终结点](../articles/private-link/private-endpoint-overview.md)将数据从虚拟网络内的服务器安全地备份到恢复服务保管库。 专用终结点为保管库使用 VNET 地址空间中的 IP。 虚拟网络中的资源与保管库之间的网络流量将通过虚拟网络和 Microsoft 主干网络上的专用链接传输。 这样就不会从公共 Internet 泄露信息。 专用终结点可用于备份和还原在 Azure VM 中运行的 SQL 数据库和 SAP HANA 数据库。 它还可以通过 MARS 代理用于本地服务器。

Azure VM 备份不需要 Internet 连接，因此不需要专用终结点允许网络隔离。

在[此处](../articles/backup/private-endpoints.md)详细了解 Azure 备份的专用终结点。