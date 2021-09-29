---
title: 将 Azure Synapse Analytics 工作区从一个区域移到另一个区域
description: 本文介绍如何将 Azure Synapse Analytics 工作区从一个区域移到另一个区域。
author: phanir
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 08/16/2021
ms.author: phanir
ms.reviewer: jrasnick
ms.openlocfilehash: 6ad246eefb86c31291d2a9745c6f77e276701744
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214078"
---
# <a name="move-an-azure-synapse-analytics-workspace-from-one-region-to-another"></a>将 Azure Synapse Analytics 工作区从一个区域移到另一个区域

本文是一篇分步指南，介绍如何将 Azure Synapse Analytics 工作区从一个 Azure 区域移到另一个区域。

> [!NOTE]
> 本文中的步骤实际上并未移动工作区。 这些步骤展示了如何使用源区域中的 Azure Synapse Analytics 专用 SQL 池备份和项目在新区域中创建新工作区。

## <a name="prerequisites"></a>先决条件

- 将源区域 Azure Synapse 工作区与 Azure DevOps 或 GitHub 集成。 有关详细信息，请参阅 [Synapse Studio 中的源代码管理](cicd/source-control.md)。
- 在执行脚本的服务器上安装 [Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-6.3.0&preserve-view=true) 和 [Azure CLI](/cli/azure/install-azure-cli) 模块。
- 确保在目标区域中重新创建所有从属服务（例如 Azure 机器学习、Azure 存储和 Azure 专用链接中心），或将其移到目标区域（如果服务支持区域移动）。 
- 将 Azure 存储移到其他区域。 有关详细信息，请参阅[将 Azure 存储帐户移到另一区域](../storage/common/storage-account-move.md)。
- 确保源区域和目标区域工作区中的专用 SQL 池名称和 Apache Spark 池名称相同。

## <a name="scenarios-for-a-region-move"></a>区域移动场景

- **新的合规性要求**：作为新合规性要求的一部分，组织要求将数据和服务放置在同一区域。
- **新 Azure 区域的可用性**：新的 Azure 区域可用，并且有项目或业务要求将工作区和其他 Azure 资源移到新的可用 Azure 区域。
- **选择了错误的区域**：创建 Azure 资源时选择了错误的区域。

## <a name="steps-to-move-an-azure-synapse-workspace-to-another-region"></a>将 Azure Synapse 工作区移到另一个区域的步骤

将 Azure Synapse 工作区从一个区域移到另一个区域是一个多步骤过程。 下面是概要步骤：

1. 在目标区域中创建一个新的 Azure Synapse 工作区，同时创建一个 Spark 池，其配置与源区域工作区中使用的配置相同。
1. 使用还原点或异地备份将专用 SQL 池还原到目标区域。
1. 在新的逻辑 SQL Server 上重新创建所有必需的登录名。
1. 创建无服务器 SQL 池和 Spark 池数据库和对象。
1. 如果要使用 Azure DevOps 发布管道来部署项目，请将 Azure DevOps 服务主体添加到 Azure Synapse 基于角色的访问控制 (RBAC) Synapse 项目发布者角色。
1. 将代码项目（SQL 脚本、笔记本）、链接服务、管道、数据集、Spark 作业定义触发器和凭据从 Azure DevOps 发布管道部署到目标区域 Azure Synapse 工作区。
1. 将 Azure Active Directory (Azure AD) 用户或组添加到 Azure Synapse RBAC 角色。 如果要使用托管标识进行身份验证，请授权存储 Blob 参与者访问 Azure 存储和 Azure Key Vault 上系统分配的托管标识 (SA-MI)。
1. 在默认附加存储或包含要使用无服务器 SQL 池查询的数据的存储帐户上，向所需的 Azure AD 用户授予“存储 Blob 读取者”或“存储 Blob 参与者”角色。
1. 重新创建自承载集成运行时 (SHIR)。
1. 在目标 Azure Synapse 工作区中手动上传所有必需的库和 jar。
1. 如果工作区部署在托管虚拟网络中，则创建所有托管专用终结点。
1. 测试目标区域中的新工作区，并更新指向源区域工作区的所有 DNS 条目。
1. 如果在源工作区中创建了专用终结点连接，请在目标区域工作区中也创建一个。
1. 在对源区域中的工作区进行全面测试并将所有连接路由到目标区域工作区之后，可以删除源区域中的工作区。
## <a name="prepare"></a>准备
## <a name="step-1-create-an-azure-synapse-workspace-in-a-target-region"></a>步骤 1：在目标区域中创建 Azure Synapse 工作区

