---
title: 如何扫描专用 SQL 池
description: 本操作方法指南介绍了关于如何扫描专用 SQL 池的详细信息。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 09dc3c20ca95f32ee4c8f01d6b4986adfcd3703e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751916"
---
# <a name="register-and-scan-dedicated-sql-pools-formerly-sql-dw"></a>注册并扫描专用 SQL 池（以前称为 SQL DW）

> [!NOTE]
> 如果想在 Synapse 工作区内注册和扫描专用 SQL 数据库，则必须按照[此处](register-scan-synapse-workspace.md)的说明进行操作。

本文介绍如何在 Purview 中注册和扫描专用 SQL 池（以前称为 SQL DW）的实例。

## <a name="supported-capabilities"></a>支持的功能

Azure Synapse Analytics（以前称为 SQL DW）支持完整和增量扫描，以捕获元数据和架构。 扫描时还会根据系统和自定义分类规则自动分类数据。

### <a name="known-limitations"></a>已知的限制

> * Azure Purview 在“架构”选项卡中不支持超过 300 列，并将显示“Additional-Columns-Truncated”。 

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，先创建一个 Azure Purview 帐户。 有关创建 Purview 帐户的详细信息，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。
- 你需要是 Azure Purview 数据源管理员
- Purview 帐户与 Azure Synapse Analytics 之间的网络访问权限。
 
## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

有三种方法可为 Azure Synapse Analytics 设置身份验证：

- 托管标识
- SQL 身份验证
- Service Principal

    > [!Note]
    > 只有服务器级别主体登录（由预配过程创建）或 master 数据库中的 `loginmanager` 数据库角色成员可以创建新的登录。 授予权限后大约需要 15 分钟，Purview 帐户应具有适当的权限才能扫描资源。

### <a name="managed-identity-recommended"></a>托管标识（推荐） 
   
Purview 帐户具有其自己的托管标识，这基本上就是创建它时所用的 Purview 名称。 必须遵循先决条件和有关[使用 Azure AD 应用程序创建 Azure AD 用户](../azure-sql/database/authentication-aad-service-principal-tutorial.md)的教程，使用准确的 Purview 托管标识名称，在 Azure Synapse Analytics（以前称为 SQL DW）中创建 Azure AD 用户。

用于创建用户和授予权限的示例 SQL 语法：

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_datareader', [PurviewManagedIdentity]
GO
```

身份验证必须具有充分权限，以获取数据库、架构和表的元数据。 它还必须能够查询表以进行采样分类。 建议向标识分配 `db_datareader` 权限。

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

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>授予服务主体访问 Azure Synapse Analytics（以前称为 SQL DW）的权限

此外，还必须遵循先决条件和有关[使用 Azure AD 应用程序创建 Azure AD 用户](../azure-sql/database/authentication-aad-service-principal-tutorial.md)的教程，在 Azure Synapse Analytics 中创建一个 Azure AD 用户。 用于创建用户和授予权限的示例 SQL 语法：

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalName]
GO
```

> [!Note]
> Purview 需要“应用程序(客户端) ID”和“客户端机密”才能进行扫描 。

### <a name="sql-authentication"></a>SQL 身份验证

如果没有可用的登录名，可以按照[创建登录名](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1)中的说明，为 Azure Synapse Analytics（以前称为 SQL DW）创建登录名。

如果选择的身份验证方法为 **SQL 身份验证**，则需要获取密码，并将其存储在密钥保管库中：

1. 获取 SQL 登录的密码
1. 导航到你的密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”并在登录 SQL 时输入“名称”和“值”作为密码  
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，使用密钥[创建新凭据](manage-credentials.md#create-a-new-credential)以设置扫描

## <a name="register-a-sql-dedicated-pool-formerly-sql-dw"></a>注册 SQL 专用池（之前称为 SQL DW）

若要在 Purview 中注册新的 SQL 专用池，请执行以下操作：

1. 导航到你的 Purview 帐户。
1. 在左侧导航区域中选择“数据映射”。
1. 选择“注册”
1. 在“注册源”中，选择“SQL dedicated pool（SQL 专用池）”（之前称为 SQL DW）。
1. 选择“继续”

在“注册源(Azure Synapse Analytics)”屏幕上，执行以下操作：

1. 输入数据源将在目录中列出的名称。
2. 选择 Azure 订阅以筛选 Azure Synapse 工作区。
3. 选择一个 Azure Synapse 工作区。
4. 选择集合或创建新集合（可选）。
5. 选择“注册”以注册数据源。

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="注册源选项" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 在 Purview Studio 的左窗格中选择“数据映射”选项卡。

1. 选择已注册的 SQL 专用池源。

1. 选择“新建扫描”

1. 选择要连接到数据源的凭据。

   :::image type="content" source="media/register-scan-azure-synapse-analytics/sql-dedicated-pool-set-up-scan.png" alt-text="设置扫描":::

1. 通过在列表中选择适当的项，可以将扫描范围限定到特定的表。

   :::image type="content" source="media/register-scan-azure-synapse-analytics/scope-scan.png" alt-text="限定扫描范围":::

1. 然后选择扫描规则集。 可以在系统默认项和现有的自定义规则集之间选择，或者可以以内联方式创建新规则集。

   :::image type="content" source="media/register-scan-azure-synapse-analytics/select-scan-rule-set.png" alt-text="扫描规则集":::

1. 选择扫描触发器。 可以设置一个计划或运行一次扫描。

   :::image type="content" source="media/register-scan-azure-synapse-analytics/trigger-scan.png" alt-text="trigger":::

1. 查看扫描并选择“保存并运行”。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
