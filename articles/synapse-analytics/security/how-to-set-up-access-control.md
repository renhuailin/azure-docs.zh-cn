---
title: 如何为 Synapse 工作区设置访问控制
description: 本文将介绍如何使用 Azure 角色、Synapse 角色、SQL 权限和 Git 权限控制对 Azure Synapse 工作区的访问。
services: synapse-analytics
author: meenalsri
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 8/05/2021
ms.author: ronytho
ms.reviewer: jrasnick, wiassaf
ms.custom: subject-rbac-steps
ms.openlocfilehash: 513b2edd432a274f155e79362e715fbc426a9f9e
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081503"
---
# <a name="how-to-set-up-access-control-for-your-azure-synapse-workspace"></a>如何为 Azure Synapse 工作区设置访问控制 

本文介绍如何使用 Azure 角色、Azure Synapse 角色、SQL 权限和 Git 权限控制对 Microsoft Azure Synapse 工作区的访问。   

在本指南中，你将设置一个工作区，并配置一个适用于许多 Azure Synapse 项目的基本访问控制系统。  本指南还介绍了更高级的选项，允许你根据需要进行更精细的控制。  

你可以使用与组织中的角色相对应的安全组来简化 Azure Synapse 访问控制。  只需从安全组添加和删除用户即可管理访问权限。

在开始本演练之前，请阅读 [Azure Synapse 访问控制概述](./synapse-workspace-access-control-overview.md)，熟悉 Azure Synapse Analytics 使用的访问控制机制。   

## <a name="access-control-mechanisms"></a>访问控制机制

> [!NOTE]
> 本指南中采用的方法是创建多个安全组，然后将角色分配给这些组。 设置组后，只需管理安全组中的成员身份即可控制对工作区的访问。

为了保护 Azure Synapse 工作区，你需要遵循一种模式来配置以下项：

- **安全组**，用于对具有相似访问要求的用户分组。
- **Azure 角色**，用于控制谁可以创建和管理 SQL 池、Apache Spark 池和集成运行时，以及访问 ADLS Gen2 存储。
- **Synapse 角色**，用于控制对已发布代码项目的访问，以及对 Apache Spark 计算资源和集成运行时的使用 
- **SQL 权限**，用于控制对 SQL 池的管理和数据平面访问。 
- **Git 权限**，用于在为工作区配置 Git-support 的情况下控制谁可以访问源代码管理中的代码项目 
 
## <a name="steps-to-secure-an-azure-synapse-workspace"></a>保护 Azure Synapse 工作区的步骤

本文档使用标准名称来简化说明。 请将它们替换为你选择的名称。

|设置 | 标准名称 | 说明 |
| :------ | :-------------- | :---------- |
| **Synapse 工作区** | `workspace1` |  Azure Synapse 工作区的名称。 |
| **ADLSGEN2 帐户** | `storage1` | 用于工作区的 ADLS 帐户。 |
| **容器** | `container1` | STG1 中的容器，由工作区默认使用。 |
| **Active Directory 租户** | `contoso` | Active Directory 租户名称。|
||||

## <a name="step-1-set-up-security-groups"></a>步骤 1：创建安全组

>[!Note] 
>预览期间，建议创建映射到 Azure Synapse“Synapse SQL 管理员”和“Synapse Apache Spark 管理员”角色的安全组 。  由于引入了新的细化 Synapse RBAC 角色和范围，因此现在我们建议你使用这些新功能来控制对工作区的访问。  这些新角色和范围可提供更大的配置灵活性，并可让你认识到开发人员在创建分析应用程序时经常会混合使用 SQL 和 Spark。你可能需要向它们授予对特定资源（而不是整个工作区）的访问权限。 [详细了解](./synapse-workspace-synapse-rbac.md) Synapse RBAC。

为工作区创建以下安全组：

