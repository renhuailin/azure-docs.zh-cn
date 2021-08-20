---
title: 注册并扫描 Azure SQL 数据库
description: 本教程介绍如何扫描 Azure SQL 数据库
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 06/08/2021
ms.openlocfilehash: f4fa21c99a17111b1045b66713490b86592e04bf
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467109"
---
# <a name="register-and-scan-an-azure-sql-database"></a>注册并扫描 Azure SQL 数据库

本文概述了如何在 Purview 中注册 Azure SQL 数据库数据源并对其设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Azure SQL 数据库数据源支持以下功能：

- 完全扫描和增量扫描，用于捕获 Azure SQL 数据库中的元数据和分类。

- ADF 复制和数据流活动的数据资产之间的世系。

### <a name="known-limitations"></a>已知的限制

> * Azure Purview 在“架构”选项卡中不支持超过 300 列，并将显示“Additional-Columns-Truncated”。 

## <a name="prerequisites"></a>先决条件

1. 创建新 Purview 帐户（如果还没有该帐户）。

1. Purview 帐户与 Azure SQL 数据库之间的网络访问权限。


### <a name="set-up-authentication-for-a-scan"></a>为扫描设置身份验证

用于扫描 Azure SQL 数据库的身份验证。 如果需要创建新的身份验证，则需要[授予对 SQL 数据库的数据库访问权限](../azure-sql/database/logins-create-manage.md)。 目前，Purview 支持三种身份验证方法：

- SQL 身份验证
- Service Principal
- 托管标识

#### <a name="sql-authentication"></a>SQL 身份验证

> [!Note]
> 只有服务器级别主体登录（由预配过程创建）或 master 数据库中的 `loginmanager` 数据库角色成员可以创建新的登录。 授予权限后大约需要 15 分钟，Purview 帐户应具有适当的权限才能扫描资源。

如果没有可用的登录名，则可以按照[创建登录名](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)中的说明为 Azure SQL 数据库创建登录名。 后续步骤将用到该用户名和密码 。

