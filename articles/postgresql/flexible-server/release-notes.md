---
title: Azure Database for PostgreSQL - 灵活服务器发行说明
description: Azure Database for PostgreSQL - 灵活服务器的发行说明。
author: sr-msft
ms.author: srranga
ms.custom: references_regions
ms.service: postgresql
ms.topic: overview
ms.date: 06/23/2021
ms.openlocfilehash: 87af6f9764c2ab01b0e0d02d8eb4a6c7342ca31c
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894619"
---
# <a name="release-notes---azure-database-for-postgresql---flexible-server"></a>发行说明 - Azure Database for PostgreSQL - 灵活服务器

本页提供有关功能添加、引擎版本支持、扩展以及与 Flexible Server - PostgreSQL 相关的任何其他公告的最新新闻和更新。

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

## <a name="release-june-2021"></a>发布时间：2021 年 6 月

* 支持通过创建新服务器使用[最新的 PostgreSQL 次要版本](./concepts-supported-versions.md) 13.3、12.7 和 11.12<sup>$</sup>。
* 支持[新区域](overview.md#azure-regions)，包括澳大利亚东南部、巴西南部、韩国中部、挪威东部、南非北部、瑞士北部、阿拉伯联合酋长国北部以及美国西部。
* 支持[按需故障转移](./concepts-high-availability.md#on-demand-failover)功能，包括用于区域冗余高可用性部署的强制故障转移和计划的故障转移。
* 支持通过创建新服务器对所有主版本进行 [SCRAM 身份验证](how-to-connect-scram.md)<sup>$</sup>。
* 支持通过创建新服务器使用 `shared_preload_libraries` 预加载 `pg_prewarm`<sup>$</sup>。
* 支持 lo 扩展。 有关每个主版本支持的版本，请参阅[扩展页](./concepts-extensions.md)<sup>$</sup>。
* 修复了多个 bug，并提升了稳定性和性能<sup>$</sup>。
  
<sup> **$**</sup> 你的现有服务器将自动升级到受支持的最新次要版本，还会在服务器将来的维护时段内启用新功能。

## <a name="release-may-2021"></a>发布时间：2021 年 5 月

* 支持 [PostgreSQL 主版本 13](./concepts-supported-versions.md)。
* 支持扩展（包括 pg_partman、pg_cron 和 pgaudit）。 有关每个主版本支持的版本，请参阅[扩展页](./concepts-extensions.md)。
* 修复了多个 bug，并提升了稳定性和性能。

## <a name="release-april-2021"></a>发布时间：2021 年 4 月

* 支持通过创建新服务器使用[最新的 PostgreSQL 次要版本](./concepts-supported-versions.md) 12.6 和 11.11。
* 支持虚拟网络 (VNET) [专用 DNS 区域](./concepts-networking.md#private-access-vnet-integration)。
* 支持在时间点还原操作期间选择可用性区域。
* 支持新[区域](./overview.md#azure-regions)，包括澳大利亚东部、加拿大中部和法国中部。
* 支持[内置的 PgBouncer](./concepts-pgbouncer.md) 连接池程序。 
<!--- * Support for [pglogical](https://github.com/2ndQuadrant/pglogical) extension version 2.3.2. -->
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