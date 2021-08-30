---
title: 如何扫描 Azure Synapse Analytics 工作区
description: 了解如何扫描 Azure Purview 数据目录中的 Azure Synapse 工作区。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/18/2021
ms.openlocfilehash: a74e88d72d1e7109b6e0acfa81485476eed9e00b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731145"
---
# <a name="register-and-scan-azure-synapse-analytics-workspaces"></a>注册和扫描 Azure Synapse Analytics 工作区

本文概述如何在 Azure Purview 中注册 Azure Synapse Analytics 工作区并对其设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Azure Synapse Analytics 工作区扫描支持捕获元数据和架构，以在其中查找专用和无服务器 SQL 数据库。 工作区扫描还会根据系统和自定义分类规则自动对数据进行分类。

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，请创建一个 Azure Purview 帐户。 有关详细信息，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。
- 必须是 Azure Purview 数据源管理员。
- 按以下部分所述设置身份验证。

## <a name="register-and-scan-an-azure-synapse-workspace"></a>注册和扫描 Azure Synapse 工作区

> [!IMPORTANT]
> 若要成功扫描工作区，请按照以下步骤操作，并完全按照后续部分所述应用权限。

### <a name="step-1-register-your-source"></a>**步骤 1：注册源**

> [!NOTE]
> 只有至少对 Azure Synapse 工作区具有“读取者”角色并且也是 Azure Purview 中“数据源管理者”的用户才能执行此步骤 。

若要在数据目录中注册新的 Azure Synapse 源，请执行以下操作：

1. 转到 Azure Purview 帐户。
1. 在左侧窗格中，选择“源”。
1. 选择“注册”  。
1. 在“注册源”下，选择“Azure Synapse Analytics(多个)” 。
1. 选择“继续”。

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Azure Purview 中一系列源的屏幕截图，其中包括 Azure Synapse Analytics。":::

1. 在“注册源(Azure Synapse Analytics)”页面上，执行以下操作：

    a. 输入在数据目录中列出数据源时将使用的名称.  
    b. （可选）选择要筛选到的订阅。  
    c. 在“工作区名称”下拉列表中，选择你正在使用的工作区。  
    d. 在“终结点”下拉列表中，系统会根据工作区选择自动填充 SQL 终结点。  
    e. 在“选择集合”下拉列表中，选择要使用的集合，或者可以选择创建一个新集合。  
    f. 选择“注册”以完成数据源的注册。
    
    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="“(Azure Synapse Analytics)”页面的屏幕截图，该页面用于输入有关 Azure Synapse 源的详细信息。":::


### <a name="step-2-apply-permissions-to-enumerate-the-contents-of-the-azure-synapse-workspace"></a>**步骤 2**：应用权限以枚举 Azure Synapse 工作区的内容

#### <a name="set-up-authentication-for-enumerating-dedicated-sql-database-resources"></a>设置用于枚举专用 SQL 数据库资源的身份验证

1. 在 Azure 门户，转到 Azure Synapse 工作区资源。  
1. 在左侧窗格中，选择“访问控制(IAM)” **** 。 

   > [!NOTE]
   > 必须是“所有者”或“用户访问管理员”，才能在资源组中添加角色 。
   
1. 选择“添加”按钮。   
1. 设置“读取者”角色，并输入 Azure Purview 帐户名称（表示其托管服务标识 (MSI)）。
1. 选择“保存”完成角色分配。

> [!NOTE]
> 如果计划注册和扫描 Azure Purview 帐户中的多个 Azure Synapse 工作区，还可以从更高级别（例如资源组或订阅）分配角色。 

#### <a name="set-up-authentication-for-enumerating-serverless-sql-database-resources"></a>设置用于枚举无服务器 SQL 数据库资源的身份验证

需要在三处设置身份验证以允许 Purview 枚举无服务器 SQL 数据库资源：Synapse 工作区、关联的存储和无服务器数据库。 以下步骤将设置所有三者的权限。

1. 在 Azure 门户，转到 Azure Synapse 工作区资源。  
1. 在左侧窗格中，选择“访问控制(IAM)” **** 。 

   > [!NOTE]
   > 必须是“所有者”或“用户访问管理员”，才能在资源组中添加角色 。
   
