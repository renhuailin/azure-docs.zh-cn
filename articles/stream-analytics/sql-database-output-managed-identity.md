---
title: 使用托管标识访问 Azure SQL 数据库或 Azure Synapse Analytics - Azure 流分析
description: 本文介绍如何使用托管标识对 Azure 流分析作业的 Azure SQL 数据库或 Azure Synapse Analytics 输出进行身份验证。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: e491c421f4af256b2e74fa61eb442d269bdb9e34
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102487910"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>使用托管标识访问 Azure 流分析作业的 Azure SQL 数据库或 Azure Synapse Analytics（预览）

Azure 流分析支持对 Azure SQL 数据库和 Azure Synapse Analytics 输出接收器进行[托管标识身份验证](../active-directory/managed-identities-azure-resources/overview.md)。 托管标识消除了基于用户的身份验证方法的如下限制：发生密码更改或用户令牌过期（每隔 90 天过期）时需要重新进行身份验证。 当你不再需要手动进行身份验证时，流分析部署可以完全自动化。

托管标识是 Azure Active Directory 中注册的表示给定流分析作业的托管应用程序。 托管应用程序用于对目标资源进行身份验证。 本文介绍如何通过 Azure 门户为流分析作业的 Azure SQL 数据库或 Azure Synapse Analytics 输出启用托管标识。

## <a name="overview"></a>概述

本文介绍使用托管标识身份验证模式将流分析作业连接到 Azure SQL 数据库或 Azure Synapse Analytics SQL 池所需的步骤。 

- 首先为流分析作业创建一个系统分配托管标识。 这是作业在 Azure Active Directory 中的标识。  

- 将 Active Directory 管理员添加到 SQL Server 或 Synapse 工作区，这将为该资源启用 Azure AD（托管标识）身份验证。

- 接下来，在数据库中创建一个表示流分析作业标识的包含的用户。 每当流分析作业与 SQL DB 或 Synapse SQL DB 资源交互时，它将引用这个标识来检查流分析作业拥有哪些权限。

- 向流分析作业授予访问 SQL 数据库或 Synapse SQL 池所需的权限。

- 最后，在流分析作业中添加 Azure SQL 数据库/Azure Synapse Analytics 作为输出。

## <a name="prerequisites"></a>先决条件

#### <a name="azure-sql-database"></a>[Azure SQL 数据库](#tab/azure-sql)

使用此功能需满足以下条件：

- 有 Azure 流分析作业。

- 有 Azure SQL 数据库资源。

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

使用此功能需满足以下条件：

- 有 Azure 流分析作业。

- 有 Azure Synapse Analytics SQL 池。

- 为[流分析作业](azure-synapse-analytics-output.md)配置的 Azure 存储帐户。

- 注意：与 Synapse SQL MSI 集成的流分析帐户存储 MSI 当前不可用。

---

## <a name="create-a-managed-identity"></a>创建托管标识

首先，创建 Azure 流分析作业的托管标识。

