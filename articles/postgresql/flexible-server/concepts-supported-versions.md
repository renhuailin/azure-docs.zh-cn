---
title: 支持的版本 - Azure Database for PostgreSQL 灵活服务器
description: 介绍 Azure Database for PostgreSQL 灵活服务器中支持的 PostgreSQL 主版本和次要版本。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/23/2021
ms.openlocfilehash: 7f779d3856187cb6ce5db55b3a974fdd3818d8dc
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894934"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 灵活服务器中支持的 PostgreSQL 主版本

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

Azure Database for PostgreSQL 灵活服务器目前支持以下主版本：

## <a name="postgresql-version-13"></a>PostgreSQL 版本 13

当前的次要版本为 13.3。 若要详细了解此版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/13/static/release-13-3.html)。 新服务器将使用此次要版本创建。 

## <a name="postgresql-version-12"></a>PostgreSQL 版本 12

当前的次要版本为 12.7。 若要详细了解此版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/12/static/release-12-7.html)。 新服务器将使用此次要版本创建。 现有服务器会在将来的计划性维护时段自动升级到受支持的最新次要版本。

## <a name="postgresql-version-11"></a>PostgreSQL 版本 11

当前的次要版本为 11.12。 若要详细了解此版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/11/static/release-11-12.html)。 新服务器将使用此次要版本创建。 现有服务器会在将来的计划性维护时段自动升级到受支持的最新次要版本。

## <a name="postgresql-version-10-and-older"></a>PostgreSQL 版本 10 及更早版本

对于 Azure Database for PostgreSQL 灵活服务器，我们不支持 PostgreSQL 版本 10 及更早版本。 如果需要较旧版本，请使用[单一服务器](../concepts-supported-versions.md)部署选项。

## <a name="managing-upgrades"></a>管理升级

PostgreSQL 项目定期发布次要版本来修复已报告的 bug。 Azure Database for PostgreSQL 在服务的每月部署期间会自动使用次要版本修补服务器。

尚不支持自动主版本升级。 例如，当前没有从 PostgreSQL 11 到 PostgreSQL 12 的自动升级。<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->