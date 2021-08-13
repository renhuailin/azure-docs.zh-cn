---
title: 使用 sqlcmd 连接到 Synapse SQL
description: 使用 sqlcmd 命令行实用程序连接并查询无服务器 SQL 池和专用 SQL 池。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 3972b82c3477e6ac75574ce9110ad90435bbf8a3
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112538874"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>使用 sqlcmd 连接到 Synapse SQL

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql/get-started-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

在 Synapse SQL 中，可以使用 [sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) 命令行实用程序连接并查询无服务器 SQL 池和专用 SQL 池。  

## <a name="1-connect"></a>1.连接
若要开始使用 [sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true)，请打开命令提示符并输入 **sqlcmd**，后跟 Synapse SQL 数据库的连接字符串。 连接字符串需要以下参数：

* **服务器 (-S)：** 采用 `<`Server Name`>`.database.windows.net 格式的服务器
* **数据库 (-d)：** 数据库名称
* **启用带引号的标识符 (-I)：** 必须启用带引号的标识符才能连接到 Synapse SQL 实例

若要使用 SQL Server 身份验证，需要添加用户名和密码参数：

* **用户 (-U)：** 采用 `<`User`>` 格式的服务器用户
* **密码 (-P)：** 与用户关联的密码

连接字符串可能如以下示例所示：

**无服务器 SQL 池**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**专用 SQL 池**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

若要使用 Azure Active Directory 集成的身份验证，需要添加 Azure Active Directory 参数：

* **Azure Active Directory 身份验证 (-G)：** 使用 Azure Active Directory 进行身份验证

连接字符串可能如以下示例之一所示：

**无服务器 SQL 池**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**专用 SQL 池**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> 需要 [启用 Azure Active Directory 身份验证](../sql/active-directory-authentication.md) 才能使用 Active Directory 进行身份验证。

## <a name="2-query"></a>2.查询

### <a name="use-dedicated-sql-pool"></a>使用专用 SQL 池

连接后，可以对实例发出任何支持的 [Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) 语句。 在此示例中，查询是以交互模式提交的：

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

针对专用 SQL 池，以下示例展示了如何使用 -Q 选项或将 SQL 输送到 sqlcmd，以便在批处理模式下运行查询：

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>使用无服务器 SQL 池

连接后，可以对实例发出任何受支持的 [Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) 语句。  在以下示例中，查询是以交互模式提交的：

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

针对无服务器 SQL 池，以下示例展示了如何使用 -Q 选项或将 SQL 输送到 sqlcmd，以便在批处理模式下运行查询：

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>后续步骤

有关 sqlcmd 选项的详细信息，请参阅 [sqlcmd 文档](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true)。