在本部分中，你将使用 Azure PowerShell、Azure CLI 和 Azure 门户创建 Azure Synapse 工作区。 你将创建一个资源组和一个 Azure Data Lake Storage Gen2 帐户，该帐户将作为 PowerShell 脚本和 CLI 脚本的一部分，用作工作区的默认存储。 若要自动执行部署过程，请从 DevOps 发布管道调用这些 PowerShell 或 CLI 脚本。

### <a name="azure-portal"></a>Azure 门户
若要从 Azure 门户创建工作区，请按照[快速入门：创建 Synapse 工作区](quickstart-create-workspace.md)中的步骤操作。

### <a name="azure-powershell"></a>Azure PowerShell 
以下脚本使用 New-AzResourceGroup 和 New-AzSynapseWorkspace cmdlet 创建资源组和 Azure Synapse 工作区。

#### <a name="create-a-resource-group"></a>创建资源组

```powershell
$storageAccountName= "<YourDefaultStorageAccountName>"
$resourceGroupName="<YourResourceGroupName>"
$regionName="<YourTargetRegionName>"
$containerName="<YourFileSystemName>" # This is the file system name
$workspaceName="<YourTargetRegionWorkspaceName>"

$sourcRegionWSName="<Your source region workspace name>"
$sourceRegionRGName="<YourSourceRegionResourceGroupName>"
$sqlUserName="<SQLUserName>"
$sqlPassword="<SQLStrongPassword>"

$sqlPoolName ="<YourTargetSQLPoolName>" #Both Source and target workspace SQL pool name will be same
$sparkPoolName ="<YourTargetWorkspaceSparkPoolName>"
$sparkVersion="2.4"

New-AzResourceGroup -Name $resourceGroupName -Location $regionName
```

#### <a name="create-a-data-lake-storage-gen2-account"></a>创建 Data Lake Storage Gen2 帐户

```powershell
#If the Storage account is already created, then you can skip this step.
New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location  $regionName `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $true 
```


#### <a name="create-an-azure-synapse-workspace"></a>创建 Azure Synapse 工作区

```powershell
$password = ConvertTo-SecureString $sqlPassword -AsPlainText -Force
$creds = New-Object System.Management.Automation.PSCredential ($sqlUserName, $password)

New-AzSynapseWorkspace -ResourceGroupName $resourceGroupName `
                        -Name $workspaceName -Location $regionName `
                        -DefaultDataLakeStorageAccountName $storageAccountName `
                        -DefaultDataLakeStorageFilesystem $containerName `
                        -SqlAdministratorLoginCredential $creds 
```

若要使用托管虚拟网络创建工作区，请向该脚本添加额外的参数“ManagedVirtualNetwork”。 若要详细了解可用的选项，请参阅[托管虚拟网络配置](/powershell/module/az.synapse/new-azsynapsemanagedvirtualnetworkconfig?view=azps-6.3.0&preserve-view=true)。


```powershell
#Creating a managed virtual network configuration
$config = New-AzSynapseManagedVirtualNetworkConfig -PreventDataExfiltration -AllowedAadTenantIdsForLinking ContosoTenantId 

#Creating an Azure Synapse workspace
New-AzSynapseWorkspace -ResourceGroupName $resourceGroupName `
                        -Name $workspaceName -Location $regionName `
                        -DefaultDataLakeStorageAccountName $storageAccountName `
                        -DefaultDataLakeStorageFilesystem $containerName `
                        -SqlAdministratorLoginCredential $creds `
                              -ManagedVirtualNetwork $config
```


