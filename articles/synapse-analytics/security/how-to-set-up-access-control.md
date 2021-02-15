---
title: 如何为 Synapse 工作区设置访问控制
description: 本文介绍如何使用 Azure 角色、Synapse 角色、SQL 权限和 Git 权限控制对 Synapse 工作区的访问。
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: c6ec84d41d113a38e78ab13404ef19faf625530b
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102166"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>如何为 Synapse 工作区设置访问控制 

本文介绍如何使用 Azure 角色、Synapse 角色、SQL 权限和 Git 权限控制对 Synapse 工作区的访问。   

在本指南中，您将设置一个工作区，并为许多 Synapse 项目配置一个适用的基本访问控制系统。  然后，它介绍了更高级的选项，以便在需要时进行更精细的控制。  

可以通过使用与组织中的角色和角色一致的安全组来简化 Synapse 访问控制。  只需在安全组中添加和删除用户即可管理访问权限。

在开始本演练之前，请阅读 [Synapse access control 概述](./synapse-workspace-access-control-overview.md) ，熟悉 Synapse 使用的访问控制机制。   

## <a name="access-control-mechanisms"></a>访问控制机制

> [!NOTE]
> 本指南中采用的方法是创建多个安全组，然后将角色分配给这些组。 设置组后，只需管理安全组中的成员身份即可控制对工作区的访问。

若要保护 Synapse 工作区，请遵循配置以下项的模式：

- **安全组**，用于对具有相似访问要求的用户进行分组。
- **Azure 角色**，用于控制哪些用户可以创建和管理 SQL 池、Apache Spark 池和集成运行时，以及如何访问 ADLS Gen2 存储。
- **Synapse 角色**，用于控制对已发布代码项目的访问，使用 Apache Spark 计算资源和集成运行时 
- **Sql 权限**，用于控制对 SQL 池的管理和数据平面访问。 
- **Git 权限**，用于在为工作区配置 Git 支持的情况下控制哪些用户可以访问源代码管理中的代码项目 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>保护 Synapse 工作区的步骤

本文档使用标准名称来简化说明。 将它们替换为所选的名称。

|设置 | 标准名称 | 说明 |
| :------ | :-------------- | :---------- |
| **Synapse 工作区** | `workspace1` |  Synapse 工作区的名称。 |
| **ADLSGEN2 帐户** | `storage1` | 用于工作区的 ADLS 帐户。 |
| **容器** | `container1` | STG1 中的容器，由工作区默认使用。 |
| **Active Directory 租户** | `contoso` | Active Directory 租户名称。|
||||

## <a name="step-1-set-up-security-groups"></a>步骤 1：创建安全组

>[!Note] 
>预览期间，建议创建映射到 Synapse **SYNAPSE SQL 管理员** 和 **Synapse Apache Spark 管理员** 角色的安全组。  由于引入了新的细化 Synapse RBAC 角色和范围，现在建议你使用这些新功能来控制对工作区的访问。  这些新角色和作用域可提供更大的配置灵活性，并可让开发人员在创建分析应用程序时经常混合使用 SQL 和 Spark，并可能需要授予对特定资源（而不是整个工作区）的访问权限。 [详细了解](./synapse-workspace-synapse-rbac.md) Synapse RBAC。

为工作区创建以下安全组：

- **`workspace1_SynapseAdministrators`**，适用于需要完全控制工作区的用户。  至少将自己添加到此安全组。
- **`workspace1_SynapseContributors`**，适用于需要开发、调试代码并将其发布到服务的开发人员。   
- **`workspace1_SynapseComputeOperators`**，适用于需要管理和监视 Apache Spark 池和集成运行时的用户。
- **`workspace1_SynapseCredentialUsers`**，对于需要使用工作区 MSI 调试和运行业务流程管道的用户 (托管服务标识) 凭据并取消管道运行。   

你将在工作区范围内将 Synapse 角色分配给这些组。  

同时创建此安全组： 
- **`workspace1_SQLAdmins`**，在工作区的 SQL 池中需要 SQL Active Directory 管理员权限的用户组。 

在 `workspace1_SQLAdmins` 创建 sql 池中的 sql 权限时，将使用该组。 

对于基本设置，这五个组就足够了。 稍后，你可以添加安全组来处理需要更专业访问权限的用户，或仅向用户提供对特定资源的访问权限。

> [!NOTE]
>- 通过[本文](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)了解如何创建安全组。
>- 通过[本文](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)了解如何添加另一个安全组中的安全组。

>[!Tip]
>单个 Synapse 用户可以使用 Azure 门户中 Azure Active Directory 来查看他们的组成员身份，以确定他们被授予了哪些角色。

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>步骤2：准备 ADLS Gen2 存储帐户

Synapse 工作区使用的默认存储容器：
  - 存储 Spark 表的备份数据文件
  - Spark 作业的执行日志
  - 管理你选择安装的库

确定有关存储的下列信息：

