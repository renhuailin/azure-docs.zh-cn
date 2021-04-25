---
title: 如何扫描 Azure Synapse 工作区
description: 了解如何扫描 Azure Purview 数据目录中的 Synapse 工作区。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/31/2021
ms.openlocfilehash: 230894b8e474c8d230322fb1e240f0317512d036
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031253"
---
# <a name="register-and-scan-azure-synapse-workspaces"></a>注册和扫描 Azure Synapse 工作区

本文概述如何在 Purview 中注册 Azure Synapse 工作区并对其设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Azure Synapse 工作区扫描支持捕获元数据和架构，以在其中查找专用和无服务器的 SQL 数据库。 它还根据系统和自定义分类规则自动分类数据。

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，先创建一个 Azure Purview 帐户。 有关创建 Purview 帐户的详细信息，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。
- 你需要是 Azure Purview 数据源管理员
- 按以下部分所示设置身份验证

### <a name="setting-up-authentication-for-enumerating-dedicated-sql-database-resources-under-a-synapse-workspace"></a>设置用于枚举 Synapse 工作区下的专用 SQL 数据库资源的身份验证

1. 在 Azure 门户中，导航到 Synapse 工作区所在的“资源组”或“订阅” 。  
1. 从左侧导航菜单中选择“访问控制(IAM)” ****   
1. 你必须是所有者或用户访问管理员，才能在资源组或订阅中添加角色。 选择“+ 添加”按钮。 
1. 设置“读取者”角色，并在“选择”输入框下输入 Azure Purview 帐户名称（表示其 MSI）。 单击“保存”以完成角色分配。
1. 按照上述步骤 2 到 4，还可以在 Synapse 工作区所在的资源组或订阅中为 Azure Purview MSI 添加“存储 Blob 数据读取者”角色。

### <a name="setting-up-authentication-for-enumerating-serverless-sql-database-resources-under-a-synapse-workspace"></a>设置用于枚举 Synapse 工作区下的无服务器 SQL 数据库资源的身份验证

> [!NOTE]
> 若要运行这些命令，你必须是工作区的 Synapse 管理员。 在[此处](../synapse-analytics/security/how-to-set-up-access-control.md)详细了解 Synapse 权限。

1. 导航到你的 Synapse 工作区
1. 导航到“数据”部分和某个无服务器 SQL 数据库
1. 单击省略号图标，然后启动新的 SQL 脚本
1. 通过在 SQL 脚本中运行以下命令，将 Azure Purview 帐户 MSI（用帐户名表示）作为 sysadmin 添加到无服务器 SQL 数据库上：
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

### <a name="setting-up-authentication-to-scan-resources-under-a-synapse-workspace"></a>设置身份验证以扫描 Synapse 工作区下的资源

有三种方法可为 Azure Synapse 源设置身份验证：

- 托管标识
- Service Principal
 
#### <a name="using-managed-identity-for-dedicated-sql-databases"></a>将托管标识用于专用 SQL 数据库

1. 导航到你的 Synapse 工作区
1. 导航到“数据”部分和某个无服务器 SQL 数据库
1. 单击省略号图标，然后启动新的 SQL 脚本
1. 通过在 SQL 脚本中运行以下命令，将 Azure Purview 帐户 MSI（用帐户名表示）作为 db_owner 添加到专用 SQL 数据库上：

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [PurviewAccountName]
    GO
    ```
#### <a name="using-managed-identity-for-serverless-sql-databases"></a>将托管标识用于无服务器 SQL 数据库

1. 导航到你的 Synapse 工作区
1. 导航到“数据”部分和某个无服务器 SQL 数据库
1. 单击省略号图标，然后启动新的 SQL 脚本
1. 通过在 SQL 脚本中运行以下命令，将 Azure Purview 帐户 MSI（用帐户名表示）作为 sysadmin 添加到无服务器 SQL 数据库上：
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

#### <a name="using-service-principal-for-dedicated-sql-databases"></a>将服务主体用于专用 SQL 数据库

> [!NOTE]
> 必须首先按照[此处](manage-credentials.md)的说明设置服务主体类型的新凭据。

1. 导航到你的 Synapse 工作区
1. 导航到“数据”部分和某个无服务器 SQL 数据库
1. 单击省略号图标，然后启动新的 SQL 脚本
1. 通过在 SQL 脚本中运行以下命令，将“服务主体 ID”作为“db_owner”添加到专用 SQL 数据库上 ：

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [ServicePrincipalID]
    GO
    ```