### <a name="azure-cli"></a>Azure CLI

此 Azure CLI 脚本将创建资源组、Data Lake Storage Gen2 帐户和文件系统。 然后，创建 Azure Synapse 工作区。

#### <a name="create-a-resource-group"></a>创建资源组

```azurecli
az group create --name $resourceGroupName --location $regionName
```

#### <a name="create-a-data-lake-storage-gen2-account"></a>创建 Data Lake Storage Gen2 帐户

```azurecli
# Checking if name is not used only then creates it.

$StorageAccountNameAvailable=(az storage account check-name --name $storageAccountName --subscription $subscriptionId | ConvertFrom-Json).nameAvailable

if($StorageAccountNameAvailable)
{
Write-Host "Storage account Name is available to be used...creating storage account"

#Creating an Data Lake Storage Gen2 account
$storgeAccountProvisionStatus=az storage account create `
  --name $storageAccountName `
  --resource-group $resourceGroupName `
  --location $regionName `
  --sku Standard_GRS `
  --kind StorageV2 `
  --enable-hierarchical-namespace $true

($storgeAccountProvisionStatus| ConvertFrom-Json).provisioningState
}
else
{
    Write-Host "Storage account Name is NOT available to be used...use another name --    exiting the script..."
    EXIT
}

#Creating a container in a Data Lake Storage Gen2 account

$key=(az storage account keys list -g $resourceGroupName -n $storageAccountName|ConvertFrom-Json)[0].value

$fileShareStatus=(az storage share create --account-name $storageAccountName --name $containerName --account-key $key)

if(($fileShareStatus|ConvertFrom-Json).created -eq "True")
{
      Write-Host f"Successfully created the fileshare - '$containerName'"
}
```


#### <a name="create-an-azure-synapse-workspace"></a>创建 Azure Synapse 工作区

```azurecli
az synapse workspace create `
  --name $workspaceName `
  --resource-group $resourceGroupName `
  --storage-account $storageAccountName `
  --file-system $containerName `
  --sql-admin-login-user $sqlUserName `
  --sql-admin-login-password $sqlPassword `
  --location $regionName
```
若要启用托管虚拟网络，请在上述脚本中包含参数 `--enable-managed-virtual-network`。 有关更多选项，请参阅[工作区托管虚拟网络](/cli/azure/synapse/workspace?view=azure-cli-latest&preserve-view=true)。

```azurecli
az synapse workspace create `
  --name $workspaceName `
  --resource-group $resourceGroupName `
  --storage-account $storageAccountName `
  --file-system $FileShareName `
  --sql-admin-login-user $sqlUserName `
  --sql-admin-login-password $sqlPassword `
  --location $regionName `
  --enable-managed-virtual-network true `
  --allowed-tenant-ids "Contoso"
```

## <a name="step-2-create-an-azure-synapse-workspace-firewall-rule"></a>步骤 2：创建 Azure Synapse 工作区防火墙规则 
创建工作区后，为工作区添加防火墙规则。 将 IP 限制在一定范围内。 可以从 Azure 门户或者使用 PowerShell 或 CLI 添加防火墙。

### <a name="azure-portal"></a>Azure 门户
选择防火墙选项并添加 IP 地址范围，如下面的屏幕截图所示。

:::image type="icon" source="media/how-to-move-workspace-from-one-region-to-another/firewall.png" border="false":::


### <a name="azure-powershell"></a>Azure PowerShell 
运行以下 PowerShell 命令，通过指定开始和结束 IP 地址来添加防火墙规则。 根据你的要求更新 IP 地址范围。