1. 选择“添加”按钮。   
1. 设置“读取者”角色，并输入 Azure Purview 帐户名称（表示其托管服务标识 (MSI)）。
1. 选择“保存”完成角色分配。
1. 在 Azure 门户中，转到 Azure Synapse 工作区所在的“资源组”或“订阅” 。
1. 在左侧窗格中，选择“访问控制(IAM)” **** 。 

   > [!NOTE]
   > 必须是“所有者”或“用户访问管理员”，才能在资源组或订阅中添加角色  。 

1. 选择“添加”按钮。 
1. 设置“存储 Blob 数据读取者”角色，并在“选择”输入框中输入 Azure Purview 帐户名称（表示其 MSI） 。 
1. 选择“保存”完成角色分配。
1. 转到 Azure Synapse 工作区并打开 Synapse Studio。
1. 选择左侧的“数据”选项卡。
1. 选择其中一个数据库旁边的省略号 (...)，然后启动新的 SQL 脚本。
1. 将 Azure Purview 帐户 MSI（用帐户名表示）添加到无服务器 SQL 数据库上。 可以通过在 SQL 脚本中运行以下命令来执行此操作：
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ```

### <a name="step-3-apply-permissions-to-scan-the-contents-of-the-workspace"></a>**步骤 3**：应用权限以扫描工作区的内容

可以通过两种方法其中之一为 Azure Synapse 源设置身份验证：

- 使用托管标识
- 使用服务主体

> [!IMPORTANT]
> 无服务器数据库的这些步骤不适用于复制的数据库。 目前在 Synapse 中，从 Spark 数据库复制的无服务器数据库是只读的。 有关详细信息，请转到[此处](../synapse-analytics/sql/resources-self-help-sql-on-demand.md#operation-is-not-allowed-for-a-replicated-database)。

> [!NOTE]
> 必须在打算注册和扫描的 Azure Synapse 工作区中的每个专用 SQL 数据库上设置身份验证。 以下部分中提到的用于无服务器 SQL 数据库的权限适用于工作区内的所有数据库。 也就是说，只需要设置一次身份验证。

#### <a name="use-a-managed-identity-for-dedicated-sql-databases"></a>将托管标识用于专用 SQL 数据库

1. 转到 Azure Synapse 工作区。
1. 转到“数据”部分，然后查找其中一个专用 SQL 数据库。
1. 选择数据库旁边的省略号 (...)，然后启动新的 SQL 脚本。

   > [!NOTE]
   > 若要运行以下过程中的命令，必须对工作区具有 Azure Synapse 管理员身份。 有关 Azure Synapse Analytics 权限的详细信息，请参阅：[为 Azure Synapse 工作区设置访问控制](../synapse-analytics/security/how-to-set-up-access-control.md)。

1. 将 Azure Purview 帐户 MSI（用帐户名表示）作为“db_datareader”添加到专用 SQL 数据库。 可以通过在 SQL 脚本中运行以下命令来执行此操作：

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [PurviewAccountName]
    GO
    ```
#### <a name="use-a-managed-identity-for-serverless-sql-databases"></a>将托管标识用于无服务器 SQL 数据库

1. 转到 Azure Synapse 工作区。
1. 转到“数据”部分，然后对要扫描的每个数据库执行后续步骤 。
1. 选择数据库旁边的省略号 (...)，然后启动新的 SQL 脚本。
1. 将 Azure Purview 帐户 MSI（用帐户名表示）作为“db_datareader”添加到无服务器 SQL 数据库上。 可以通过在 SQL 脚本中运行以下命令来执行此操作：
    ```sql
    CREATE USER [PurviewAccountName] FOR LOGIN [PurviewAccountName];
    ALTER ROLE db_datareader ADD MEMBER [PurviewAccountName]; 
    ```

#### <a name="use-a-service-principal-for-dedicated-sql-databases"></a>将服务主体用于专用 SQL 数据库

> [!NOTE]
> 必须首先按照 [Azure Purview 中用于源身份验证的凭据](manage-credentials.md)中的说明设置“服务主体”类型的新凭据 。

