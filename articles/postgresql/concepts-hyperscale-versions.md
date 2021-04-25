---
title: 支持的版本 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL 中可用的 PostgreSQL 版本 - 超大规模 (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d8a584b6ba752e8f9220defa575f519828ba07e6
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023793"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL 中支持的数据库版本 - 超大规模 (Citus)

## <a name="postgresql-versions"></a>PostgreSQL 版本

> [!IMPORTANT]
> 超大规模 (Citus) 中的可自定义 PostgreSQL 版本当前为预览版。  此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
>
> 请参阅[超大规模 (Citus) 的预览功能](hyperscale-preview-features.md)，以查看其他新功能的完整列表。

在创建过程中，可以自定义在超大规模 (Citus) 服务器组中运行的 PostgreSQL 的版本。 当前选择除版本 11 之外的任何内容都是预览功能。

超大规模 (Citus) 目前支持以下主版本：

### <a name="postgresql-version-13-preview"></a>PostgreSQL 版本 13（预览版）

当前次要版本为 13.2。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/13/static/release-13-2.html)。

### <a name="postgresql-version-12-preview"></a>PostgreSQL 版本 12（预览版）

当前次要版本为 12.6。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/12/static/release-12-6.html)。

### <a name="postgresql-version-11"></a>PostgreSQL 版本 11

当前次要版本为 11.11。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/11/static/release-11-11.html)。

### <a name="postgresql-version-10-and-older"></a>PostgreSQL 版本 10 及更早版本

对于 Azure Database for PostgreSQL 超大规模 (Citus)，我们不支持 PostgreSQL 版本 10 及更早版本。

## <a name="citus-and-other-extension-versions"></a>Citus 和其他扩展版本

根据服务器组中运行的 PostgreSQL 版本，还将安装不同的 [Postgres 扩展版本](concepts-hyperscale-extensions.md)。  特别是，Postgres 13 将安装 Citus 10，而更早的 Postgres 版本将安装 Citus 9.5。

## <a name="next-steps"></a>后续步骤

* 查看在哪些版本中安装了哪些[扩展](concepts-hyperscale-extensions.md)。
* 了解如何[创建超大规模 (Citus) 服务器组](quickstart-create-hyperscale-portal.md)。
