---
title: 使用具有 .NET 和 C# 的 Visual Studio 进行查询
description: 使用 Visual Studio 创建与 Azure SQL 数据库中的数据库或 Azure SQL 托管实例连接并运行查询的 C# 应用。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 1d8859f4790610e72ad517f74bbbbf0cf77d9316
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705194"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>快速入门：使用 Visual Studio 中的 .NET 和 C# 来连接和查询数据库
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

本快速入门展示了如何使用 Visual Studio 中的 [.NET Framework](https://www.microsoft.com/net/) 和 C# 代码通过 Transact-SQL 语句查询 Azure SQL 或 Synapse SQL 中的数据库。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community、Professional 或 Enterprise 版本。
- 可在其中运行查询的数据库。

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>创建用于查询 Azure SQL 数据库中的数据库的代码

1. 在 Visual Studio 中，创建新的项目。 
   
1. 在“新建项目”对话框中，选择“Visual C#”，然后选择“控制台应用(.NET Framework)”  。
   
1. 输入“sqltest”作为项目名称，然后选择“确定”。 创建新项目。 
   
1. 选择“项目” > “管理 NuGet 包” 。 
   
1. 在“NuGet 包管理器”中，选择“浏览”选项卡，然后搜索并选择“Microsoft.Data.SqlClient”  。
   
1. 在“Microsoft.Data.SqlClient”页上选择“安装” 。 
   - 如果出现提示，请选择“确定”继续安装。 
   - 如果显示“接受许可证”窗口，则选择“我接受” 。
   
1. 安装完成后，可以关闭“NuGet 包管理器”。 
   
1. 在代码编辑器中，将 Program.cs 内容替换为以下代码。 替换 `<your_server>`、`<your_username>`、`<your_password>` 和 `<your_database>` 的值。
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
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
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
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
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>运行代码

1. 若要运行该应用，请选择“调试” > “开始调试”，或选择工具栏上的“开始”，或按 F5   。
1. 验证是否返回了数据库名称和排序规则，然后关闭应用窗口。

## <a name="next-steps"></a>后续步骤

- 了解如何在 Windows/Linux/macOS 中[使用 .NET Core 连接和查询 Azure SQL 数据库中的数据库](connect-query-dotnet-core.md)。  
- 了解[在 Windows/Linux/macOS 中通过命令行使用 .NET Core 入门](/dotnet/core/tutorials/using-with-xplat-cli)。
- 了解如何[使用 SSMS 在 Azure SQL 数据库中设计你的第一个数据库](design-first-database-tutorial.md)，或者如何[使用 .NET 在 Azure SQL 数据库中设计你的第一个数据库](design-first-database-csharp-tutorial.md)。
- 有关 .NET 的详细信息，请参阅 [.NET 文档](/dotnet/)。
- 重试逻辑示例：[使用 ADO.NET 弹性连接到 Azure SQL][step-4-connect-resiliently-to-sql-with-ado-net-a78n]。


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