1. 转到 Azure Synapse 工作区。
1. 转到“数据”部分，然后查找其中一个专用 SQL 数据库。
1. 选择数据库旁边的省略号 (...)，然后启动新的 SQL 脚本。
1. 将“服务主体 ID”作为“db_datareader”添加到专用 SQL 数据库 。 可以通过在 SQL 脚本中运行以下命令来执行此操作：

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [ServicePrincipalID]
    GO
    ```
> [!NOTE]
> 对 Synapse 工作区中所有专用 SQL 数据库重复上一步。 

#### <a name="use-a-service-principal-for-serverless-sql-databases"></a>将服务主体用于无服务器 SQL 数据库

1. 转到 Azure Synapse 工作区。
1. 转到“数据”部分，然后查找其中一个无服务器 SQL 数据库。
1. 选择数据库旁边的省略号 (...)，然后启动新的 SQL 脚本。
1. 将“服务主体 ID”添加到无服务器 SQL 数据库。 可以通过在 SQL 脚本中运行以下命令来执行此操作：
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ```
    
1. 将“服务主体 ID”作为“db_datareader”添加到要扫描的每个无服务器 SQL 数据库 。 可以通过在 SQL 脚本中运行以下命令来执行此操作：
   ```sql
    CREATE USER [ServicePrincipalID] FOR LOGIN [ServicePrincipalID];
    ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalID]; 
    ```

### <a name="step-4-set-up-azure-synapse-workspace-firewall-access"></a>**步骤 4**：设置 Synapse 工作区防火墙访问权限

1. 在 Azure 门户中，转到 Azure Synapse 工作区。 

1. 在左侧窗格中选择“防火墙”。

1. 对于“允许 Azure 服务和资源访问此工作区”控件，选择“启用” 。

1. 选择“保存”。

### <a name="step-5-set-up-a-scan-on-the-workspace"></a>**步骤 5**：对工作区设置扫描

若要创建并运行新扫描，请执行以下操作：

1. 在 Purview Studio 的左侧窗格中，选择“数据映射”选项卡。

1. 选择所注册的数据源。

1. 选择“查看详细信息”，然后选择“新建扫描” 。 此外，可以选择源磁贴上的“扫描快速操作”图标。

1. 在“扫描”详细信息窗格的“名称”框中，输入此扫描的名称 。
1. 在“类型”下拉列表中，选择要在此源中扫描的资源类型。 “SQL 数据库”是 Azure Synapse 工作区中目前唯一支持的类型。
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Azure Synapse 源扫描的详细信息窗格的屏幕截图。":::

1. 在“凭据”下拉列表中，选择凭据以连接到数据源中的资源。 
  
1. 在每个类型中，可以选择扫描所有资源，也可以选择按名称扫描部分资源。

1.  选择“继续”以继续。 

1.  选择“Azure Synapse SQL”类型的“扫描规则集” 。 还可以创建内联扫描规则集。

1. 选择扫描触发器。 可以将其计划为每周或每月运行一次，或者只运行一次 。

1. 查看扫描，然后选择“保存”以完成设置。   

#### <a name="view-your-scans-and-scan-runs"></a>查看扫描和扫描运行情况

1. 通过选择“源”部分下磁贴上的“查看详细信息”，查看源详细信息。 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Azure Synapse Analytics 源详细信息页的屏幕截图。"::: 

1. 转到“扫描详细信息”页，查看扫描运行详细信息。

    * “状态栏”显示有关子资源运行状态的简短摘要。 状态显示在工作区级别扫描中。  
    * 绿色表示扫描运行成功，红色表示扫描运行失败，灰色表示扫描仍在运行。  
    * 可以通过单击它们来查看有关扫描运行的更详细信息。

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Azure Synapse Analytics 扫描详细信息页的屏幕截图。" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

    * 可以在“源详细信息”页面底部查看最近失败的扫描运行摘要。 同样，可以通过单击它们来查看有关扫描运行的更详细信息。

#### <a name="manage-your-scans"></a>管理扫描

若要编辑、删除或取消扫描，请执行以下操作：

1. 转到管理中心。 在“源和扫描”部分下选择“数据源”，然后选择要管理的数据源 。

1. 选择要管理的扫描，然后选择“编辑”。

   - 若要删除扫描，请选择“删除”。
   - 如果扫描正在运行，可以将其取消。

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)   
