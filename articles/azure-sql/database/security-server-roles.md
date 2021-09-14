---
title: 服务器角色
titleSuffix: Azure SQL Database
description: 本文概述了 Azure SQL 数据库的逻辑服务器的服务器角色
ms.service: sql-database
ms.subservice: security
author: AndreasWolter
ms.author: anwolter
ms.topic: conceptual
ms.date: 09/02/2021
ms.reviewer: vanto
ms.openlocfilehash: 64c012373187059792174618920b797b2d081aa2
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537195"
---
# <a name="azure-sql-database-server-roles-for-permission-management"></a>用于权限管理的 Azure SQL 数据库服务器角色

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

在 Azure SQL 数据库中，服务器是一个逻辑概念，不能在服务器级别授予权限。 为了简化权限管理，Azure SQL 数据库提供了一组固定的服务器级角色来帮助管理[逻辑服务](logical-servers.md)器上的权限。 角色是对登录进行分组的安全主体。

> [!NOTE]
> 本文中所说的“角色”概念类似于 Windows 操作系统中的“组”。 

这些特殊的固定服务器级角色使用前缀 ##MS_ 和后缀 ## 来区分 其他常规用户创建的主体。 

与本地 SQL Server 一样，服务器权限也是分层组织的。 这些服务器级角色拥有的权限可以传播到数据库权限。 为了将权限有效地传播到数据库，登录需要在数据库中有一个用户帐户。

例如，服务器级别角色 ##MS_ServerStateReader## 具有“查看服务器状态”权限。  如果作为此角色成员的登录名在 master 和 WideWorldImporters 数据库中具有用户帐户，则该用户将在这两个数据库中拥有“查看数据库状态”权限。  

> [!NOTE]
> 可以在用户数据库中拒绝任何权限，效果等同于通过角色成员资格覆盖服务器范围的授权。 但是，在系统数据库 master 中，不能授予或拒绝权限。

Azure SQL 数据库目前提供三个固定的服务器角色。 无法更改被授予了固定服务器角色的权限，并且这些角色不能以其他固定角色作为成员。 可以将服务器级别的 SQL 登录名作为成员添加到服务器级别角色。

> [!IMPORTANT]
> 固定服务器角色的每个成员都可以将其他登录名添加到该同一角色。

有关 Azure SQL 数据库登录名和用户的详细信息，请参阅[向数据库授予对 Azure SQL 数据库、SQL 托管实例和 Azure Synapse Analytics 的访问权限](logins-create-manage.md)。
  
## <a name="built-in-server-level-roles"></a>内置服务器级角色

下表显示了服务器级的固定角色及其权限。  
  
|内置服务器级角色|说明|  
|------------------------------|-----------------|  
|##MS_DefinitionReader##|##MS_DefinitionReader## 固定服务器角色的成员可以读取“查看任何定义”覆盖的所有目录视图，分别是在该角色的成员具有用户帐户的任何数据库上“查看定义”。  |  
|##MS_ServerStateReader##|##MS_ServerStateReader## 固定服务器角色的成员可以读取“查看服务器状态”所涵盖的所有动态管理视图 (DMV) 和功能，分别是该角色的成员具有用户帐户的任何数据库上的“查看数据库状态”。  |
|##MS_ServerStateManager##|##MS_ServerStateManager## 固定服务器角色的成员与##MS_ServerStateReader## 角色具有相同的权限。  此外，该角色还具有“变更服务器状态”权限，该权限允许访问多个管理操作，例如：`DBCC FREEPROCCACHE`、`DBCC FREESYSTEMCACHE ('ALL')`、`DBCC SQLPERF()`； |  


## <a name="permissions-of-fixed-server-roles"></a>固定服务器角色的权限

每个内置的服务器级角色都分配有特定的权限。 下表显示了分配给服务器级角色的权限。 如果数据库中存在用户帐户，则还会显示继承的数据库级权限。
  
|服务器级的固定角色|服务器级别权限|数据库级权限（如果数据库用户存在）  
|-------------|----------|-----------------|  
|##MS_DefinitionReader##|查看任何数据库、查看任何定义、查看任何安全定义|查看定义，查看安全定义|  
|##MS_ServerStateReader##|查看服务器状态、查看服务器性能状态、查看服务器安全状态|查看数据库状态、查看数据库性能状态、查看数据库安全状态|  
|##MS_ServerStateManager##|变更服务器状态、查看服务器状态、查看服务器性能状态、查看服务器安全状态|查看数据库状态、查看数据库性能状态、查看数据库安全状态|   
  
  
## <a name="working-with-server-level-roles"></a>使用服务器级角色

下表介绍了可以 Azure SQL 数据库中可用于服务器级角色的系统视图和功能。  
  
