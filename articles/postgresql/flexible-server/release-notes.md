---
title: Azure Database for PostgreSQL - 灵活服务器发行说明
description: Azure Database for PostgreSQL - 灵活服务器的发行说明。
author: sr-msft
ms.author: srranga
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: ad417bc44de7d13594f003f7aa1e39b2308204ed
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064856"
---
# <a name="release-notes---azure-database-for-postgresql---flexible-server"></a>发行说明 - Azure Database for PostgreSQL - 灵活服务器

本页提供有关功能添加、引擎版本支持、扩展以及与 Flexible Server - PostgreSQL 相关的任何其他公告的最新新闻和更新。

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

## <a name="release-april-26-2021"></a>发布日期：2021 年 4 月 26 日

* 支持通过创建新服务器使用[最新的 PostgreSQL 次要版本](./concepts-supported-versions.md) 12.6 和 11.11。
* 支持虚拟网络 (VNET) [专用 DNS 区域](./concepts-networking.md#private-access-vnet-integration)。
* 支持在时间点还原操作期间选择可用性区域。
* 支持新[区域](./overview.md#azure-regions)，包括澳大利亚东部、加拿大中部和法国中部。
* 支持[内置的 PgBouncer](./concepts-pgbouncer.md) 连接池程序。 
* 支持 [pglogical](https://github.com/2ndQuadrant/pglogical) 扩展版本 2.3.2。
* [智能性能](concepts-query-store.md)目前提供公共预览版。
* 修复了多个 bug，并提升了稳定性和性能。

## <a name="contacts"></a>联系人

如果你有任何关于 Azure Database for PostgreSQL 灵活服务器的问题或建议，请向 Azure Database for PostgreSQL 团队 ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)) 发送电子邮件。 请注意，此电子邮件地址不是技术支持别名。

另外，请酌情考虑以下联系点：

- 若要联系 Azure 支持，请[从 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 若要修复帐户问题，请在 Azure 门户中提交[支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
- 若要提供反馈或请求新功能，请通过 [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql) 创建条目。
  

## <a name="next-steps"></a>后续步骤

现在，你已阅读 Azure Database for PostgreSQL 灵活服务器部署模式简介，接下来可创建第一个服务器了：[使用 Azure 门户创建 Azure Database for PostgreSQL 灵活服务器](./quickstart-create-server-portal.md)