```powershell
$WorkspaceWeb = (Get-AzSynapseWorkspace -Name $workspaceName -ResourceGroupName $resourceGroup).ConnectivityEndpoints.Web
$WorkspaceDev = (Get-AzSynapseWorkspace -Name $workspaceName -ResourceGroupName $resourceGroup).ConnectivityEndpoints.Dev

# Adding firewall rules
$FirewallParams = @{
  WorkspaceName = $workspaceName
  Name = 'Allow Client IP'
  ResourceGroupName = $resourceGroup
  StartIpAddress = "0.0.0.0"
  EndIpAddress = "255.255.255.255"
}
New-AzSynapseFirewallRule @FirewallParams
```

运行以下脚本以更新工作区的托管标识 SQL 控制设置：

```powershell 
Set-AzSynapseManagedIdentitySqlControlSetting -WorkspaceName $workspaceName -Enabled $true 
```

### <a name="azure-cli"></a>Azure CLI


```azurecli
az synapse workspace firewall-rule create --name allowAll --workspace-name $workspaceName  `
--resource-group $resourceGroupName --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
 
运行以下脚本以更新工作区的托管标识 SQL 控制设置：

```azurecli
az synapse workspace managed-identity grant-sql-access `
--workspace-name $workspaceName --resource-group $resourceGroupName
```


## <a name="step-3-create-an-apache-spark-pool"></a>步骤 3：创建 Apache Spark 池

使用源区域工作区中所用的配置来创建 Spark 池。

### <a name="azure-portal"></a>Azure 门户

若要从 Azure 门户创建 Spark 池，请参阅[快速入门：使用 Azure 门户新建无服务器 Apache Spark 池](quickstart-create-apache-spark-pool-portal.md)。


你还可以按照[快速入门：使用 Synapse Studio 创建无服务器 Apache Spark 池](quickstart-create-apache-spark-pool-studio.md)中的步骤，从 Synapse Studio 创建 Spark 池。

### <a name="azure-powershell"></a>Azure PowerShell

以下脚本将创建一个 Spark 池，其中包含两个工作器节点和一个驱动程序节点。 更新值，使其与源区域工作区 Spark 池匹配。


```powershell
#Creating a Spark pool with 3 nodes (2 worker + 1 driver) and a small cluster size with 4 cores and 32 GB RAM. 
New-AzSynapseSparkPool `
    -WorkspaceName  $workspaceName `
    -Name $sparkPoolName `
    -NodeCount 3 `
    -SparkVersion $sparkVersion `
    -NodeSize Small
```
 
### <a name="azure-cli"></a>Azure CLI

```azurecli
az synapse spark pool create --name $sparkPoolName --workspace-name $workspaceName --resource-group $resourceGroupName `
--spark-version $sparkVersion --node-count 3 --node-size small
```
## <a name="move"></a>移动
## <a name="step-4-restore-a-dedicated-sql-pool"></a>步骤 4：还原专用 SQL 池 

### <a name="restore-from-geo-redundant-backups"></a>从异地冗余备份中还原

若要使用 Azure 门户和 PowerShell 从异地备份中还原专用 SQL 池，请参阅[在 Azure Synapse Analytics 中异地还原专用 SQL 池](sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup.md)。


### <a name="restore-by-using-restore-points-from-the-source-region-workspace-dedicated-sql-pool"></a>使用源区域工作区专用 SQL 池中的还原点进行还原

使用源区域工作区专用 SQL 池的还原点将专用 SQL 池还原到目标区域工作区。 可以使用 Azure 门户、Synapse Studio 或 PowerShell 从还原点进行还原。 如果源区域不可访问，则无法使用此选项进行还原。

#### <a name="synapse-studio"></a>Synapse Studio

在 Synapse Studio 中，可以使用还原点从订阅中的任何工作区还原专用 SQL 池。 创建专用 SQL 池时，在“其他设置”下，选择“还原点”并选择工作区，如下面的屏幕截图所示。 如果创建了用户定义的还原点，请使用它来还原 SQL 池。 否则，可以选择最新的自动还原点。

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/restore-sql-pool.png" alt-text="还原 SQL 池":::


#### <a name="azure-powershell"></a>Azure PowerShell

运行以下 PowerShell 脚本还原工作区。 此脚本使用源工作区专用 SQL 池中的最新还原点，在目标工作区中还原 SQL 池。 在运行该脚本之前，请将性能级别从 DW100c 更新为所需的值。 

> [!IMPORTANT]
> 两个工作区中的专用 SQL 池名称应该相同。


```powershell
#Getting the restore points
$restorePoint=Get-AzSynapseSqlPoolRestorePoint -WorkspaceName $sourceRegionWSName -Name $sqlPoolName|Sort-Object  -Property RestorePointCreationDate -Descending `
                                                                                         | SELECT RestorePointCreationDate -ExpandProperty  RestorePointCreationDate -First 1
```
 
 