|功能|类型|说明|  
|-------------|----------|-----------------|  
|[IS_SRVROLEMEMBER (Transact-SQL)](/sql/t-sql/functions/is-srvrolemember-transact-sql)|元数据|指示 SQL 登录名是否为指定服务器级角色的成员。|  
|[sys.server_role_members (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-server-role-members-transact-sql)|元数据|为每个服务器级角色的每个成员返回一行。|
|[sys.sql_logins (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-sql-logins-transact-sql)|元数据|为每个 SQL 登录名返回一行。|
|[ALTER SERVER ROLE (Transact-SQL)](/sql/t-sql/statements/alter-server-role-transact-sql)|Command|更改服务器角色的成员资格。| 

## <a name="examples"></a><a name="_examples"></a> 示例

本节中的示例展示了如何在 Azure SQL 数据库中使用服务器级角色。  

### <a name="a-adding-a-sql-login-to-a-server-level-role"></a>A. 向服务器级角色添加 SQL 登录名

以下示例将 SQL 登录名“Jiao”添加到服务器级角色 ##MS_ServerStateReader##。 该语句必须在虚拟主数据库中运行。
  
```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao;  
GO
```  

### <a name="b-listing-all-principals-sql-authentication-which-are-members-of-a-server-level-role"></a>B. 列出作为服务器级角色成员的所有主体（SQL 身份验证）

以下语句使用 `sys.server_role_members` 和 `sys.sql_logins` 目录视图返回任何固定服务器级角色的所有成员。 该语句必须在虚拟主数据库中运行。
  
```sql  
SELECT
        sql_logins.principal_id         AS MemberPrincipalID
    ,   sql_logins.name                 AS MemberPrincipalName
    ,   roles.principal_id              AS RolePrincipalID
    ,   roles.name                      AS RolePrincipalName
FROM sys.server_role_members AS server_role_members
INNER JOIN sys.server_principals AS roles
    ON server_role_members.role_principal_id = roles.principal_id
INNER JOIN sys.sql_logins AS sql_logins 
    ON server_role_members.member_principal_id = sql_logins.principal_id
;  
GO  
```  
### <a name="c-complete-example-adding-a-login-to-a-server-level-role-retrieving-metadata-for-role-membership-and-permissions-and-running-a-test-query"></a>C. 完整示例：向服务器级角色添加登录名，检索角色成员资格和权限的元数据，并运行测试查询

#### <a name="part-1-preparing-role-membership-and-user-account"></a>第 1 部分：准备角色成员资格和用户帐户

从虚拟主数据库运行此命令。

```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao

-- check membership in metadata:
select IS_SRVROLEMEMBER('##MS_ServerStateReader##', 'Jiao')
--> 1 = Yes

SELECT
        sql_logins.principal_id         AS MemberPrincipalID
    ,   sql_logins.name                 AS MemberPrincipalName
    ,   roles.principal_id              AS RolePrincipalID
    ,   roles.name                      AS RolePrincipalName
FROM sys.server_role_members AS server_role_members
INNER JOIN sys.server_principals AS roles
    ON server_role_members.role_principal_id = roles.principal_id
INNER JOIN sys.sql_logins AS sql_logins 
    ON server_role_members.member_principal_id = sql_logins.principal_id
;   
GO  
``` 

下面是结果集。
  
```
MemberPrincipalID MemberPrincipalName RolePrincipalID RolePrincipalName        
------------- ------------- ------------------ -----------   
6         Jiao      11            ##MS_ServerStateReader##   
```  

从用户数据库运行此命令。

```sql  
-- Creating a database-User for 'Jiao'
CREATE USER Jiao
    FROM LOGIN Jiao
;   
GO  
``` 

#### <a name="part-2-testing-role-membership"></a>第 2 部分：测试角色成员资格

以登录名 `Jiao` 身份登录并连接到示例中使用的用户数据库。

```sql  
-- retrieve server-level permissions of currently logged on User
SELECT * FROM sys.fn_my_permissions(NULL, 'Server')
;  

-- check server-role membership for `##MS_ServerStateReader##` of currently logged on User
SELECT USER_NAME(), IS_SRVROLEMEMBER('##MS_ServerStateReader##')
--> 1 = Yes

-- Does the currently logged in User have the `VIEW DATABASE STATE`-permission?
SELECT HAS_PERMS_BY_NAME(NULL, 'DATABASE', 'VIEW DATABASE STATE'); 
--> 1 = Yes

-- retrieve database-level permissions of currently logged on User
SELECT * FROM sys.fn_my_permissions(NULL, 'DATABASE')
GO 

-- example query:
SELECT * FROM sys.dm_exec_query_stats
--> will return data since this user has the necessary permission

``` 

## <a name="limitations-of-server-level-roles"></a>服务器级角色的限制

- 角色分配最多可能需要 5 分钟才能生效。 同样对于现有会话，在关闭并重新打开连接之前，对服务器角色分配的更改不会生效。 这是由于 master 数据库和同一逻辑服务器上的其他数据库之间的分布式架构。
  - 部分解决方法：为了缩短等待周期并确保服务器角色分配在数据库中是最新的，服务器管理员或 Azure AD 管理员可以在登录名有权访问的用户数据库中运行 `DBCC FLUSHAUTHCACHE`。 当前登录的用户在运行 `DBCC FLUSHAUTHCACHE` 后仍需要重新连接，以使成员资格更改对这些用户生效。

- master 数据库中不支持 `IS_SRVROLEMEMBER()`。


## <a name="see-also"></a>另请参阅

- [数据库级别的角色](/sql/relational-databases/security/authentication-access/database-level-roles)   
- [安全性目录视图 (Transact-SQL)](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)   
- [安全函数 (Transact-SQL)](/sql/t-sql/functions/security-functions-transact-sql)   
- [权限（数据库引擎）](/sql/relational-databases/security/permissions-database-engine)
- [DBCC FLUSHAUTHCACHE (Transact SQL)](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql)
