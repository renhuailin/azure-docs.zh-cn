---
title: 使用 .NET Core 连接和查询数据库
description: 本主题介绍如何使用 .NET Core 创建连接到 Azure SQL 数据库中数据库或 Azure SQL 托管实例的程序，并使用 Transact-SQL 语句对其进行查询。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 1d25f43ef5a694d8b94710055bf1be72a7fcb45c
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705202"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>快速入门：使用 .NET Core (C#) 查询数据库
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

在本快速入门中，将使用 [.NET Core](https://www.microsoft.com/net/) 和 C# 代码连接到数据库。 然后，将运行 Transact-SQL 语句来查询数据。

> [!TIP]
> 以下 Microsoft Learn 模块可帮助你免费学习如何[开发和配置可查询 Azure SQL 数据库中数据库的 ASP.NET 应用程序](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 已安装[适用于操作系统的 .NET Core](https://www.microsoft.com/net/core)。
- 可在其中运行查询的数据库。 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>创建新的 .NET Core 项目

1. 打开命令提示符，然后创建一个名为 sqltest 的文件夹。 导航到此文件夹，并运行以下命令。

    ```cmd
    dotnet new console
    ```

    此命令将创建新的应用项目文件，包括初始 C# 代码文件 (**Program.cs**)、XML 配置文件 (**sqltest.csproj**) 和所需的二进制文件。

2. 在文本编辑器中，打开 sqltest.csproj 并在 `<Project>` 标记之间粘贴以下 XML。 此 XML 会添加 `System.Data.SqlClient` 作为依赖项。

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>插入代码以查询 Azure SQL 数据库中的数据库

1. 在文本编辑器中打开 Program.cs 文件。

2. 将内容替换为以下代码，为服务器、数据库、用户名和密码添加相应的值。

> [!NOTE]
> 若要使用 ADO.NET 连接字符串，请将代码中设置服务器、数据库、用户名和密码的 4 行替换为以下行。 在字符串中，设置用户名和密码。
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       

                    String sql = "SELECT name, collation_name FROM sys.databases";

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>运行代码

1. 在提示符处，运行以下命令。

   ```cmd
   dotnet restore
   dotnet run
   ```

2. 验证是否返回了行。

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. 选择 Enter 关闭应用程序窗口。

## <a name="next-steps"></a>后续步骤

- [在 Windows/Linux/macOS 中通过命令行使用 .NET Core 入门](/dotnet/core/tutorials/using-with-xplat-cli)。
- 了解如何[使用 .NET Framework 和 Visual Studio 连接和查询 Azure SQL 数据库或 Azure SQL 托管实例](connect-query-dotnet-visual-studio.md)。  
- 了解如何[使用 SSMS 设计首个数据库](design-first-database-tutorial.md)，或者如何[使用 C# 和 ADO.NET 设计数据库并进行连接](design-first-database-csharp-tutorial.md)。
- 有关 .NET 的详细信息，请参阅 [.NET 文档](/dotnet/)。