```powershell
<#
Transform Synapse SQL pool resource ID to SQL database ID because currently the command only accepts the SQL database ID. 
For example: /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Sql/servers/<WorkspaceName>/databases/<DatabaseName>
#>
$pool = Get-AzSynapseSqlPool -ResourceGroupName $sourceRegionRGName -WorkspaceName $sourcRegionWSName -Name $sqlPoolName
$databaseId = $pool.Id `
    -replace "Microsoft.Synapse", "Microsoft.Sql" `
    -replace "workspaces", "servers" `
    -replace "sqlPools", "databases" 
 

$restoredPool = Restore-AzSynapseSqlPool -FromRestorePoint `
                                         -RestorePoint $restorePoint `
                                         -TargetSqlPoolName $sqlPoolName `
                                         -ResourceGroupName $resourceGroupName `
                                         -WorkspaceName $workspaceName `
                                         -ResourceId $databaseId `
                                         -PerformanceLevel DW100c -AsJob


#Tracks the status of the restore 

Get-Job | Where-Object Command -In ("Restore-AzSynapseSqlPool") | `
Select-Object Id,Command,JobStateInfo,PSBeginTime,PSEndTime,PSJobTypeName,Error |Format-Table
```
还原专用 SQL 池后，在 Azure Synapse 中创建所有 SQL 登录名。 若要创建所有登录名，请按照[创建登录名](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true)中的步骤操作。

## <a name="step-5-create-a-serverless-sql-pool-spark-pool-database-and-objects"></a>步骤 5：创建无服务器 SQL 池、Spark 池数据库和对象

你无法备份和还原无服务器 SQL 池数据库和 Spark 池。 作为一种可能的解决方法，你可以：

1. 创建笔记本和 SQL 脚本，其中包含用于重新创建所有所需 Spark 池、无服务器 SQL 池数据库、表、角色和具有所有角色分配的用户的代码。 将这些项目签入 Azure DevOps 或 GitHub。
1. 如果存储帐户名称已更改，请确保代码项目指向正确的存储帐户名称。
1. 创建管道，以特定顺序调用这些代码项目。 在目标区域工作区中执行这些管道时，将在目标区域工作区中创建 Spark SQL 数据库、无服务器 SQL 池数据库、外部数据源、视图、角色以及用户和权限。
1. 将源区域工作区与 Azure DevOps 集成时，这些代码项目将成为存储库的一部分。 稍后，你可以使用步骤 6 中提到的 DevOps 发布管道将这些代码项目部署到目标区域工作区。
1. 在目标区域工作区中，手动触发这些管道。

## <a name="step-6-deploy-artifacts-and-pipelines-by-using-cicd"></a>步骤 6：使用 CI/CD 部署项目和管道 

 若要了解如何将 Azure Synapse 工作区与 Azure DevOps 或 GitHub 集成，以及如何将项目部署到目标区域工作区，请按照 [Azure Synapse 工作区的持续集成和持续交付 (CI/CD)](cicd/continuous-integration-delivery.md) 中的步骤操作。 

将工作区与 Azure DevOps 集成后，你会发现一个名为 workspace_publish 的分支。 此分支包含工作区模板，模板中包括笔记本、SQL 脚本、数据集、链接服务、管道、触发器和 Spark 作业定义等项目的定义。

这个来自 Azure DevOps 存储库的屏幕截图显示了项目和其他组件的工作区模板文件。

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/devops-repo-workspace-publish.png" alt-text="显示 workspace_publish 的屏幕截图。":::