1. 在 [Azure 门户](https://portal.azure.com)中，打开 Azure 流分析作业。

1. 从左侧导航菜单中，选择“配置”下的“托管标识”。 然后，选中“使用系统分配的托管标识”旁的框，然后选择“保存”。

   ![选择系统分配的托管标识](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)

   在 Azure Active Directory 中，为流分析作业标识创建服务主体。 新建标识的生命周期将由 Azure 管理。 删除流分析作业时，Azure 会自动删除关联的标识（即服务主体）。

1. 保存配置后，服务主体的对象 ID (OID) 将列为主体 ID，如下所示： 

   ![显示为主体 ID 的对象 ID](./media/sql-db-output-managed-identity/principal-id.png)

   服务主体与流分析作业同名。 例如，如果作业的名称是 MyASAJob，则服务主体的名称也是 MyASAJob。

## <a name="select-an-active-directory-admin"></a>选择 Active Directory 管理员

创建托管标识后，选择 Active Directory 管理员。

1. 导航到 Azure SQL 数据库或 Azure Synapse Analytics SQL 池资源，然后分别选择资源所属的 SQL Server 或 Synapse 工作区。 可在服务器名称或工作区名称附近的资源概述页中找到指向这些资源的链接 。

1. 对于 SQL Server 和 Synapse 工作区，分别在“设置”下选择“Active Directory 管理员”或“SQL Active Directory 管理员”  。 然后选择“设置管理员”。

   ![Active Directory 管理员页](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. 在“Active Directory 管理员”页中，搜索将成为 SQL Server 管理员的某个用户或组，并单击“选择”。 这将是能够在下一个部分创建“包含的数据库用户”的用户。

   ![添加 Active Directory 管理员](./media/sql-db-output-managed-identity/add-admin.png)

   “Active Directory 管理员”页会显示 Active Directory 的所有成员和组。 不能选择灰显的用户或组，因为不支持它们充当 Azure Active Directory 管理员。 有关受支持的管理员列表，请参阅 [将 Azure Active Directory 身份验证与使用 SQL 数据库或 Azure Synapse 进行身份验证结合使用](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations)中的“Azure Active Directory 功能和限制”部分 ****  。

1. 在“Active Directory 管理员”页中，选择“保存”。 更改管理员的过程只需要几分钟。

## <a name="create-a-contained-database-user"></a>创建包含数据库用户

接下来，在 Azure SQL 或 Azure Synapse 数据库中创建包含的数据库用户，该用户将映射到 Azure Active Directory 标识。 包含的数据库用户在主数据库中没有登录名，但它映射到与数据库关联的目录中的标识。 Azure Active Directory 标识可以是单独的用户帐户，也可以是组。 在这种情况下，你需要为流分析作业创建包含的数据库用户。 

有关详细信息，请查看以下文章以了解 Azure AD 集成的背景：[SQL 数据库和 Azure Synapse Analytics 的通用身份验证（对 MFA 的 SSMS 支持）](../azure-sql/database/authentication-mfa-ssms-overview.md)

1. 使用 SQL Server Management Studio 连接到 Azure SQL 或 Azure Synapse 数据库。 “用户名”是具有 ALTER ANY USER 权限的 Azure Active Directory 用户。 在 SQL Server 上设置的管理员是一个示例。 使用“Azure Active Directory - 通用且具有 MFA”身份验证。 

   ![连接到 SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   服务器名称 `<SQL Server name>.database.windows.net` 在不同区域可能不同。 例如，中国地区应使用 `<SQL Server name>.database.chinacloudapi.cn`。
 
   可转到“选项”>“连接属性”>“连接到数据库”来指定特定的 Azure SQL 或 Azure Synapse 数据库。  

   ![SQL Server 连接属性](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. 首次连接时，可能会遇到以下窗口：

   ![新建防火墙规则窗口](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. 如果是这样，请在 Azure 门户中转到 SQL Server/Synapse 工作区资源。 在“安全”部分下，打开“防火墙和虚拟网络”/“防火墙”页 。 
   1. 使用任意规则名称添加新规则。
   1. 使用“新建防火墙规则”窗口中的“发件人”IP 地址作为“开始 IP”。
   1. 使用“新建防火墙规则”窗口中的“收件人”IP 地址作为“结束 IP”。 
   1. 选择“保存”，并尝试再次从 SQL Server Management Studio 进行连接。 

1. 连接后，创建包含的数据库用户。 下面的 SQL 命令将创建一个包含的数据库用户，其名称与你的流分析作业相同。 请确保在 ASA_JOB_NAME 两侧加上括号。 使用以下 T-SQL 语法并运行查询。 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```
   
    若要验证是否已正确添加包含的数据库用户，请在相关数据库下的 SSMS 中运行以下命令，并检查 ASA_JOB_NAME 是否位于“名称”列下。

   ```sql
   SELECT * FROM <SQL_DB_NAME>.sys.database_principals 
   WHERE type_desc = 'EXTERNAL_USER' 
   ```

1. 为了让 Microsoft 的 Azure Active Directory 验证流分析作业是否具有对 SQL 数据库的访问权限，我们需要授予 Azure Active Directory 与数据库进行通信的权限。 为此，请再次转到 Azure 门户的“防火墙和虚拟网络”/“防火墙”页面，并启用“允许 Azure 服务和资源访问此服务器/工作区”。

   ![防火墙和虚拟网络](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>授予流分析作业权限

#### <a name="azure-sql-database"></a>[Azure SQL 数据库](#tab/azure-sql)

在创建包含数据库用户并按照上节所述在门户中授予其对 Azure 服务的访问权限后，流分析作业就具有了托管标识的权限，可通过托管标识连接到 Azure SQL 数据库资源。 我们建议你向流分析作业授予“选择”和“插入”权限，因为后面在流分析工作流中会需要这些权限 。 通过“选择”权限，此作业可以测试其与 Azure SQL 数据库中表的连接。 配置输入和 Azure SQL 数据库输出后，可以通过“插入”权限测试端到端流分析查询。

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

在创建包含数据库用户并按照上节所述在门户中授予其对 Azure 服务的访问权限后，流分析作业就具有了托管标识的权限，可通过托管标识连接到 Azure Synapse 数据库资源。 我们建议你向流分析作业进一步授予“选择”、“插入”和“管理数据库批量操作”权限，因为后面在流分析工作流中会需要这些权限  。 通过“选择”权限，此作业可以测试其与 Azure Synapse 数据库中表的连接。 配置输入和 Azure Synapse 数据库输出后，可通过“插入”和“管理数据库批量操作”权限测试端到端流分析查询 。

若要授予“管理数据库批量操作”权限，你将需要向流分析作业授予[数据库默示的权限](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest&preserve-view=true#remarks)下标记为“控制”的所有权限 。 你需要此权限，因为流分析作业执行 COPY 语句，该语句需要[管理数据库批量操作和插入](/sql/t-sql/statements/copy-into-transact-sql)权限。

---

可以使用 SQL Server Management Studio 将这些权限授予流分析作业。 有关详细信息，请参阅 GRANT (Transact-SQL) 参考。

若要仅对数据库中某个表或对象授予权限，请使用以下 T-SQL 语法，并运行查询。 

#### <a name="azure-sql-database"></a>[Azure SQL 数据库](#tab/azure-sql)

```sql
GRANT CONNECT, SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT CONNECT, SELECT, INSERT, CONTROL, ADMINISTER DATABASE BULK OPERATIONS OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

或者，你可以在 SQL Server Management Studio 中右键单击 Azure SQL 或 Azure Synapse 数据库，然后选择“属性”>“权限”。 从“权限”菜单中，你可以看到之前添加的流分析作业，可以根据需要手动授予或拒绝权限。

若要查看已添加到 ASA_JOB_NAME 用户的所有权限，请在相关 DB 下的 SSMS 中运行以下命令： 

```sql
SELECT dbprin.name, dbprin.type_desc, dbperm.permission_name, dbperm.state_desc, dbperm.class_desc, object_name(dbperm.major_id) 
FROM sys.database_principals dbprin 
LEFT JOIN sys.database_permissions dbperm 
ON dbperm.grantee_principal_id = dbprin.principal_id 
WHERE dbprin.name = '<ASA_JOB_NAME>' 
```

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>创建 Azure SQL 数据库或 Azure Synapse 输出

#### <a name="azure-sql-database"></a>[Azure SQL 数据库](#tab/azure-sql)

配置托管标识后，便可以将 Azure SQL 数据库或 Azure Synapse 输出添加到流分析作业。

请确保已在 SQL 数据库中使用适当的输出架构创建了一个表。 在向流分析作业添加 SQL 数据库输出时，此表的名称是必须填写的必需属性之一。 此外，请确保此作业具有“选择”和“插入”权限，以便测试连接并运行流分析查询。  如果尚未执行此操作，请参阅[授予流分析作业权限](#grant-stream-analytics-job-permissions)部分。 

1. 返回到你的流分析作业，然后在“作业拓扑”下导航到“输出”页。 

1. 选择“添加”>“SQL 数据库”。 在 SQL 数据库输出接收器的输出属性窗口中，从“身份验证模式”下拉列表选择“托管标识”。

1. 填写其余的属性。 若要详细了解如何创建 SQL 数据库输出，请参阅[使用流分析创建 SQL 数据库输出](sql-database-output.md)。 完成后，选择“保存”。

1. 单击“保存”后，资源的连接测试应该会自动触发。 成功完成后，你已成功将流分析作业配置为使用托管标识身份验证模式连接到 Azure SQL 数据库或 Synapse SQL 数据库。 

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

配置托管标识和存储帐户后，便可将 Azure SQL 数据库或 Azure Synapse 输出添加到流分析作业。

请确保已在 Azure Synapse 数据库中使用适当的输出架构创建了一个表。 在向流分析作业添加 Azure Synapse 输出时，此表的名称是必须填写的必需属性之一。 此外，请确保此作业具有“选择”和“插入”权限，以便测试连接并运行流分析查询。  如果尚未执行此操作，请参阅[授予流分析作业权限](#grant-stream-analytics-job-permissions)部分。

1. 返回到你的流分析作业，然后在“作业拓扑”下导航到“输出”页。

1. 选择“添加”>“Azure Synapse Analytics”。 在 SQL 数据库输出接收器的输出属性窗口中，从“身份验证模式”下拉列表选择“托管标识”。

1. 填写其余的属性。 若要详细了解如何创建 Azure Synapse 输出，请参阅 [Azure 流分析中的 Azure Synapse Analytics 输出](azure-synapse-analytics-output.md)。 完成后，选择“保存”。

1. 单击“保存”后，资源的连接测试应该会自动触发。 成功完成后，你可以继续将托管标识用于使用流分析的 Azure Synapse Analytics 资源。 

---

## <a name="remove-managed-identity"></a>删除托管标识

仅当删除流分析作业后，才会删除为该作业创建的托管标识。 如果不删除作业，则无法删除其托管标识。 如果不想再使用托管标识，可以更改对输出的身份验证方法。 在删除作业之前，托管标识会始终存在，如果决定再次使用托管标识身份验证，将再次使用该标识。

## <a name="next-steps"></a>后续步骤

* [了解 Azure 流分析的输出](stream-analytics-define-outputs.md)
* [从 Azure 流分析输出到 Azure SQL 数据库](stream-analytics-sql-output-perf.md)
* [Azure 流分析中的 Azure Synapse Analytics 输出](azure-synapse-analytics-output.md)
