---
title: 如何扫描 Azure Synapse Analytics
description: 本操作方法指南介绍了如何扫描 Azure Synapse Analytics 的详细信息。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: c95f8b9e4466b22519a4dea580a86a0dcda83857
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555927"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>注册并扫描 Azure Synapse Analytics

本文介绍如何在监控范围中注册和扫描 (以前的 SQL DW) 的 Azure Synapse Analytics 实例。

## <a name="supported-capabilities"></a>支持的功能

Azure Synapse Analytics (以前的 SQL DW) 支持完整和增量扫描，以捕获元数据和架构。 扫描还会根据系统和自定义分类规则自动对数据进行分类。

### <a name="known-limitations"></a>已知限制

Azure 监控范围不支持扫描 Azure Synapse Analytics 中的[视图](/sql/relational-databases/views/views?view=azure-sqldw-latest&preserve-view=true)

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，请创建一个 Azure 监控范围帐户。 有关创建监控范围帐户的详细信息，请参阅 [快速入门：创建 Azure 监控范围帐户](create-catalog-portal.md)。
- 你需要成为 Azure 监控范围数据源管理员
- 监控范围帐户与 Azure Synapse Analytics 之间的网络访问。
 
## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

有三种方法可设置 Azure blob 存储的身份验证：

- 托管标识
- SQL 身份验证
- Service Principal

    > [!Note]
    > 只有服务器级别主体登录（由预配过程创建）或 master 数据库中的 `loginmanager` 数据库角色成员可以创建新的登录。 授予权限后大约需要 15 分钟，Purview 帐户应具有适当的权限才能扫描资源。

### <a name="managed-identity-recommended"></a>建议 (托管标识)  
   
在创建监控范围帐户时，该帐户具有自己的托管标识，这基本上是监控范围名称。 必须遵循 [使用 Azure AD 应用程序创建 Azure AD 用户](/azure/azure-sql/database/authentication-aad-service-principal-tutorial)的先决条件和教程，在 Azure Synapse Analytics 中创建一个 Azure AD 用户， (以前的 SQL DW) 和确切的监控范围托管标识名称。

用于创建用户和授予权限的示例 SQL 语法：

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

身份验证必须具有获取数据库、架构和表的元数据的权限。 它还必须能够查询表以进行采样分类。 建议为 `db_owner` 标识分配权限。

### <a name="service-principal"></a>Service Principal

若要将服务主体身份验证用于扫描，可以使用现有的身份验证，也可以创建一个新的身份验证。 

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

需要获取服务主体的应用程序 ID 和机密：

1. 导航到 [Azure 门户](https://portal.azure.com)中的服务主体
1. 复制“概述”中的“应用程序(客户端) ID”和“证书和机密”中的“客户端机密”的值   。
1. 导航到你的密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”并输入所选的“名称”和“值”作为服务主体的“客户端机密”   
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，使用服务主体[创建新凭据](manage-credentials.md#create-a-new-credential)以设置扫描 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>授予服务主体访问 Azure Synapse Analytics (以前的 SQL DW) 

此外，还必须遵循 [使用 Azure AD 应用程序创建 Azure AD 用户](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial)的先决条件和教程，在 Azure Synapse Analytics 中创建 Azure AD 用户。 用于创建用户和授予权限的示例 SQL 语法：

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [ServicePrincipalName]
GO
```

> [!Note]
> Purview 需要“应用程序(客户端) ID”和“客户端机密”才能进行扫描 。

### <a name="sql-authentication"></a>SQL 身份验证

如果你还没有 SQL DW) ，你可以按照 [创建登录名](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) 中的说明创建 Azure Synapse Analytics 的登录名， (以前的 SQL DW。

如果选择的身份验证方法为 **SQL 身份验证**，则需要获取密码，并将其存储在密钥保管库中：

1. 获取 SQL 登录名的密码
1. 导航到你的密钥保管库
1. 选择“设置”>“机密”
1. 选择 " **+ 生成/导入**"，并输入名称和 **值** 作为 SQL 登录 **名** 的 *密码*
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，使用密钥 [创建新凭据](manage-credentials.md#create-a-new-credential) 以设置扫描

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>将 Azure Synapse Analytics 实例注册 (以前的 SQL DW) 

若要在数据目录中注册新的 Azure Synapse Analytics 服务器，请执行以下操作：

1. 导航到你的 Purview 帐户
1. 在左侧导航区域中选择“源”
1. 选择“注册”
1. 在 " **注册源**" 中，选择 " **Azure Synapse Analytics (以前的 SQL DW)**
1. 选择“继续”

在 " **(Azure Synapse Analytics 注册源")** 屏幕上，执行以下操作：

1. 输入数据源将在目录中列出的名称。
1. 选择要指向所需存储帐户的方式：
   1. 选择 " **从 azure** 订阅"，从 " **azure 订阅** " 下拉框中选择适当的订阅，并从 " **服务器名称** " 下拉框中选择相应的服务器。
   1. 也可选择“手动输入”并输入服务器名称 。
1. 选择“完成”以注册数据源。

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="注册源选项" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)