#### <a name="using-service-principal-for-serverless-sql-databases"></a>将服务主体用于无服务器 SQL 数据库

1. 导航到你的 Synapse 工作区
1. 导航到“数据”部分和某个无服务器 SQL 数据库
1. 单击省略号图标，然后启动新的 SQL 脚本
1. 通过在 SQL 脚本中运行以下命令，将 Azure Purview 帐户 MSI（用帐户名表示）作为 sysadmin 添加到无服务器 SQL 数据库上：
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [ServicePrincipalID];
    ```

> [!NOTE]
> 你必须在你打算注册和扫描的 Synapse 工作区中的每个专用 SQL 数据库上设置身份验证。 上述的无服务器 SQL 数据库权限应用于工作区中的所有无服务器 SQL 数据库，即你只需运行一次。
    
## <a name="register-an-azure-synapse-source"></a>注册 Azure Synapse 源

若要在数据目录中注册新的 Azure Synapse 源，请执行以下操作：

1. 导航到你的 Purview 帐户
1. 在左侧导航区域中选择“源”
1. 选择“注册”
1. 在“注册源”上选择“Azure Synapse Analytics(多个)” 
1. 选择“继续”

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="设置 Azure Synapse 源":::

在“注册源(Azure Synapse Analytics)”屏幕上，执行以下操作：

1. 输入数据源将在目录中列出的名称。
1. （可选）选择要筛选到的订阅。
1. 从下拉列表中选择 Synapse 工作区名称。 SQL 终结点会根据你选择的工作区自动填充。 
1. 选择一个集合或创建新集合（可选）
1. 选择“完成”以注册数据源

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="填写 Azure Synapse 源的详细信息":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 导航到“源”部分。

1. 选择所注册的数据源。

1. 单击“查看详细信息”并选择“+ 新建扫描”，或使用源磁贴上的扫描快速操作图标

1. 填写“名称”，然后选择要在此源中扫描的所有资源类型。 SQL 数据库是 Synapse 工作区中目前唯一支持的类型。
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Azure Synapse 源扫描":::

1. 选择“凭据”以连接到数据源中的资源。 
  
1. 在每个类型中，你可以选择扫描所有资源，也可以选择按名称扫描部分资源。
1.  单击“继续”以继续。 

1.  选择 Azure Synapse SQL 类型的“扫描规则集”。 还可以创建内联扫描规则集。

1. 选择扫描触发器。 可以将其计划为每周/每月运行或运行一次 

1. 查看扫描，然后选择“保存”以完成设置   

## <a name="viewing-your-scans-and-scan-runs"></a>查看扫描和扫描运行

1. 通过单击“源”部分下的磁贴上的“查看详细信息”，查看源详细信息。 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Azure Synapse 源详细信息"::: 

1. 导航到“扫描详细信息”页来查看扫描运行详细信息。
    1. 状态栏是有关子资源运行状态的简短摘要。 它将显示在工作区级别扫描中。
    1. 绿色表示成功，而红色表示失败。 灰色表示扫描仍在进行中
    1. 可单击每个扫描以查看更精细的详细信息

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Azure Synapse 扫描详细信息" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

1. 在源详细信息页面底部查看最近失败的扫描运行摘要。 还可单击这些运行以查看与之相关更精细的详细信息。

## <a name="manage-your-scans---edit-delete-or-cancel"></a>管理扫描 - 编辑、删除或取消
若要管理或删除扫描，请执行以下操作：

- 导航到管理中心。 在“源和扫描”部分下，选择“数据源”，然后选择所需的数据源 。

- 选择要管理的扫描。 可以通过选择“编辑”来编辑扫描。

- 可以通过选择“删除”来删除扫描。
- 如果扫描正在运行，你也可以将其取消。

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)   