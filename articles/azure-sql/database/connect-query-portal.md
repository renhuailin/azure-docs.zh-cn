---
title: 在 Azure 门户中使用查询编辑器查询 SQL 数据库（预览）
description: 了解如何使用查询编辑器针对 Azure SQL 数据库中的数据库运行 Transact-SQL (T-SQL) 查询。
titleSuffix: Azure SQL Database
keywords: 连接到 sql 数据库, 查询 sql 数据库, azure 门户, 门户, 查询编辑器
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, contperf-fy21q3-portal
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: mathoma
ms.date: 03/01/2021
ms.openlocfilehash: 88ff2a523bb17af28dce0a45ef73af7da5b9d9e4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739717"
---
# <a name="quickstart-use-the-azure-portals-query-editor-preview-to-query-an-azure-sql-database"></a>快速入门：使用 Azure 门户的查询编辑器（预览）查询 Azure SQL 数据库
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

查询编辑器是 Azure 门户中的一个工具，用于针对 Azure SQL 数据库中的数据库或 Azure Synapse Analytics 中的数据仓库运行 SQL 查询。

在本快速入门中，你将使用查询编辑器针对数据库运行 Transact-SQL (T-SQL) 查询。

## <a name="prerequisites"></a>先决条件

### <a name="create-a-database-with-sample-data"></a>使用示例数据创建数据库

需要 AdventureWorksLT 示例数据库才能完成本快速入门。 如果在 SQL 数据库中没有 AdventureWorksLT 示例数据库的有效副本，请参阅以下快速入门来快速创建一个：

[快速入门：使用 Azure 门户、PowerShell 或 Azure CLI 创建 Azure SQL 数据库中的数据库](single-database-create-quickstart.md)

### <a name="set-an-azure-active-directory-admin-for-the-server-optional"></a>为服务器设置一个 Azure Active Directory 管理员（可选）

配置一个 Azure Active Directory (Azure AD) 管理员即可使用单个标识登录到 Azure 门户和数据库。 如果要使用 Azure AD 连接到查询编辑器，请执行以下步骤。

此过程是可选的，你可以改用 SQL 身份验证来连接到查询编辑器。

> [!NOTE]
> * Azure AD 管理员尚不支持电子邮件帐户（例如，outlook.com、gmail.com、yahoo.com 等）。 请确保选择在 Azure AD 中本机创建或联合到 Azure AD 中的用户。
> * Azure AD 管理员登录使用那些启用了双重身份验证的帐户，但查询编辑器不支持双重身份验证。

1. 在 Azure 门户中，导航到 SQL 数据库服务器。

2. 在“SQL Server”菜单中选择“Active Directory 管理员”。 

3. 在 SQL Server 的“Active Directory 管理员”页工具栏上，选择“设置管理员” 。

    ![选择 active directory](./media/connect-query-portal/select-active-directory.png)

4. 在“添加管理员”页上的搜索框中，输入要查找的用户或组，选择其作为管理员，然后选择“选择”按钮。 

5. 返回 SQL Server“Active Directory 管理员”页工具栏中，选择“保存” 。

## <a name="using-sql-query-editor"></a>使用 SQL 查询编辑器