- **`workspace1_SynapseAdministrators`** ，适用于需要完全控制工作区的用户。  至少在开始的时候将自己添加到此安全组。
- **`workspace1_SynapseContributors`** ，适用于需要开发、调试代码并将其发布到服务的开发人员。   
- **`workspace1_SynapseComputeOperators`** ，适用于需要管理和监视 Apache Spark 池和集成运行时的用户。
- **`workspace1_SynapseCredentialUsers`** ，适用于需要使用工作区 MSI（托管服务标识）凭据来调试和运行业务流程管道以及取消管道运行的用户。   

你很快就需要在工作区范围内将 Synapse 角色分配给这些组。  

另请创建以下安全组： 
- **`workspace1_SQLAdmins`** ，在工作区的 SQL 池中需要 SQL Active Directory 管理员权限的用户的组。 

当你在创建的 SQL 池中配置 SQL 权限时，将使用 `workspace1_SQLAdmins` 组。 

对于基本设置，这五个组足够了。 稍后，你可以添加安全组，以便处理需要更专用访问权限的用户，或向用户提供对特定资源的访问权限。

> [!NOTE]
>- 通过[本文](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)了解如何创建安全组。
>- 通过[本文](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)了解如何添加另一个安全组中的安全组。

>[!Tip]
>单个 Synapse 用户可以使用 Azure 门户中的 Azure Active Directory 来查看其组成员身份，以确定其被授予了哪些角色。

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>步骤 2：准备 ADLS Gen2 存储帐户

Azure Synapse 工作区将默认存储容器用于：
  - 存储 Spark 表的备份数据文件
  - Spark 作业的执行日志
  - 管理你选择安装的库

确定关于存储的以下信息：

- 用于工作区的 ADLS Gen2 帐户。 本文档将它称为 `storage1`。 `storage1` 被视为工作区的“主”存储帐户。
- `workspace1` 中的容器，将由 Synapse 工作区默认使用。 本文档将它称为 `container1`。 
 
- 选择“访问控制 (IAM)”。

- 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。

