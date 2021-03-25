---
title: 快速入门：Fivetran 和专用 SQL 池（以前称为 SQL DW）
description: 开始将 Fivetran 与 Azure Synapse Analytics 中的专用 SQL 池（以前称为 SQL DW）配合使用。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f332c3b0bd53d80d4a8471f53c56ecab611787c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96456377"
---
# <a name="quickstart-fivetran-with-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>快速入门：将 Fivetran 与 Azure Synapse Analytics 中的专用 SQL 池（以前称为 SQL DW）配合使用 

本快速入门介绍如何设置一个新的可以使用专用 SQL 池（以前称为 SQL DW）的 Fivetran 用户。 本文假设你有一个现有的专用 SQL 池（以前称为 SQL DW）。

## <a name="set-up-a-connection"></a>设置连接

1. 查找用于连接到专用 SQL 池（以前称为 SQL DW）的完全限定的服务器名和数据库名。
    
    如果在查找该信息时需要帮助，请参阅[连接到专用 SQL 池（以前称为 SQL DW）](sql-data-warehouse-connection-strings.md)。

2. 在安装向导中，选择是要直接连接数据库还是通过 SSH 隧道进行连接。

   如果选择直接连接数据库，则必须创建防火墙规则，用于允许访问。 此方法是最简单且最安全的方法。

   如果选择通过 SSH 隧道进行连接，Fivetran 会连接到网络中一个单独的服务器。 服务器提供一个连接到数据库的 SSH 隧道。 如果数据库位于虚拟网络上一个无法访问的子网中，则必须使用此方法。

3. 在服务器级别防火墙中添加 IP 地址 52.0.2.4，以允许从 Fivetran 到专用 SQL 池（以前称为 SQL DW）实例的传入连接。

   有关详细信息，请参阅[创建服务器级防火墙规则](create-data-warehouse-portal.md#create-a-server-level-firewall-rule)。

## <a name="set-up-user-credentials"></a>设置用户凭据

1. 使用 SQL Server Management Studio (SSMS) 或你喜欢使用的工具连接到专用 SQL 池（以前称为 SQL DW）。 以服务器管理员用户身份登录。 然后，运行以下 SQL 命令，为 Fivetran 创建一个用户：

    - 在 master 数据库中： 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - 在专用 SQL 池（以前称为 SQL DW）数据库中：

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. 向 Fivetran 用户授予对专用 SQL 池（以前称为 SQL DW）的以下权限：

    ```sql
    GRANT CONTROL to fivetran;
    ```

    创建数据库范围的凭据需要 CONTROL 权限，而用户在通过 PolyBase 从 Azure Blob 存储加载文件时会使用该凭据。

3. 向 Fivetran 用户添加适当的资源类。 所用资源类取决于创建列存储索引时需要的内存。 例如，与 Marketo 和 Salesforce 之类的产品集成时由于产品使用的列数较多且数据量较大，需要较高级别的资源类。 较高级别的资源类需要更多内存来创建列存储索引。

    建议使用静态资源类。 可以从 `staticrc20` 资源类着手。 资源类 `staticrc20` 为每个用户分配 200 MB，不考虑你所使用的性能级别。 如果列存储索引无法使用初始资源类级别，请提高资源类的级别。

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    有关详细信息，请阅读[内存和并发限制](memory-concurrency-limits.md)和[资源类](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)。


## <a name="connect-from-fivetran"></a>从 Fivetran 连接

若要从 Fivetran 帐户连接到专用 SQL 池（以前称为 SQL DW），请输入用于访问专用 SQL 池（以前称为 SQL DW）的凭据： 

* 主机（服务器名称）。
* 端口。
* 数据库。
* 用户（用户名应该为 fivetran\@server_name，其中 server_name 是 Azure 主机 URI server\_name.database.windows.net 的一部分）。
* Password。
