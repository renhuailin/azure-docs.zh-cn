---
title: “Azure Database for PostgreSQL - 超大规模 (Citus)”中的预览功能
description: 更新了当前处于预览状态的功能列表
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 07/09/2021
ms.openlocfilehash: 1e0f7c133a0ac0091355cba20d251333e6402212
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463532"
---
# <a name="preview-features-for-postgresql---hyperscale-citus"></a>“PostgreSQL - 超大规模 (Citus)”的预览功能

“Azure Database for PostgreSQL - 超大规模 (Citus)”提供未发布功能的预览版。 预览版本在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。  有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="features-currently-in-preview"></a>当前处于预览状态的功能

下面是当前提供预览版的功能：

* **[基本层](concepts-hyperscale-tiers.md)** 。 仅使用协调器节点而不使用任何工作器节点运行服务器组。 要执行初始测试和开发并处理小型生产工作负载，这是一种经济实惠的方式。
* **[PostgreSQL 12 和 13](concepts-hyperscale-versions.md)** 。
  在服务器组中使用最新的数据库版本。
* **[Citus 10](concepts-hyperscale-versions.md#citus-and-other-extension-versions)** 。
  在运行 PostgreSQL 13 的服务器组上自动安装。
* **[列式存储](concepts-hyperscale-columnar.md)** 。
  在磁盘上连续存储所选表的列（而不是行）。 支持磁盘上压缩。 适用于分析和数据仓库工作负载。
* **[只读副本](howto-hyperscale-read-replicas-portal.md)** （当前仅限同一区域）。 主服务器组发生的任何更改都反映在其副本中，针对副本的查询不会导致原始服务器上产生额外的负载。
  副本是提高只读工作负载性能的有用工具。
* **[托管 PgBouncer](concepts-hyperscale-connection-pool.md)** 。
  允许一次性将多个客户端连接到服务器组，同时可限制活动连接数的连接池程序。 它可满足连接请求，同时使协调器节点顺畅运行。
* **[pgAudit](concepts-hyperscale-audit.md)** 。 通过标准 PostgreSQL 日志记录功能提供详细的会话和对象审核日志记录。 它生成通过某些政府、金融或 ISO 认证审核所需的审核日志。
* [create_extension() UDF](concepts-hyperscale-extensions.md#use-postgresql-extensions)。
  允许创建需要管理员访问权限才能安装的扩展。

### <a name="available-regions-for-preview-features"></a>预览功能的可用区域

[超大规模 (Citus) 支持的所有区域](concepts-hyperscale-configuration-options.md#regions)都提供 pgAudit 扩展。
其他预览功能仅在美国东部提供。

## <a name="does-my-server-group-have-access-to-preview-features"></a>我的服务器组是否有权访问预览功能？

要确定你的超大规模 (Citus) 服务器组是否启用了预览功能，请导航到 Azure 门户中服务器组的“概述”页面。
如果看到属性“层级: 基本(预览)”或“层级: 标准(预览)”，则你的服务器组可以访问预览功能 。

### <a name="how-to-get-access"></a>如何获得访问权限

创建新的超大规模 (Citus) 服务器组时，选中“启用预览功能”复选框。

## <a name="contact-us"></a>联系我们

请向 [Ask AZURE DB For PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) 发送电子邮件，告诉我们你使用预览功能时的体验。
（此电子邮件地址并不是技术支持渠道。 有关技术问题，请创建[支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。）