1. 登录到 [Azure 门户](https://portal.azure.com/)并选择要查询的数据库。

2. 在“SQL 数据库”菜单中，选择“查询编辑器(预览)” 。

    ![查找查询编辑器](./media/connect-query-portal/find-query-editor.PNG)

### <a name="establish-a-connection-to-the-database"></a>与数据库建立连接

即使已登录到门户，也仍需提供凭据才能访问数据库。 可以使用 SQL 身份验证或 Azure Active Directory 连接到数据库。

#### <a name="connect-using-sql-authentication"></a>使用 SQL 身份验证进行连接

1. 在“登录”页上的“SQL Server 身份验证”下，输入有权访问数据库的用户的 **登录名** 和 **密码**。  如果你不确定，请使用数据库服务器的服务器管理员的登录名和密码。

    ![登录](./media/connect-query-portal/login-menu.png)

2. 选择“确定”。

#### <a name="connect-using-azure-active-directory"></a>使用 Azure Active Directory 进行连接

在“查询编辑器(预览)”中，查看“登录”页上的“Active Directory 身份验证”部分。 将会自动进行身份验证。因此，如果你是数据库的 Azure AD 管理员，则会看到一条消息，指出你已登录。 然后选择“以 \<your user or group ID> 继续”按钮。 如果页面中指出你未成功登录，可能需要刷新页面。

### <a name="query-a-database-in-sql-database"></a>在 SQL 数据库中查询数据库

以下示例查询应该针对 AdventureWorksLT 示例数据库成功运行。

#### <a name="run-a-select-query"></a>运行 SELECT 查询

1. 将以下查询粘贴到查询编辑器中：

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. 选择“运行”，然后查看“结果”窗格中的输出。 

   ![查询编辑器结果](./media/connect-query-portal/query-editor-results.png)

3. （可选）可将查询另存为 .sql 文件，或者将返回的数据导出为 .json、.csv 或 .xml 文件。

#### <a name="run-an-insert-query"></a>运行 INSERT 查询

运行以下 [INSERT](/sql/t-sql/statements/insert-transact-sql/) T-SQL 语句，以将新产品添加到 `SalesLT.Product` 表中。

1. 将之前的查询替换为此查询。

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. 选择“运行”在 `Product` 表中插入新行。 “消息”窗格显示“查询已成功：受影响的行：1” **。**


#### <a name="run-an-update-query"></a>运行 UPDATE 查询

运行以下 [UPDATE](/sql/t-sql/queries/update-transact-sql/) T-SQL 语句以修改新产品。

1. 将之前的查询替换为此查询。

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. 选择“运行”更新 `Product` 表中的指定行。 “消息”窗格显示“查询已成功：受影响的行：1” **。**

#### <a name="run-a-delete-query"></a>运行 DELETE 查询

运行以下 [DELETE](/sql/t-sql/statements/delete-transact-sql/) T-SQL 语句以删除新产品。

1. 将之前的查询替换为此查询：

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. 选择“运行”删除 `Product` 表中的指定行。 “消息”窗格显示“查询已成功：受影响的行：1” **。**


## <a name="troubleshooting-and-considerations"></a>疑难解答和注意事项

使用查询编辑器时，需要注意以下要点。

### <a name="configure-local-network-settings"></a>配置本地网络设置

如果查询编辑器中出现以下错误之一：
 - “本地网络设置可能正在阻止查询编辑器发出查询。请单击此处了解有关如何配置网络设置的说明”
 - “无法与服务器建立连接。这可能表示本地防火墙配置或网络代理设置存在问题”

这是因为查询编辑器使用端口 443 和 1443 进行通信。 你需要确保已在这些端口上启用出站 HTTPS 流量。 下面的说明会指导你完成此操作，具体取决于你的操作系统。 你可能需要与公司 IT 人员协作，以便授予在本地网络上打开此连接的权限。

#### <a name="steps-for-windows"></a>适用于 Windows 的步骤

1. 打开 Windows Defender 防火墙
2. 在左侧菜单上，选择“高级设置”
3. 在“高级安全 Windows Defender 防火墙”中的左侧菜单上，选择“出站规则”。 
4. 选择右侧菜单上的“新建规则...”

在“新建出站规则向导”中，执行以下步骤：

1. 选择“端口”作为要创建的规则的类型。 选择“下一步”
2. 选择“TCP”
3. 选择“特定远程端口”，并输入“443, 1443”。 然后，选择“下一步”
4. 选择“仅允许安全的连接”
5. 选择“下一步”，然后再次选择“下一步” 
5. 让“域”、“专用”和“公用”都保持选定状态
6. 为规则提供一个名称，例如“访问 Azure SQL 查询编辑器”，并根据需要提供说明。 然后选择“完成”

#### <a name="steps-for-mac"></a>适用于 Mac 的步骤
1. 打开“系统首选项”（Apple 菜单 >“系统首选项”）。
2. 单击“安全和隐私”。
3. 单击“防火墙”。
4. 如果防火墙处于禁用状态，请选择底部的“单击此锁进行更改”，然后选择“启用防火墙”
4. 单击“防火墙选项”。
5. 在“安全和隐私”窗口中选择此选项：“自动允许已签名的软件接收传入连接。”

#### <a name="steps-for-linux"></a>适用于 Linux 的步骤
运行以下命令以更新 iptables
  ```
  sudo iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT
  sudo iptables -A OUTPUT -p tcp --dport 1443 -j ACCEPT
  ```

### <a name="connection-considerations"></a>连接注意事项

* 对于与查询编辑器的公共连接，你需要[将出站 IP 地址添加到服务器已允许的防火墙规则中](firewall-create-server-level-portal-quickstart.md)，以访问数据库和数据仓库。

* 如果你在服务器上设置了专用链接连接，并且是从专用虚拟网络中的 IP 连接到查询编辑器，则查询编辑器将正常工作，无需你将客户端 IP 地址添加到 SQL 数据库服务器防火墙规则中。

* 使用查询编辑器所需的最基本 RBAC 权限是对服务器和数据库的“读取”访问权限。 具有此级别访问权限的任何人都可以访问查询编辑器功能。 若要限制特定用户的访问，必须阻止他们使用 Azure Active Directory 或 SQL 身份验证凭据登录到查询编辑器。 如果他们无法将自己分配为服务器的 AAD 管理员，或者无法访问/添加 SQL 管理员帐户，则他们将不能使用查询编辑器。

* 查询编辑器不支持连接到 `master` 数据库。

* 查询编辑器无法使用 `ApplicationIntent=ReadOnly` 连接到副本数据库

* 如果看到了“无法验证 X-CSRF-Signature 标头”错误消息，请执行以下操作来解决问题：

    * 请确保将计算机的时钟设置为正确的时间和时区。 你还可以尝试通过搜索实例位置所在的时区（例如“美国东部”、“太平洋”等），将计算机的时区与 Azure 匹配。
    * 如果使用的是代理网络，请确保未修改或删除请求标头“X-CSRF-Signature”。

### <a name="other-considerations"></a>其他注意事项

* 按 **F5** 刷新查询编辑器页，任何正在处理的查询都将丢失。

* 查询执行的超时为 5 分钟。

* 查询编辑器仅支持地理数据类型的柱面投影。

* 数据库表和视图不支持 IntelliSense，但编辑器支持自动补全已键入的名称。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure SQL 数据库支持的 Transact-SQL (T-SQL)，请参阅[解决迁移到 SQL 数据库过程中的 Transact-SQL 差异](transact-sql-tsql-differences-sql-server.md)。