- 要用于工作区的 ADLS Gen2 帐户。 本文档对其进行了调用 `storage1` 。 `storage1` 被视为工作区的 "主要" 存储帐户。
- `workspace1`默认情况下，Synapse 工作区将使用的容器。 本文档对其进行了调用 `container1` 。 

- 使用 Azure 门户将以下 Azure 角色分配 `container1` 给安全组 

  - 将 **存储 Blob 数据参与者** 角色分配给 `workspace1_SynapseAdmins` 
  - 将 **存储 Blob 数据参与者** 角色分配给 `workspace1_SynapseContributors`
  - 将 **存储 Blob 数据参与者** 角色分配给 `workspace1_SynapseComputeOperators`

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>第 3 步：创建和配置 Synapse 工作区

在 Azure 门户中，创建 Synapse 工作区：

- 选择订阅
- 选择或创建一个资源组，该资源组具有 Azure **所有者** 角色。
- 命名工作区 `workspace1`
- 选择 `storage1` 存储帐户
- 选择用作 `container1` "filesystem" 的容器。
- 在 Synapse Studio 中打开 WS1
- 导航到 "**管理**  >  **访问控制**"，并在 *工作区范围内* 将 Synapse 角色分配给安全组，如下所示：
  - 将 **Synapse 管理员** 角色分配给 `workspace1_SynapseAdministrators` 
  - 将 **Synapse 参与者** 角色分配给 `workspace1_SynapseContributors` 
  - 将 **Synapse 计算操作员** 角色分配给 `workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>步骤4：向工作区 MSI 授予对默认存储容器的访问权限

若要运行管道和执行系统任务，Synapse 要求 (MSI) 的工作区托管服务标识需要访问 `container1` 默认 ADLS Gen2 帐户。

- 打开 Azure 门户
- 找到存储帐户， `storage1` 然后 `container1`
- 使用 **访问控制 (IAM)**，确保将 **存储 Blob 数据参与者** 角色分配给工作区 MSI
  - 如果未分配，则分配。
  - MSI 与工作区同名。 本文将是 `workspace1` 。

## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>步骤5：向 Synapse 管理员授予工作区上的 Azure 参与者角色 

若要创建 SQL 池、Apache Spark 池和集成运行时，用户必须至少具有 Azure 参与者对工作区的访问权限。 参与者角色还允许这些用户管理资源，包括暂停和缩放。

- 打开 Azure 门户
- 找到工作区， `workspace1`
- 将上的 **Azure 参与者** 角色分配 `workspace1` 给 `workspace1_SynapseAdministrators` 。 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>步骤6：分配 SQL Active Directory 管理员角色

工作站创建者会自动设置为工作区的 SQL Active Directory 管理员。  只能为单个用户或组授予此角色。 在此步骤中，将工作区中的 SQL Active Directory 管理员分配给 `workspace1_SQLAdmins` 安全组。  分配此角色向此组授予了对工作区中所有 SQL 池和数据库的特权管理权限。   

- 打开 Azure 门户
- 导航到 `workspace1`
- 在 " **设置**" 下，选择 **SQL Active Directory 管理**
- 选择 " **设置管理员** " 并选择 **`workspace1_SQLAdmins`**

>[!Note]
>步骤6是可选的。  你可以选择向组授予 `workspace1_SQLAdmins` 更低权限的角色。 若要分配 `db_owner` 或其他 sql 角色，必须在每个 sql 数据库上运行脚本。 

## <a name="step-7-grant-access-to-sql-pools"></a>步骤7：授予对 SQL 池的访问权限

默认情况下，分配了 Synapse 管理员角色的所有用户也将在 `db_owner` 无服务器 sql 池 "内置" 及其所有数据库上分配 SQL 角色。

对于其他用户和工作区 MSI，可以使用 SQL 权限来控制对 SQL 池的访问。  分配 SQL 权限要求在创建后在每个 SQL 数据库上运行 SQL 脚本。  有三种情况需要你运行这些脚本：
1. 向其他用户授予对无服务器 SQL 池 "内置" 和它的数据库的访问权限
2. 授予对专用池数据库的任何用户访问权限
3. 向工作区提供对 SQL 池数据库的 MSI 访问权限，以便能够成功运行需要 SQL 池访问的管道。

下面包含了示例 SQL 脚本。

若要授予对专用 SQL 池数据库的访问权限，则工作区创建者或组的任何成员都可以运行脚本 `workspace1_SQLAdmins` 。  

若要授予对无服务器 SQL 池 "内置" 的访问权限，可通过组或组的任何成员运行脚本 `workspace1_SQLAdmins`  `workspace1_SynapseAdministrators` 。 

> [!TIP]
> 需要为 **每个** sql 池运行以下步骤，以便向用户授予对所有 sql 数据库的访问权限，但在部分 [工作区范围权限](#workspace-scoped-permission) 中，你可以在工作区级别向用户分配 sysadmin 角色。

### <a name="step-71-serverless-sql-pool-built-in"></a>步骤7.1：内置的无服务器 SQL 池

本部分提供了一些脚本示例，说明如何向用户授予访问特定数据库或无服务器 SQL 池中 "内置" 的所有数据库的权限。

> [!NOTE]
> 在脚本示例中，将 *alias* 替换为要授予访问权限的用户或组的别名，并将 domain 替换为你正在使用的公司域的 *域* 。

#### <a name="database-scoped-permission"></a>数据库范围的权限

若要向用户授予 **单一** 无服务器 SQL 数据库的访问权限，请执行此示例中的步骤：

1. 创建登录名

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. 创建用户

    ```sql
    use yourdb -- Use your database name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. 将用户添加到指定角色的成员

    ```sql
    use yourdb -- Use your database name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>工作区范围的权限

若要授予对工作区中 **所有** 无服务器 SQL 池的完全访问权限，请使用本示例中的脚本：

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>步骤7.2：专用 SQL 池

若要授予访问 **单个** 专用 SQL 池数据库的权限，请在 Synapse SQL 脚本编辑器中执行以下步骤：

1. 在数据库中创建用户，方法是在目标数据库上运行以下命令，并使用 " *连接到* " 下拉列表进行选择：

    ```sql
    --Create user in the database
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. 向用户授予有权访问数据库的角色：

    ```sql
    --Grant role to the user in the database
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> 如果不需要授予 *db_owner* 权限， *db_datareader* 和 *db_datawriter* 可用于读取/写入权限。
> 要使 Spark 用户能够直接从 Spark 读入或从 SQL 池中写入 Spark，需要 *db_owner* 权限。

创建用户后，运行查询以验证无服务器 SQL 池是否可以查询存储帐户。

### <a name="step-73-sql-access-control-for-synapse-pipeline-runs"></a>步骤7.3： Synapse 管道运行的 SQL 访问控制

### <a name="workspace-managed-identity"></a>工作区托管标识

> [!IMPORTANT]
> 若要成功运行包含引用 SQL 池的数据集或活动的管道，需要向工作区标识授予对 SQL 池的访问权限。

在每个 SQL 池上运行以下命令，以允许工作区托管系统标识在 SQL 池数据库上运行管道 (s) ：  

>[!note]
>在下面的脚本中，对于专用的 SQL 池数据库，databasename 与池名称相同。  对于无服务器 SQL 池中的数据库，databasename 是数据库的名称。

```sql
--Create a SQL user for the workspace MSI in database
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<databasename> TO <workspacename>;
```

针对同一个 SQL 池运行以下脚本可以删除此权限：

```sql
--Revoke permission granted to the workspace MSI
REVOKE CONTROL ON DATABASE::<databasename> TO <workspacename>;

