---
title: 使用托管标识来访问 Azure SQL 数据库或 Azure Synapse Analytics-Azure 流分析
description: 本文介绍如何使用托管标识对 azure SQL 数据库或 Azure Synapse Analytics 输出的 Azure 流分析作业进行身份验证。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: ee617b50d85f611e130ec5533239c8924efecc6b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352178"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>使用托管标识从 Azure 流分析作业中访问 Azure SQL 数据库或 Azure Synapse Analytics (预览) 

Azure 流分析支持 Azure SQL 数据库和 Azure Synapse Analytics 输出接收器的 [托管标识身份验证](../active-directory/managed-identities-azure-resources/overview.md) 。 托管标识消除了基于用户的身份验证方法的如下限制：发生密码更改或用户令牌过期（每隔 90 天过期）时需要重新进行身份验证。 当你不再需要手动进行身份验证时，流分析部署可以完全自动化。

托管标识是 Azure Active Directory 中注册的表示给定流分析作业的托管应用程序。 托管应用程序用于对目标资源进行身份验证。 本文介绍如何通过 Azure 门户，为 Azure SQL 数据库或 Azure Synapse Analytics output (s) 启用流分析作业的托管标识。

## <a name="prerequisites"></a>先决条件

#### <a name="azure-sql-database"></a>[Azure SQL 数据库](#tab/azure-sql)

若要使用此功能，需要满足以下要求：

- 有 Azure 流分析作业。

- 有 Azure SQL 数据库资源。

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

若要使用此功能，需要满足以下要求：

- 有 Azure 流分析作业。

- Azure Synapse Analytics SQL 池。

- [配置为流分析作业](azure-synapse-analytics-output.md)的 Azure 存储帐户。

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

1. 导航到 Azure SQL 数据库或 Azure Synapse Analytics 资源，并选择数据库所处的 SQL Server。 可以在资源概述页上的“服务器名称”旁找到 SQL Server 名称。