可以使用工作区模板通过 Azure DevOps 发布管道将项目和管道部署到工作区，如下面的屏幕截图所示。

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/release-pipeline.png" alt-text="显示 synapse-release-pipeline 的屏幕截图。":::

如果工作区未与 GitHub 或 Azure DevOps 集成，则必须手动重新创建或编写自定义 PowerShell 或 Azure CLI 脚本，以将所有项目、管道、链接服务、凭据、触发器和 Spark 定义部署到目标区域工作区。


> [!NOTE]
> 此过程要求你不断更新管道和代码项目，以包含对源区域工作区中的 Spark 和无服务器 SQL 池、对象和角色所做的任何更改。

## <a name="step-7-create-a-shared-integration-runtime"></a>步骤 7：创建共享集成运行时

若要创建 SHIR，请按照[创建和配置自承载集成运行时](../data-factory/create-self-hosted-integration-runtime.md)中的步骤操作。

## <a name="step-8-assign-an-azure-role-to-managed-identity"></a>步骤 8：向托管标识分配 Azure 角色

 为默认附加 Data Lake Storage Gen2 帐户上的新工作区的托管标识分配 `Storage Blob Contributor` 访问权限。 同时在使用 SA-MI 进行身份验证的其他存储帐户上分配访问权限。 为所有必需的存储帐户的 Azure AD 用户和组分配 `Storage Blob Contributor` 或 `Storage Blob Reader` 访问权限。

### <a name="azure-portal"></a>Azure 门户
按照[向工作区托管标识授予权限](security/how-to-grant-workspace-managed-identity-permissions.md)中的步骤将“存储 Blob 数据参与者”角色分配给工作区的托管标识。

### <a name="azure-powershell"></a>Azure PowerShell
将“存储 Blob 数据参与者”角色分配给工作区的托管标识。

```powershell

$workSpaceIdentityObjectID= (Get-AzSynapseWorkspace -ResourceGroupName $resourceGroupName -Name $workspaceName).Identity.PrincipalId 
$scope = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Storage/storageAccounts/$($storageAccountName)"

# Adding Storage Blob Data Contributor to WS Managed Identity on the storage account. This errors out with the message New-AzRoleAssignment : Exception of type 'Microsoft.Rest.Azure.CloudException' was thrown.
# But it creates the required permissions on the storage account.
$roleAssignedforManagedIdentity=New-AzRoleAssignment -ObjectId $workSpaceIdentityObjectID `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope $scope -ErrorAction SilentlyContinue
```


### <a name="azure-cli"></a>Azure CLI

```azurecli
# Getting Role name
$roleName =az role definition list --query "[?contains(roleName, 'Storage Blob Data Contributor')].{roleName:roleName}" --output tsv

#Getting resource id for storage account
$scope= (az storage account show --name $storageAccountName|ConvertFrom-Json).id

#Getting principal ID for WS Managed Identity
$workSpaceIdentityObjectID=(az synapse workspace show --name $workspaceName --resource-group $resourceGroupName|ConvertFrom-Json).Identity.PrincipalId 
                    
