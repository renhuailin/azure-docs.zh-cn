---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: b9e275cbdaf264df3d0a40f032693fd93553e520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "84317707"
---
### <a name="database-tier"></a>数据库层

数据库层包含应用程序的数据库实例。 数据库可以是 Oracle DB、Oracle RAC 或 Oracle Exadata 数据库系统。 

如果选择使用 Oracle DB，则可以通过 Azure 市场中提供的 Oracle DB 映像将数据库实例部署在 Azure 上。 或者，你可以使用 Azure 与 OCI 之间的互连在 OCI 上采用 PaaS 模式部署 Oracle DB。

对于 Oracle RAC，可以采用 PaaS 模式使用 OCI。 建议使用双节点 RAC 系统。 虽然可以在 Azure CloudSimple 上采用 IaaS 模式部署 Oracle RAC，但这不是 Oracle 支持的配置。 请参阅[适用于授权云环境的 Oracle 程序](http://www.oracle.com/us/corporate/pricing/authorized-cloud-environments-3493562.pdf)。

最后，对于 Exadata 系统，请使用 OCI 互连并在 OCI 中部署 Exadata 系统。 上面的体系结构图显示了在 OCI 中跨两个子网部署的 Exadata 系统。

对于生产方案，请跨两个可用性区域（如果在 Azure 中部署）或两个可用性域（在 OCI 中）部署数据库的多个实例。 使用 Oracle Active Data Guard 同步主数据库和备用数据库。

数据库层只接收来自中间层的请求。 为便于管理，建议你设置一个网络安全组（如果在 OCI 中部署数据库，则为安全列表），从而仅允许来自中间层的端口 1521 和来自堡垒服务器的端口 22 上的请求。

对于部署在 OCI 中的数据库，必须使用连接到 FastConnect 线路的动态路由网关 (DRG) 设置单独的虚拟云网络。