--Delete the workspace MSI user in the database
DROP USER [<workspacename>];
```

## <a name="step-8-add-users-to-security-groups"></a>步骤8：向安全组添加用户

访问控制系统的初始配置已完成。

若要管理访问权限，可以将用户添加到已设置的安全组并将其删除。  虽然你可以手动将用户分配到 Synapse 角色，但如果你这样做，则不会一致地配置其权限。 相反，请只在安全组中添加或删除用户。

## <a name="step-9-network-security"></a>步骤9：网络安全

作为保护工作区的最后一个步骤，你应该确保网络访问安全，使用：
- [工作区防火墙](./synapse-workspace-ip-firewall.md)
- [托管虚拟网络](./synapse-workspace-managed-vnet.md) 
- [专用终结点](./synapse-workspace-managed-private-endpoints.md)
- [专用链接](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>步骤10：完成

你的工作区现在已完全配置并受到保护。

## <a name="supporting-more-advanced-scenarios"></a>支持更高级的方案

本指南重点介绍如何设置基本的访问控制系统。 可以通过创建更多的安全组并在更具体的范围内分配这些组，来支持更高级的方案。 请考虑以下情况：

对于更高级的开发方案（包括 CI/CD），启用对工作区的 **Git 支持**。  在 Git 模式下，Git 权限将确定用户是否可以将更改提交到其工作分支。  仅从协作分支进行发布到服务。  考虑为需要在工作分支中开发和调试更新的开发人员创建安全组，但不需要将更改发布到实时服务。

**限制开发人员** 对特定资源的访问权限。  为只需访问特定资源的开发人员创建更细化的其他安全组。  为这些组分配适用于特定 Spark 池、集成运行时或凭据的适当 Synapse 角色。

**限制操作员访问代码项目**。  为需要监视 Synapse 计算资源和查看日志的操作状态的操作员创建安全组，但不需要访问代码或将更新发布到服务。 将计算操作员角色分配给这些组，使其作用域为特定 Spark 池和集成运行时。  

## <a name="next-steps"></a>后续步骤

了解 [如何管理 SYNAPSE RBAC 角色分配](./how-to-manage-synapse-rbac-role-assignments.md) 创建 [Synapse 工作区](../quickstart-create-workspace.md)