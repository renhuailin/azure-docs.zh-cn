---
title: 使用 Python 查询数据库
description: 本主题介绍如何使用 Python 创建连接到 Azure SQL 数据库中的数据库的程序并使用 Transact-SQL 语句对其进行查询。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: connect
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: dzsquared
ms.author: drskwier
ms.reviewer: mathoma
ms.date: 12/19/2020
ms.openlocfilehash: ca6f5b9107daa321f6451ca63100f493aa081ba2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114466985"
---
# <a name="quickstart-use-python-to-query-a-database"></a>快速入门：使用 Python 查询数据库

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

在本快速入门中，你将使用 Python 连接到 Azure SQL 数据库、Azure SQL 托管实例或 Synapse SQL 数据库，并使用 T-SQL 语句来查询数据。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- 将在其中运行查询的数据库。

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 和相关软件
    

    |**操作**|**macOS**|**Ubuntu**|**Windows**|
    |----------|-----------|------------|---------|
    |安装用于 SQL Server 的 ODBC 驱动程序、SQLCMD 和 Python 驱动程序|执行[在 macOS 上使用 SQL Server 创建 Python 应用](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)中的步骤 1.2、1.3 和 2.1  。 这也会安装 Homebrew 和 Python。       |[配置用于 pyodbc Python 开发的环境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)|[配置用于 pyodbc Python 开发的环境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)。|
    |安装 Python 和其他所需的包|    |使用 `sudo apt-get install python python-pip gcc g++ build-essential`。|    |
    |更多信息|[macOS 上的 Microsoft ODBC 驱动程序](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)  |[Linux 上的 Microsoft ODBC 驱动程序](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|[Linux 上的 Microsoft ODBC 驱动程序](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|



若要进一步了解 Python 和 Azure SQL 数据库中的数据库，请参阅[适用于 Python 的 Azure SQL 数据库库](/python/api/overview/azure/sql)、[pyodbc 存储库](https://github.com/mkleehammer/pyodbc/wiki/)和 [pyodbc 示例](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)。

## <a name="create-code-to-query-your-database"></a>创建用于查询数据库的代码 

1. 在文本编辑器中，创建新文件 sqltest.py。  
   
1. 添加以下代码。 从先决条件部分获取连接信息，并将 \<server>、\<database>、\<username> 和 \<password> 的值替换为自己的值。
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '{<password>}'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER=tcp:'+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>运行代码

1. 请在命令提示符处运行以下命令：

   ```cmd
   python sqltest.py
   ```

1. 确认已返回数据库及其排序规则，然后关闭命令窗口。

## <a name="next-steps"></a>后续步骤

- [在 Azure SQL 数据库中设计第一个数据库](design-first-database-tutorial.md)
- [用于 SQL Server 的 Microsoft Python 驱动程序](/sql/connect/python/python-driver-for-sql-server/)
- [Python 开发人员中心](https://azure.microsoft.com/develop/python/?v=17.23h)