1. 在“设置”下，选择“Active Directory 管理员”。 然后选择“设置管理员”。

   ![Active Directory 管理员页](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. 在“Active Directory 管理员”页中，搜索将成为 SQL Server 管理员的某个用户或组，并单击“选择”。

   ![添加 Active Directory 管理员](./media/sql-db-output-managed-identity/add-admin.png)

   “Active Directory 管理员”页会显示 Active Directory 的所有成员和组。 不能选择灰显的用户或组，因为它们不受 Azure Active Directory 管理员的支持。 请参阅在 **Azure Active Directory Features and Limitations**    [使用 SQL 数据库或 Azure Synapse 进行身份验证时使用 Azure Active Directory 身份验证](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations)的 Azure Active Directory 功能和限制部分中受支持的管理员列表。 Azure RBAC)  (基于角色的访问控制仅适用于该门户，不会传播到 SQL Server。 此外，所选用户或组是将能够在下一个部分创建“包含数据库用户”的用户。

1. 在“Active Directory 管理员”页中，选择“保存”。 更改管理员的过程只需要几分钟。

   设置 Azure Active Directory 管理员时，新的管理员名称 (用户或组) 不能作为 SQL Server 身份验证用户存在于虚拟主数据库中。 如果存在，则 Azure Active Directory 管理员安装程序会失败并回滚其创建，这表示管理员 (名称) 已经存在。 由于 SQL Server 身份验证用户不是 Azure Active Directory 的一部分，因此使用 Azure Active Directory 身份验证连接到服务器的任何操作都将失败。 

## <a name="create-a-contained-database-user"></a>创建包含数据库用户

接下来，在 Azure SQL 或 Azure Synapse 数据库中创建映射到 Azure Active Directory 标识的包含的数据库用户。 包含的数据库用户不具有主数据库的登录名，但会映射到与该数据库关联的目录中的标识。 Azure Active Directory 标识可以是单独的用户帐户，也可以是组。 在这种情况下，你需要为流分析作业创建包含的数据库用户。 

1. 使用 SQL Server Management Studio 连接到 Azure SQL 或 Azure Synapse 数据库。 “用户名”是具有 ALTER ANY USER 权限的 Azure Active Directory 用户。 在 SQL Server 上设置的管理员是一个示例。 使用“Azure Active Directory - 通用且具有 MFA”身份验证。 

   ![连接到 SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   服务器名称 `<SQL Server name>.database.windows.net` 在不同区域可能不同。 例如，中国地区应使用 `<SQL Server name>.database.chinacloudapi.cn`。
 
   可以通过转到 " **选项" > 连接属性 "> 连接到数据库**" 来指定特定的 azure SQL 或 azure Synapse 数据库。  

   ![SQL Server 连接属性](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. 首次连接时，可能会遇到以下窗口：

   ![新建防火墙规则窗口](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. 如果是这样，请在 Azure 门户中转到 SQL Server 资源。 在“安全”部分下，打开“防火墙和虚拟网络”页。 
   1. 使用任意规则名称添加新规则。
   1. 使用“新建防火墙规则”窗口中的“发件人”IP 地址作为“开始 IP”。
   1. 使用“新建防火墙规则”窗口中的“收件人”IP 地址作为“结束 IP”。 
   1. 选择“保存”，并尝试再次从 SQL Server Management Studio 进行连接。 

1. 连接后，创建包含的数据库用户。 下面的 SQL 命令将创建一个包含的数据库用户，其名称与你的流分析作业相同。 请确保在 ASA_JOB_NAME 两侧加上括号。 使用以下 T-SQL 语法并运行查询。 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

1. 为了让 Microsoft 的 Azure Active Directory 验证流分析作业是否具有对 SQL 数据库的访问权限，我们需要授予 Azure Active Directory 与数据库进行通信的权限。 为此，请在 Azure 门户中再次访问 "防火墙和虚拟网络" 页，并启用 "允许 Azure 服务和资源访问此服务器"。 

   ![防火墙和虚拟网络](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>授予流分析作业权限

#### <a name="azure-sql-database"></a>[Azure SQL 数据库](#tab/azure-sql)

按照上一部分中所述，在门户中创建了包含的数据库用户并向其授予了对 Azure 服务的访问权限后，流分析作业将拥有托管标识的权限，以通过托管标识 **连接** 到 Azure SQL 数据库资源。 我们建议你向流分析作业授予“选择”和“插入”权限，因为后面在流分析工作流中会需要这些权限。 **SELECT** 权限允许作业在 Azure SQL 数据库中测试与表的连接。 在配置了输入和 Azure SQL 数据库输出后， **INSERT** 权限允许测试端到端流分析查询。

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

按照上一部分中所述，在门户中创建了包含的数据库用户并向其授予了对 Azure 服务的访问权限后，流分析作业将拥有托管标识的权限，以通过托管标识 **连接** 到 Azure Synapse 数据库资源。 建议你进一步向流分析作业授予 SELECT、INSERT 和管理数据库大容量操作权限，因为稍后将在流分析工作流中需要这些权限。 **SELECT** 权限允许作业在 Azure Synapse 数据库中测试与表的连接。 在配置了输入和 Azure Synapse 数据库输出后，" **插入** 和 **管理数据库批量操作** " 权限允许测试端到端流分析查询。

若要授予 "管理数据库大容量操作" 权限，你将需要向流分析作业授予在 [数据库权限中隐含](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest#remarks)为 **控制** 的所有权限。 你需要此权限，因为流分析作业执行 COPY 语句，该语句需要 [管理数据库大容量操作和插入](/sql/t-sql/statements/copy-into-transact-sql)。

---

可以使用 SQL Server Management Studio 将这些权限授予流分析作业。 有关详细信息，请参阅 GRANT (Transact-SQL) 参考。

若要仅对数据库中某个表或对象授予权限，请使用以下 T-SQL 语法，并运行查询。 

#### <a name="azure-sql-database"></a>[Azure SQL 数据库](#tab/azure-sql)

```sql
GRANT SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT [PERMISSION NAME] OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

或者，你可以在 SQL Server Management Studio 中右键单击 Azure SQL 或 Azure Synapse 数据库，然后选择 " **属性" > 权限**"。 从“权限”菜单中，你可以看到之前添加的流分析作业，可以根据需要手动授予或拒绝权限。

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>创建 Azure SQL 数据库或 Azure Synapse 输出

#### <a name="azure-sql-database"></a>[Azure SQL 数据库](#tab/azure-sql)

配置托管标识后，便可以将 Azure SQL 数据库或 Azure Synapse 输出添加到流分析作业。

请确保已在 SQL 数据库中使用适当的输出架构创建了一个表。 在向流分析作业添加 SQL 数据库输出时，此表的名称是必须填写的必需属性之一。 此外，请确保此作业具有“选择”和“插入”权限，以便测试连接并运行流分析查询。  如果尚未执行此操作，请参阅[授予流分析作业权限](#grant-stream-analytics-job-permissions)部分。 

1. 返回到你的流分析作业，然后在“作业拓扑”下导航到“输出”页。 

1. 选择“添加”>“SQL 数据库”。 在 SQL 数据库输出接收器的输出属性窗口中，从“身份验证模式”下拉列表选择“托管标识”。

1. 填写其余的属性。 若要详细了解如何创建 SQL 数据库输出，请参阅[使用流分析创建 SQL 数据库输出](sql-database-output.md)。 完成后，选择“保存”。

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

由于已配置托管标识和存储帐户，因此可将 Azure SQL 数据库或 Azure Synapse 输出添加到流分析作业。

确保已在 Azure Synapse 数据库中使用相应的输出架构创建了一个表。 此表的名称是在将 Azure Synapse 输出添加到流分析作业时必须填写的必需属性之一。 此外，请确保此作业具有“选择”和“插入”权限，以便测试连接并运行流分析查询。  如果尚未执行此操作，请参阅[授予流分析作业权限](#grant-stream-analytics-job-permissions)部分。

1. 返回到你的流分析作业，然后在“作业拓扑”下导航到“输出”页。

1. 选择 " **添加 > Azure Synapse Analytics**"。 在 SQL 数据库输出接收器的输出属性窗口中，从“身份验证模式”下拉列表选择“托管标识”。

1. 填写其余的属性。 若要了解有关创建 Azure Synapse 输出的详细信息，请参阅 azure [流分析中的 Azure Synapse Analytics 输出](azure-synapse-analytics-output.md)。 完成后，选择“保存”。

---

## <a name="remove-managed-identity"></a>删除托管标识

仅当删除作业时，才会删除为流分析作业创建的托管标识。 如果不删除该作业，则无法删除该托管标识。 如果不再想要使用托管标识，可以更改输出的身份验证方法。 在删除作业之前，托管标识将继续存在，如果你决定再次使用托管标识身份验证，则将使用该标识。

## <a name="next-steps"></a>后续步骤

* [了解 Azure 流分析的输出](stream-analytics-define-outputs.md)
* [从 Azure 流分析输出到 Azure SQL 数据库](stream-analytics-sql-output-perf.md)
* [Azure 流分析中的 azure Synapse Analytics 输出](azure-synapse-analytics-output.md)
