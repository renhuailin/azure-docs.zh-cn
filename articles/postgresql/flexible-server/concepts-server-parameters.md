---
title: 服务器参数 - Azure Database for PostgreSQL 灵活服务器
description: 描述 Azure Database for PostgreSQL 灵活服务器中的服务器参数
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 526a06c1ffd110fa02fd3d412ab62882bd74f9fa
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387366"
---
# <a name="server-parameters-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 灵活服务器中的服务器参数

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

Azure Database for PostgreSQL 为每个服务器都提供了一部分可配置的参数。 有关 Postgres 参数的详细信息，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/13/config-setting.html)。

## <a name="an-overview-of-postgresql-parameters"></a>PostgreSQL 参数概述 

Azure Database for PostgreSQL 服务器在创建时预先配置了每个参数的最佳默认值。 静态参数需要重启服务器，而需要超级用户访问权限的参数则无法由用户配置。 

若要了解可以查看或修改哪些参数，建议先转到 Azure 门户，然后转到“服务器参数”页。 还可以使用 `ALTER DATABASE` 或 `ALTER ROLE` 命令，按用户或数据库来配置参数。

>[!NOTE]
> 由于 Azure Database for PostgreSQL 是一种托管数据库服务，因此没有为用户提供查看或修改配置文件（例如 `postgresql.conf`）的主机或 OS 访问权限。 该文件的内容会根据“服务器参数”页中的参数更改自动更新。

:::image type="content" source="./media/concepts-server-parameters/server-parameters.png" alt-text="服务器参数 - 门户":::

下面是一些参数的列表：

| 参数名称             | 说明 |
|----------------------|--------|
| **max_connections** | 可以在 Postgres 灵活服务器上优化 max_connections，可将其设置为 5,000 个连接。 有关更多详细信息，请参阅[限制文档](concepts-limits.md)。 | 
| shared_buffers    | “shared_buffers”设置因所选的 SKU 而异（SKU 决定可用的内存）。 “常规用途”服务器有 2GB 的 shared_buffers 用于 2 个 vCore；“内存优化”服务器有 4GB 的 shared_buffers 用于 2 个 vCore。 当层级中的 vCore 数增加时，shared_buffers 设置大致以线性方式扩展。 | 
| shared_preload_libraries | 此参数可用于配置预定义的一组受支持的扩展。 请注意，我们始终会加载 `azure` 扩展（用于维护任务），以及 `pg_stat_statements` 扩展（你可以使用 pg_stat_statements.track 参数来控制该扩展是否处于活动状态）。 |
| connection_throttling | 对于因密码无效而登录失败的次数过多的情形，可按 IP 启用或禁用临时连接限制。 |
| require_secure_transport | 如果应用程序不支持与服务器建立 SSL 连接，则可以选择通过将此参数值设置为 `OFF` 来禁用从客户端进行的安全传输。 |
 
## <a name="next-steps"></a>后续步骤

有关支持的 PostgreSQL 扩展的信息，请参阅[扩展文档](concepts-extensions.md)。
