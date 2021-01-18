---
title: 注册并扫描本地 SQL server
description: 本教程介绍如何使用自承载 IR 扫描本地 SQL server。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: 0d282ee805ac61ba17ceb3ecc6a3d8179ea7b319
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555893"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>注册并扫描本地 SQL server

本文概述了如何在监控范围中注册 SQL server 数据源并在其中设置扫描。

## <a name="supported-capabilities"></a>支持的功能

SQL server 本地数据源支持以下功能：

- 用于捕获本地网络或 Azure VM 上安装的 SQL server 中的元数据和分类的 **完全和增量扫描**。

- ADF 复制/数据流活动的数据资产之间的 **沿袭**

SQL server 本地数据源支持：

- sql server 2019 中的每个 SQL 版本返回到 SQL server 2000

- 身份验证方法： SQL 身份验证

### <a name="known-limitations"></a>已知限制

Azure 监控范围不支持对 SQL Server 中的 [视图](/sql/relational-databases/views/views) 进行扫描。

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，请创建一个 Azure 监控范围帐户。 有关创建监控范围帐户的详细信息，请参阅 [快速入门：创建 Azure 监控范围帐户](create-catalog-portal.md)。

- 设置 [自承载集成运行时](manage-integration-runtimes.md) 以扫描数据源。

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

仅有一种方法可为本地 SQL server 设置身份验证：

- SQL 身份验证

### <a name="sql-authentication"></a>SQL 身份验证

SQL 标识必须有权访问主数据库。 此位置是存储的位置 `sys.databases` 。 监控范围扫描程序需要枚举才能 `sys.databases` 查找服务器中的所有 SQL 数据库实例。

#### <a name="using-an-existing-server-administrator"></a>使用现有的服务器管理员

如果计划使用现有服务器管理员 (sa) 用户扫描本地 SQL server，请确保以下各项：

1. `sa` 不是 Windows 身份验证类型。

2. 你计划使用的服务器级别用户必须具有 "公共" 和 "sysadmin" 的服务器角色。 你可以通过以下方式进行验证：导航到 SQL Server Management Studio (SSMS) ，连接到服务器，导航到 "安全性"，选择你计划使用的登录名，右键单击 " **属性** "，然后选择 " **服务器角色**"。

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="服务器级别的登录名。":::

3. 数据库将映射到对每个数据库具有至少 db_datareader 级别访问权限的用户。

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping-sa.png" alt-text="sa 的用户映射。":::

#### <a name="creating-a-new-login-and-user"></a>创建新的登录名和用户

如果要创建新的登录名和用户以扫描 SQL server，请执行以下步骤：

1. 导航到 SQL Server Management Studio (SSMS) ，连接到服务器，导航到 "安全性"，右键单击 "登录" 并创建新的登录名。 请确保选择 "SQL 身份验证"。

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="创建新的登录名和用户。":::

2. 在左侧导航栏中选择 "服务器角色"，并选择 "公共" 和 "sysadmin"。

3. 选择左侧导航栏上的 "用户映射"，并选择映射中的所有数据库。

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="用户映射。":::

4. 单击“确定”进行保存  。

5. 通过右键单击并选择 " **属性**"，再次导航到所创建的用户。 输入新密码并进行确认。 选择 "指定旧密码"，然后输入旧密码。 **创建新的登录名后，需要更改密码。**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="更改密码。":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>在密钥保管库中存储 SQL 登录密码，并在监控范围中创建凭据

1. 在 Azure 门户中，导航到密钥保管库
1. 选择“设置”>“机密”
1. 选择 " **+ 生成/导入**"，并输入 SQL Server 登录 **名** 和 **值** 作为 *密码*
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，请使用用户名和密码[创建新凭据](manage-credentials.md#create-a-new-credential)以设置扫描 

## <a name="register-a-sql-server-data-source"></a>注册 SQL server 数据源

1. 导航到你的 Purview 帐户

1. 在左侧导航栏中，选择 " **集成运行时**"。 确保设置了自承载集成运行时。 如果未设置，请按照 [此处](manage-integration-runtimes.md) 所述的步骤创建自承载集成运行时，以便在可访问本地网络的本地或 Azure VM 上进行扫描。

1. 在左侧导航区域中选择“源”

1. 选择“注册”

1. 选择 " **SQL server** "，然后 **继续**

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="设置 SQL 数据源。":::

5. 提供一个友好名称和服务器终结点，然后选择 " **完成** " 以注册数据源。 例如，如果你的 SQL server FQDN 是 **foobar.database.windows.net**，则输入 *foobar* 作为服务器终结点。

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
