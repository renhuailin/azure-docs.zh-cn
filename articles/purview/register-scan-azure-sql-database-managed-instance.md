---
title: 注册和扫描 Azure SQL 数据库托管实例
description: 本教程介绍如何扫描 Azure SQL 数据库托管实例
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 05/08/2021
ms.openlocfilehash: 655314720975738ebdbddb009d6d31ad7a322e72
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112552209"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>注册和扫描 Azure SQL 数据库托管实例

本文概述了如何在 Purview 中注册 Azure SQL 数据库托管实例数据源并对其设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Azure SQL 数据库托管实例数据源支持以下功能：

- 完全扫描和增量扫描，以捕获 Azure SQL 数据库托管实例中的元数据和分类。

- ADF 复制和数据流活动的数据资产之间的世系。

## <a name="prerequisites"></a>先决条件

- 创建新 Purview 帐户（如果还没有该帐户）。

- [在 Azure SQL 托管实例中配置公共终结点](../azure-sql/managed-instance/public-endpoint-configure.md)
    > [!Note]
    > 你的组织必须能够允许公共终结点，因为 Purview 尚不支持专用终结点。 如果使用专用终结点，扫描将不会成功。

### <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

用于扫描 Azure SQL 数据库托管实例的身份验证。 如果需要创建新的身份验证，则需要[授予对 SQL 数据库托管实例的数据库访问权限](../azure-sql/database/logins-create-manage.md)。 目前，Purview 支持三种身份验证方法：

- SQL 身份验证
- Service Principal
- 托管标识

#### <a name="sql-authentication"></a>SQL 身份验证

> [!Note]
> 只有服务器级别主体登录（由预配过程创建）或 master 数据库中的 `loginmanager` 数据库角色成员可以创建新的登录。 授予权限后大约需要 15 分钟，Purview 帐户应具有适当的权限才能扫描资源。

如果没有可用的登录名，则可以按照[创建登录名](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)中的说明为 Azure SQL 数据库托管实例创建登录名。 后续步骤将用到该用户名和密码 。

1. 在 Azure 门户中，导航到密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”并在 Azure SQL 数据库托管实例中输入“名称”和“值”作为密码  
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，请使用用户名和密码[创建新凭据](manage-credentials.md#create-a-new-credential)以设置扫描 

#### <a name="service-principal-and-managed-identity"></a>服务主体和托管标识

Purview 可以通过几个步骤使用服务主体扫描 Azure SQL 数据库托管实例

##### <a name="create-or-use-an-existing-service-principal"></a>创建或使用现有的服务主体

> [!Note]
> 如果使用的是托管标识，则跳过此步骤

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
- 按照[创建映射到 Azure AD 标识的包含的用户](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities)的先决条件和教程，在 Azure SQL 数据库托管实例中创建 Azure AD 用户
- 向标识分配 `db_datareader` 权限

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>将服务主体添加到密钥保管库和 Purview 的凭据

> [!Note]
> 如果打算使用托管标识，则可以跳过此步骤，因为默认的 Purview 标识已经在 Purview-MSI 中 

需要获取服务主体的应用程序 ID 和机密：

1. 导航到 [Azure 门户](https://portal.azure.com)中的服务主体
1. 复制“概述”中的“应用程序(客户端) ID”和“证书和机密”中的“客户端机密”的值   。
1. 导航到你的密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”并输入所选的“名称”和“值”作为服务主体的“客户端机密”   
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，使用服务主体[创建新凭据](manage-credentials.md#create-a-new-credential)以设置扫描

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>注册 Azure SQL 数据库托管实例数据源

1. 导航到你的 Purview 帐户。

1. 在左侧导航区域中选择“源”。

1. 选择“注册”。

1. 选择“Azure SQL 数据库托管实例”，然后选择“继续” 。

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="设置 SQL 数据源":::

1. 选择“从 Azure 订阅中”，然后从“Azure 订阅”下拉框中选择相应订阅，并从“服务器名称”下拉框中选择相应服务器  。

1. 提供公共终结点完全限定的域名和端口号 。 然后选择“注册”以注册数据源。

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="添加 Azure SQL 数据库托管实例":::

    例如 `foobar.public.123.database.windows.net,3342`

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 在 Purview Studio 的左窗格中选择“数据映射”选项卡。

1. 选择你注册的 Azure SQL 数据库托管实例源。

1. 选择“新建扫描”

1. 选择要连接到数据源的凭据。

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-scan-sql-mi.png" alt-text="设置扫描":::

1. 通过在列表中选择适当的项，可以将扫描范围限定到特定的表。

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/scope-your-scan.png" alt-text="限定扫描范围":::

1. 然后选择扫描规则集。 可以在系统默认项和现有的自定义规则集之间选择，或者可以以内联方式创建新规则集。

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/scan-rule-set.png" alt-text="扫描规则集":::

1. 选择扫描触发器。 可以设置一个计划或运行一次扫描。

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/trigger-scan.png" alt-text="trigger":::

1. 查看扫描并选择“保存并运行”。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

> [!NOTE]
> 删除扫描不会删除以前的 Azure SQL 数据库托管实例扫描中的资产。

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
