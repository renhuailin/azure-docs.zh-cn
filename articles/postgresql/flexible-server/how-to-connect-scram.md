---
title: 在 Azure Database for PostgreSQL - 灵活服务器中使用 SCRAM 进行连接
description: 有关如何在 Azure Database for PostgreSQL - 灵活服务器中使用 SCRAM 进行配置和连接的说明与信息。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/23/2021
ms.openlocfilehash: 7fc67ab7162c7fe62df226fd39aefcd350bbdf82
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112897233"
---
# <a name="scram-authentication-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 灵活服务器中的 SCRAM 身份验证

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

加盐质询响应身份验证机制 (SCRAM) 是一种基于密码的相互身份验证协议。 它是一种质询-响应方案，其中添加了多个安全级别，可防止在不受信任的连接中进行密码探查。 SCRAM 支持在服务器上以加密哈希的形式存储密码，从而提供高级安全性。 

若要使用 SCRAM 身份验证方法来访问 PostgreSQL 数据库服务器，客户端库需要支持 SCRAM。  请参阅支持 SCRAM 的[驱动程序列表](https://wiki.postgresql.org/wiki/List_of_drivers)。

## <a name="configuring-scram-authentication"></a>配置 SCRAM 身份验证

1. 将 password_encryption 更改为 SCRAM-SHA-256。 PostgreSQL 目前仅支持使用 SHA-256 的 SCRAM。
        :::image type="content" source="./media/how-to-configure-scram/1-password-encryption.png" alt-text="启用 SCRAM 密码加密"::: 
2. 允许将 SCRAM-SHA-256 用作身份验证方法。
        :::image type="content" source="./media/how-to-configure-scram/2-auth-method.png" alt-text="选择身份验证方法"::: 
    >[!Important]
    > 可以选择强制实施仅限 SCRAM 的身份验证，只需选择 SCRAM-SHA-256 方法即可。 这样，使用 MD5 身份验证方法的用户不再可以连接到服务器。 因此，在强制实施 SCRAM 并将所有用户密码更新为 SCRAM-SHA-256 之前，建议同时使用 MD5 和 SCRAM-256 作为身份验证方法。 可以使用步骤 #7 中所述的查询来验证用户的身份验证类型。
3. 保存更改。 这些属性是动态的，无需重启服务器。
4. 从 Postgres 客户端连接到 Postgres 服务器。 例如，
   
    ```bash
    psql "host=myPGServer.postgres.database.azure.com port=5432 dbname=postgres user=myDemoUser password=MyPassword sslmode=require"

    psql (12.3 (Ubuntu 12.3-1.pgdg18.04+1), server 12.6)
    SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
    Type "help" for help.
    ```

5. 验证密码加密。
   
    ```SQL
    postgres=> show password_encryption;
     password_encryption
    ---------------------
    scram-sha-256
    (1 row)
    ```

6. 然后可以更新用户的密码。

    ```SQL
    postgres=> \password myDemoUser
    Enter new password:
    Enter it again:
    postgres=>
    ```

7. 可以使用 `azure_roles_authtype()` 函数验证用户身份验证类型。 

    ``` SQL
    postgres=> SELECT * from azure_roles_authtype();
            rolename          | authtype
    ---------------------------+-----------
    azuresu                   | NOLOGIN
    pg_monitor                | NOLOGIN
    pg_read_all_settings      | NOLOGIN
    pg_read_all_stats         | NOLOGIN
    pg_stat_scan_tables       | NOLOGIN
    pg_read_server_files      | NOLOGIN
    pg_write_server_files     | NOLOGIN
    pg_execute_server_program | NOLOGIN
    pg_signal_backend         | NOLOGIN
    replication               | NOLOGIN
    myDemoUser                | SCRAM-256
    azure_pg_admin            | NOLOGIN
    srtest                    | SCRAM-256
    sr_md5                    | MD5
    (14 rows)
    ```

8. 然后可以从支持 SCRAM 身份验证的客户端连接到服务器。

## <a name="next-steps"></a>后续步骤
- [使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-cli.md)。
- 详细了解 [Azure Database for PostgreSQL 灵活服务器中的网络](./concepts-networking.md)
- 详细了解 [Azure Database for PostgreSQL 灵活服务器防火墙规则](./concepts-networking.md#public-access-allowed-ip-addresses)