# Adding Storage Blob Data Contributor Azure role to SA-MI
az role assignment create --assignee $workSpaceIdentityObjectID `
--role $roleName `
--scope $scope
```

## <a name="step-9-assign-azure-synapse-rbac-roles"></a>步骤 9：分配 Azure Synapse RBAC 角色

为所有需要访问目标工作区的用户添加单独的角色和权限。 以下 PowerShell 和 CLI 脚本可将 Azure AD 用户添加为目标区域工作区中的 Synapse 管理员角色。 若要获取所有 Azure Synapse RBAC 角色名称，请参阅 [Azure Synapse RBAC 角色](security/synapse-workspace-synapse-rbac-roles.md)。

### <a name="synapse-studio"></a>Synapse Studio

若要在 Synapse Studio 中添加或删除 Azure Synapse RBAC 分配，请按照[如何在 Synapse Studio 中管理 Azure Synapse RBAC 角色分配](security/how-to-manage-synapse-rbac-role-assignments.md)中的步骤操作。


### <a name="azure-powershell"></a>Azure PowerShell

以下 PowerShell 脚本可将 Synapse 管理员角色分配添加到 Azure AD 用户或组。 你可以在以下命令中使用 -RoleDefinitionId 来取代 -RoleDefinitionName，以将用户添加到工作区：

```powershell
# Add the Synapse RBAC assignment. Use the objectId of the Azure AD user or group you want to assign.
New-AzSynapseRoleAssignment `
   -WorkspaceName $workspaceName  `
   -RoleDefinitionName "Synapse Administrator" `
   -ObjectId 1c02d2a6-ed3d-46ec-b578-6f36da5819c6

# Check if user is added to the access control by running this command.
Get-AzSynapseRoleAssignment -WorkspaceName $workspaceName  
```

若要获取源区域工作区中的 ObjectId 和 RoleId，请运行 Get-AzSynapseRoleAssignment 命令。 将相同的 Azure Synapse RBAC 角色分配给目标区域工作区中的 Azure AD 用户或组。

你还可以使用 -SignInName 作为参数来取代 -ObjectId，并在其中提供用户的电子邮件地址或用户主体名称。 若要详细了解可用的选项，请参阅 [Azure Synapse RBAC - PowerShell cmdlet](/powershell/module/az.synapse/new-azsynapseroleassignment?view=azps-6.3.0&preserve-view=true)。 

### <a name="azure-cli"></a>Azure CLI

```azurecli
#Get the Object Id of the user and assign the required Azure Synapse RBAC permissions to the Azure AD user. You can provide the email address of the user (username@contoso.com) for the --assignee parameter.
az synapse role assignment create `
--workspace-name $workspaceName `
--role "Synapse Administrator" --assignee adasdasdd42-0000-000-xxx-xxxxxxx

az synapse role assignment create `
--workspace-name $workspaceName `
--role "Synapse Contributor" --assignee "user1@contoso.com"

```

若要详细了解可用的选项，请参阅 [Azure Synapse RBAC - CLI](/cli/azure/synapse/role/assignment?view=azure-cli-latest&preserve-view=true)。 

## <a name="step-10-upload-workspace-packages"></a>步骤 10：上传工作区包

将所有必需的工作区包上传到新工作区。 若要自动执行上传工作区包的过程，请参阅 [Microsoft Azure Synapse Analytics Artifacts 客户端库](https://www.nuget.org/packages/Azure.Analytics.Synapse.Artifacts/1.0.0-preview.10)。

## <a name="step-11-permissions"></a>步骤 11：权限
    
若要为目标区域 Azure Synapse 工作区设置访问控制，请按照[如何为 Azure Synapse 工作区设置访问控制](security/how-to-set-up-access-control.md)中的步骤操作。 


## <a name="step-12-create-managed-private-endpoints"></a>步骤 12：创建托管专用终结点

若要在目标区域工作区中重新创建源区域工作区中的托管专用终结点，请参阅[创建数据源的托管专用终结点](security/how-to-create-managed-private-endpoints.md)。 

## <a name="discard"></a>弃用
如果你要放弃目标区域工作区，请删除目标区域工作区。 为此，请从门户中的仪表板转到资源组，选择该工作区，然后选择“资源组”页顶部的“删除”。

## <a name="clean-up"></a>清理
若要提交更改并完成工作区移动，请在测试目标区域中的工作区后删除源区域工作区。 为此，请从门户中的仪表板转到具有源区域工作区的资源组，选择该工作区，然后选择“资源组”页顶部的“删除”。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Synapse 托管虚拟网络](security/synapse-workspace-managed-vnet.md)。
- 详细了解 [Azure Synapse 托管专用终结点](security/synapse-workspace-managed-private-endpoints.md)。
- 详细了解如何[从受限网络连接到工作区资源](security/how-to-connect-to-workspace-from-restricted-network.md)。