- 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | 存储 Blob 数据参与者 |
    | 将访问权限分配到 |SERVICEPRINCIPAL |
    | 成员 |workspace1_SynapseAdmins、workspace1_SynapseContributors 和 workspace1_SynapseComputeOperators|

    ![Azure 门户中的“添加角色分配”页。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="step-3-create-and-configure-your-azure-synapse-workspace"></a>第 3 步：创建和配置 Azure Synapse 工作区

在 Azure 门户中，创建 Azure Synapse 工作区：

- 选择订阅

- 选择或创建一个你具有 Azure“所有者”角色的资源组。

- 将工作区命名为 `workspace1`

- 选择“`storage1`”作为“存储帐户”

- 选择“`container1`”作为要用作“文件系统”的容器。

- 在 Synapse Studio 中打开 WS1

- 导航到“管理” > “访问控制”，并在工作区范围内将 Synapse 角色分配给安全组，如下所示： 
  - 将“Synapse 管理员”角色分配给 `workspace1_SynapseAdministrators` 
  - 将“Synapse 参与者”角色分配给 `workspace1_SynapseContributors` 
  - 将“Synapse 计算操作员”角色分配给 `workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>步骤 4：向工作区 MSI 授予对默认存储容器的访问权限

为了运行管道和执行系统任务，Azure Synapse 会要求工作区托管服务标识 (MSI) 访问默认 ADLS Gen2 帐户中的 `container1`。 有关详细信息，请参阅 [Azure Synapse 工作区托管标识](synapse-workspace-managed-identity.md)。

- 打开 Azure 门户
- 找到存储帐户 `storage1`，然后找到 `container1`
- 选择“访问控制 (IAM)”。
- 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。
- 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | 存储 Blob 数据参与者 |
    | 将访问权限分配到 | MANAGEDIDENTITY |
    | 成员 | 托管标识名称  |

    > [!NOTE]
    > 托管标识名称也是工作区名称。

    ![Azure 门户中的“添加角色分配”页。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>步骤 5：向 Synapse 管理员授予工作区中的“Azure 参与者”角色 

若要创建 SQL 池、Apache Spark 池和集成运行时，用户必须至少具有工作区的“Azure 参与者”角色。 参与者角色还允许这些用户管理资源，包括执行暂停和缩放操作。 如果使用 Azure 门户或 Synapse Studio 来创建 SQL 池、Apache Spark 池和集成运行时，则需要资源组级的“Azure 参与者”角色。 

- 打开 Azure 门户
- 找到工作区 `workspace1`
- 选择“访问控制 (IAM)”。
- 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。
- 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | 参与者 |
    | 将访问权限分配到 | SERVICEPRINCIPAL |
    | 成员 | workspace1_SynapseAdministrators  |

    ![Azure 门户中的“添加角色分配”页。](../../../includes/role-based-access-control/media/add-role-assignment-page.png) 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>步骤 6：分配“SQL Active Directory 管理员”角色

工作区创建者会被自动设置为工作区的 SQL Active Directory 管理员。  只能为单个用户或组授予此角色。 在此步骤中，你将工作区中的 SQL Active Directory 管理员分配给 `workspace1_SQLAdmins` 安全组。  分配此角色就是向此组授予对工作区中的所有 SQL 池和数据库的特权很高的管理员访问权限。   

- 打开 Azure 门户
- 导航到 `workspace1`
- 在“设置”下，选择“SQL Active Directory 管理员” 
- 选择“设置管理员”，然后选择“`workspace1_SQLAdmins`”

>[!Note]
>步骤 6 是可选的。  你可以选择向 `workspace1_SQLAdmins` 组授予特权较低的角色。 若要分配 `db_owner` 或其他 SQL 角色，必须在每个 SQL 数据库上运行脚本。 

## <a name="step-7-grant-access-to-sql-pools"></a>步骤 7：授予对 SQL 池的访问权限

默认情况下，所有被分配了 Synapse 管理员角色的用户也会被分配工作区中专用和无服务器 SQL 池上的 SQL `db_owner` 角色。

对于其他用户和工作区 MSI，可以使用 SQL 权限来控制对 SQL 池的访问。  分配 SQL 权限要求在每个已创建的 SQL 数据库上运行 SQL 脚本。  下面三种情况需要你运行这些脚本：
1. 向其他用户授予对无服务器 SQL 池、“Built-in”及其数据库的访问权限
2. 授予任何用户访问专用 SQL 池数据库的权限
3. 向工作区 MSI 授予对 SQL 池数据库的访问权限，使那些需要 SQL 池访问权限的管道能够成功运行。

下面包含了示例 SQL 脚本。

若要授予对专用 SQL 池数据库的访问权限，工作区创建者或 `workspace1_SQLAdmins` 组或 `workspace1_SynapseAdministrators` 组的任何成员都可以运行脚本。  

若要授予对无服务器 SQL 池“Built-in”的访问权限，可以让 `workspace1_SQLAdmins` 组或 `workspace1_SynapseAdministrators` 组的任何成员运行这些脚本。 

> [!TIP]
> 需要为每个 SQL 池运行以下步骤，以向用户授予对所有 SQL 数据库的访问权限（[工作区范围的权限](#workspace-scoped-permission)部分中的除外，其中可为用户分配工作区级别的 sysadmin 角色）。

### <a name="step-71-serverless-sql-pool-built-in"></a>步骤 7.1：无服务器 SQL 池“Built-in”

本部分提供了一些脚本示例，这些示例说明了如何向用户授予访问特定数据库或访问无服务器 SQL 池“Built-in”中的所有数据库的权限。

> [!NOTE]
> 在脚本示例中，请将 alias 替换为要向其授予访问权限的用户或组的别名，并将 domain 替换为你要使用的公司域。

#### <a name="database-scoped-permission"></a>数据库范围的权限

若要向用户授予对单个无服务器 SQL 数据库的访问权限，请执行此示例中的步骤：

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

若要授予对工作区中 **所有** 无服务器 SQL 池的完全访问权限，请使用此示例中的脚本：

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>步骤 7.2：专用 SQL 池

若要授予访问单个专用 SQL 池数据库的权限，请在 Azure Synapse SQL 脚本编辑器中执行以下步骤：

1. 在数据库中创建用户，方法是：在使用“连接到”下拉列表选择的目标数据库上运行以下命令：

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
> 如果授予 db_owner 权限不符合需要，可通过 db_datareader 和 db_datawriter 来授予读取/写入权限。  
> 要使 Spark 用户能够直接使用 Spark 在 SQL 池中读取和写入数据，需要授予 db_owner 权限。

创建用户后，请运行查询以验证无服务器 SQL 池是否可以查询存储帐户。

### <a name="step-73-sql-access-control-for-azure-synapse-pipeline-runs"></a>步骤 7.3：Azure Synapse 管道运行的 SQL 访问控制

### <a name="workspace-managed-identity"></a>工作区托管标识

> [!IMPORTANT]
> 若要成功运行包含引用 SQL 池的数据集或活动的管道，需要向工作区标识授予对 SQL 池的访问权限。

有关工作区托管标识的详细信息，请参阅 [Azure Synapse 工作区托管标识](synapse-workspace-managed-identity.md)。 请针对每个 SQL 池运行以下命令，以允许工作区托管系统标识对 SQL 池数据库运行管道：  

>[!note]
>在下面脚本中，对于专用 SQL 池数据库，`<databasename>` 与池名称相同。  对于无服务器 SQL 池“Built-in”中的数据库，`<databasename>` 是数据库的名称。

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

## <a name="step-8-add-users-to-security-groups"></a>步骤 8：向安全组添加用户

访问控制系统的初始配置已完成。

若要管理访问权限，可以在设置的安全组中添加和删除用户。  尽管可以手动将用户分配到 Azure Synapse 角色，但是如果你这样做，将会导致权限配置不一致。 相反，请只在安全组中添加或删除用户。

## <a name="step-9-network-security"></a>步骤 9：网络安全

作为保护工作区的最后一步，应该使用[工作区防火墙](./synapse-workspace-ip-firewall.md)保护网络访问。

- 无论是否使用[托管虚拟网络](./synapse-workspace-managed-vnet.md)，都可以从公共网络连接到工作区。 有关详细信息，请参阅[连接性设置](connectivity-settings.md)。
- 通过启用[公用网络访问功能](connectivity-settings.md#public-network-access)或[工作区防火墙](./synapse-workspace-ip-firewall.md)，可以控制来自公用网络的访问。
- 或者，可以使用[托管专用终结点](synapse-workspace-managed-private-endpoints.md)和[专用链接](../../azure-sql/database/private-endpoint-overview.md)来连接到工作区。 没有 [Azure Synapse Analytics 托管虚拟网络](synapse-workspace-managed-vnet.md)的 Azure Synapse 工作区无法通过托管专用终结点进行连接。

## <a name="step-10-completion"></a>步骤 10：完成

你的工作区现在已完全配置并受到保护。

## <a name="supporting-more-advanced-scenarios"></a>支持更高级的方案

本指南重点介绍了如何设置基本的访问控制系统。 你可以通过创建更多的安全组并在更具体的范围内为这些组分配更细化的角色，来支持更高级的方案。 请考虑以下情况：

对于更高级的开发方案（包括 CI/CD），为工作区启用 Git-support。  在 Git 模式下时，Git 权限将决定用户是否可以提交对其工作分支的更改。  只能从协作分支发布到服务。  考虑为需要在工作分支中开发和调试更新的开发人员创建安全组，但不需要将更改发布到实时服务。

限制开发人员对特定资源的访问权限。  为只需访问特定资源的开发人员创建其他更细化的安全组。  为这些组分配适当的、权利范围为特定 Azure Spark 池、集成运行时或凭据的 Synapse 角色。

限制操作员访问代码项目。  为需要监视 Synapse 计算资源的运行状态和查看日志但不需要访问代码或将更新发布到服务的操作员创建安全组。 将权力范围为特定 Spark 池和集成运行时的“计算操作员”角色分配给这些组。  

## <a name="next-steps"></a>后续步骤

 - 了解[如何管理 Azure Synapse RBAC 角色分配](./how-to-manage-synapse-rbac-role-assignments.md)
 - 创建 [Synapse 工作区](../quickstart-create-workspace.md)
