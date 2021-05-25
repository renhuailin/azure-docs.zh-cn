---
title: 创建用户 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 本文介绍如何创建新的用户帐户，以与 Azure Database for PostgreSQL 超大规模 (Citus) 进行交互。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/8/2019
ms.openlocfilehash: 3772918d1a7e505b3994afa7849568bb309bd0cf
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109715405"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>在 Azure Database for PostgreSQL 超大规模 (Citus) 中创建用户

## <a name="the-server-admin-account"></a>服务器管理员帐户

PostgreSQL 引擎使用[角色](https://www.postgresql.org/docs/current/sql-createrole.html)来控制对数据库对象的访问，新创建的超大规模 (Citus) 服务器组附带了几个预定义的角色：

* [默认 PostgreSQL 角色](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

由于超大规模 (Citus) 是托管的 PaaS 服务，因此只有 Microsoft 才能使用 `postgres` 超级用户角色登录。 对于有限的管理访问权限，超大规模 (Citus) 提供 `citus` 角色。

`citus` 角色的权限：

* 读取所有配置变量，甚至包括通常只有超级用户才能看到的变量。
* 读取所有 \_stat\_\* 视图，并使用各种统计信息相关的扩展，甚至包括通常只有超级用户才能看到的视图和扩展。
* 执行可能对表使用 ACCESS SHARE 锁定的监视函数，可能需要很长时间。
* [创建 PostgreSQL 扩展](concepts-hyperscale-extensions.md)（因为该角色是 `azure_pg_admin` 的成员）。

特别要注意的是，`citus` 角色有一些限制：

* 不能创建文件
* 不能创建数据库

## <a name="how-to-create-additional-user-roles"></a>如何创建其他用户角色

如前文所述，`citus` 管理员帐户没有创建其他用户的权限。 若要添加用户，请使用 Azure 门户界面。

1. 转到超大规模 (Citus) 服务器组的“角色”页，然后单击“+ 添加” ：

   :::image type="content" source="media/howto-hyperscale-create-users/1-role-page.png" alt-text="角色页":::

2. 输入角色名和密码。 单击“ **保存**”。

   :::image type="content" source="media/howto-hyperscale-create-users/2-add-user-fields.png" alt-text="添加角色":::

将在服务器组的协调器节点上创建用户，并将其传播到所有工作器节点。 通过 Azure 门户创建的角色具有 `LOGIN` 属性，这意味着它们是可以登录数据库的真实用户。

## <a name="how-to-modify-privileges-for-user-role"></a>如何修改用户角色的特权

新用户角色通常用于提供具有受限特权的数据库访问权限。 若要修改用户特权，请在 PgAdmin 或 psql 等工具中使用标准 PostgreSQL 命令。 （请参阅超大规模 (Citus) 快速入门中的[通过 psql 连接](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql)。）

例如，若要允许 `db_user` 读取 `mytable`，请授予权限：

```sql
GRANT SELECT ON mytable TO db_user;
```

超大规模 (Citus) 在整个群集中传播单表 GRANT 语句，将它们应用于所有工作器节点。 它还传播系统范围的 GRANT（例如，针对架构中的所有表）：

```sql
-- applies to the coordinator node and propagates to workers
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>如何删除用户角色或更改其密码

若要更新用户，请访问超大规模 (Citus) 服务器组的“角色”页，然后单击用户旁边的省略号 (...) 。 单击省略号后将打开一个菜单，用于删除用户或重置其密码。

   :::image type="content" source="media/howto-hyperscale-create-users/edit-role.png" alt-text="编辑角色":::

`citus` 角色具有特权，不能删除。

## <a name="next-steps"></a>后续步骤

打开防火墙以允许连接到新用户计算机的 IP 地址：[使用 Azure 门户创建和管理超大规模 (Citus) 防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)。

有关数据库用户帐户管理的详细信息，请参阅 PostgreSQL 产品文档：

* [数据库角色和特权](https://www.postgresql.org/docs/current/static/user-manag.html)
* [GRANT 语法](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [权限](https://www.postgresql.org/docs/current/static/ddl-priv.html)