1. 在 Azure 门户中，导航到密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”并在 Azure SQL 数据库中输入“名称”和“值”作为密码  
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，请使用用户名和密码[创建新凭据](manage-credentials.md#create-a-new-credential)以设置扫描 

#### <a name="service-principal-and-managed-identity"></a>服务主体和托管标识

Purview 可以通过几个步骤使用服务主体或自己的托管标识来扫描 Azure SQL 数据库

   > [!Note]
   > Purview 需要“应用程序(客户端) ID”和“客户端机密”才能进行扫描 。

##### <a name="create-or-use-an-existing-service-principal"></a>创建或使用现有的服务主体

> [!Note]
> 如果使用的是 Purview 的托管标识，则跳过此步骤

若要使用服务主体，可以使用现有的服务主体，也可以创建一个新的服务主体。 

> [!Note]
> 如果必须创建新的服务主体，请执行以下步骤：
> 1. 导航到 [Azure 门户](https://portal.azure.com)。
> 1. 从左侧菜单中选择“Azure Active Directory”。
> 1. 选择 **“应用注册”**。
> 1. 选择“+ 新建应用程序注册”。
> 1. 为应用程序输入名称（服务主体名称）。
> 1. 选择“仅此组织目录中的帐户”。
> 1. 对于重定向 URI，选择“Web”并输入所需的任何 URL；它无需是真实的 URL 或有效的 URL。
> 1. 然后选择“注册”。

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>在数据库帐户中配置 Azure AD 身份验证

服务主体或托管标识必须具有获取数据库、架构和表的元数据的权限。 它还必须能够查询表以进行采样分类。

- [使用 Azure SQL 配置和管理 Azure AD 身份验证](../azure-sql/database/authentication-aad-configure.md)
- 如果你使用的是托管标识，则你的 Purview 帐户具有其自己的托管标识，这基本上就是创建它时所用的 Purview 名称。 必须遵循关于[在 Azure SQL 数据库中创建服务主体用户](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database)的教程，在 Azure SQL 数据库中使用确切的 Purview 托管标识或你自己的服务主体创建 Azure AD 用户。 需要为该标识分配适当的权限（例如 `db_datareader`）。 用于创建用户和授予权限的示例 SQL 语法：

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [Username]
    GO
    ```

    > [!Note]
    > `Username` 是你自己的服务主体或 Purview 的托管标识。 可以详细了解[固定数据库角色及其功能](/sql/relational-databases/security/authentication-access/database-level-roles#fixed-database-roles)。
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>将服务主体添加到密钥保管库和 Purview 的凭据

> [!Note]
> 如果打算使用 Purview 的托管标识，则可以跳过此步骤，因为默认的 Purview 托管标识已经在 Purview-MSI 凭据中 。

需要获取服务主体的应用程序 ID 和机密：

1. 导航到 [Azure 门户](https://portal.azure.com)中的服务主体
1. 复制“概述”中的“应用程序(客户端) ID”和“证书和机密”中的“客户端机密”的值   。
1. 导航到你的密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”并输入所选的“名称”和“值”作为服务主体的“客户端机密”   
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，使用服务主体[创建新凭据](manage-credentials.md#create-a-new-credential)以设置扫描

### <a name="firewall-settings"></a>防火墙设置

如果数据库服务器上启用了防火墙，则需要更新防火墙，允许通过以下两种方式之一进行访问：

1. 允许通过防火墙进行 Azure 连接。
1. 安装自承载集成运行时并允许它通过防火墙进行访问。

#### <a name="allow-azure-connections"></a>允许 Azure 连接

启用 Azure 连接将允许 Azure Pureview 访问和连接服务器，而无需更新防火墙本身。 可遵循 [Azure 内部连接](../azure-sql/database/firewall-configure.md#connections-from-inside-azure)的操作指南。

1. 导航到数据库帐户
1. 在“概述”页上选择服务器名称
1. 选择“安全性”>“防火墙和虚拟网络”
1. 对于“允许 Azure 服务和资源访问此服务器”，选择“是” 

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="允许 Azure 服务和资源访问此服务器。" border="true":::

#### <a name="self-hosted-integration-runtime"></a>自承载集成运行时

可在计算机上安装自承载集成运行 (SHIR)，以连接到专用网络中的资源。

1. 在个人计算机或与数据库服务器相同的 VNet 中的计算机上[创建并安装自承载集成运行时](/azure/purview/manage-integration-runtimes)。
1. 检查数据库服务器防火墙，确认 SHIR 计算机是否可通过防火墙进行访问。 如果计算机没有访问权限，请添加该计算机的 IP。
1. 如果 Azure SQL Server 位于专用终结点后面或 VNet 中，则可使用[引入专用终结点](catalog-private-link.md#ingestion-private-endpoints-and-scanning-sources)来确保端到端网络隔离。

## <a name="register-an-azure-sql-database-data-source"></a>注册 Azure SQL 数据库数据源

若要在数据目录中注册新的 Azure SQL 数据库，请执行以下操作：

1. 导航到你的 Purview 帐户。

1. 在左侧导航区域中选择“源”。

1. 选择“注册”。

1. 在“注册源”上选择“Azure SQL 数据库” 。 选择“继续”。

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="注册新数据源" border="true":::

在“注册源（Azure SQL 数据库）”屏幕上，执行以下操作：

1. 输入数据源将在目录中列出的名称。
1. 选择“从 Azure 订阅中”，然后从“Azure 订阅”下拉框中选择相应订阅，并从“服务器名称”下拉框中选择相应服务器  。
1. 选择“注册”以注册数据源。

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="注册源选项" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> * 删除扫描不会删除以前的 Azure SQL 数据库扫描中的资产。
> * 如果在 Purview 的“架构”选项卡中编辑描述后，更改了源表并重新扫描源表，则不会再用架构更改来更新该资产。

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
