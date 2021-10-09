---
title: 注册和扫描本地 SQL Server
description: 本教程介绍如何在 Azure Purview 中使用自承载 IR 扫描本地 SQL Server。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 4cef99adecadc73f105dfffcdc72163c8b622cc3
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208625"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>注册和扫描本地 SQL Server

本文概述如何在 Purview 中注册 SQL Server 数据源并对其设置扫描。

## <a name="supported-capabilities"></a>支持的功能

SQL Server 本地数据源支持以下功能：

- 完全和增量扫描，以捕获本地网络或 Azure VM 上安装的 SQL Server 中的元数据和分类。

- 在数据资产之间建立链接，以用于 ADF 复制/数据流活动

SQL Server 本地数据源支持：

- 从 SQL Server 2000 到 SQL Server 2019 之间的每个版本

- 身份验证方法：SQL 身份验证

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，先创建一个 Azure Purview 帐户。 有关创建 Purview 帐户的详细信息，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。

- 设置[自承载集成运行时](manage-integration-runtimes.md)以扫描数据源。

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

为本地 SQL Server 设置身份验证只有一种方法：

- SQL 身份验证

### <a name="sql-authentication"></a>SQL 身份验证

SQL 帐户必须拥有访问 master 数据库的权限。 这是因为 `sys.databases` 位于 master 数据库中。 要查找服务器上的所有 SQL 数据库，Purview 扫描程序需要枚举 `sys.databases`。

#### <a name="creating-a-new-login-and-user"></a>创建新登录名和用户

如果要创建新的登录名和用户以扫描 SQL Server，请执行以下步骤：

> [!Note]
   > 以下所有步骤均可使用[此处](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql)提供的代码执行

1. 导航到 SQL Server Management Studio (SSMS)，连接到服务器，导航到“安全性”，选择并按住（或右键单击）“登录”并创建新登录名。 请确保选择“SQL 身份验证”。

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="创建新登录名和用户。":::

2. 在左侧导航栏中选择“服务器角色”，并确保已分配 public 角色。

3. 在左侧导航栏中选择“用户映射”，在“映射”中选择所有数据库，然后选择数据库角色：db_datareader。

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="用户映射。":::

4. 选择“确定”进行保存。

5. 选择并按住（或右键单击）“属性”，再次导航到所创建的用户。 输入新密码并进行确认。 选择“指定旧密码”，然后输入旧密码。 创建新登录名后需要尽快更改密码。

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="更改密码。":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>将 SQL 登录密码存储到密钥保管库并在 Purview 中创建凭据

1. 在 Azure 门户1 中，导航到密钥保管库。 选择“设置”>“机密”
1. 选择“+ 生成/导入”并在登录 SQL Server 时输入“名称”和“值”作为密码  
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，请使用用户名和密码[创建新凭据](manage-credentials.md#create-a-new-credential)以设置扫描 

## <a name="register-a-sql-server-data-source"></a>注册 SQL Server 数据源

1. 导航到你的 Purview 帐户

1. 在左侧导航栏的“源”和“扫描”下，选择“集成运行时”。 确保设置了自承载集成运行时。 如果未设置，请按照[此处](manage-integration-runtimes.md)所述的步骤创建自承载集成运行时，以扫描可以访问本地网络的本地或 Azure VM。

1. 在左侧导航区域中选择“源”。

1. 选择“注册”

1. 选择“SQL Server”，然后选择“继续”

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="设置 SQL 数据源。":::

5. 提供一个友好名称和服务器终结点，然后选择“完成”以注册数据源。 例如，如果你的 SQL Server FQDN 是 foobar.database.windows.net，则输入 foobar 作为服务器终结点。

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 在 [Purview Studio](https://web.purview.azure.com/resource/) 的左窗格中选择“数据映射”选项卡。

1. 选择已注册的 SQL Server 源。

1. 选择“新建扫描”

1. 选择要连接到数据源的凭据。

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-set-up-scan.png" alt-text="设置扫描":::

1. 通过在列表中选择适当的项，可以将扫描范围限定到特定的表。

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="限定扫描范围":::

1. 然后选择扫描规则集。 可以在系统默认项和现有的自定义规则集之间选择，或者可以以内联方式创建新规则集。

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="扫描规则集":::

1. 选择扫描触发器。 可以设置一个计划或运行一次扫描。

   :::image type="content" source="media/register-scan-on-premises-sql-server/trigger-scan.png" alt-text="trigger":::

1. 查看扫描并选择“保存并运行”。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
