---
title: 使用 Azure Purview 标签对 Azure SQL 数据进行分类
description: 从 Azure SQL 数据库中的 Azure Purview 和 Azure Synpase Analytics 导入分类
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurepowershell
ms.topic: sample
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
ms.openlocfilehash: d202a931e7af1dc212e3ab688580fd38c64dbdcb
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653741"
---
# <a name="classify-your-azure-sql-data-using-azure-purview-labels"></a>使用 Azure Purview 标签对 Azure SQL 数据进行分类
[!INCLUDE[appliesto-sqldb-asa](../../azure-sql/includes/appliesto-sqldb-asa.md)]

本文档介绍如何在 Azure SQL 数据库和 Azure Synapse Analytics（以前称为 SQL DW）中添加 Azure Purview 标签。

## <a name="create-an-application"></a>创建应用程序

1. 在 Azure 门户中，打开“Azure Active Directory”。
2. 在“管理”下，选择“应用注册” 。
3. 通过选择“新建应用程序”创建新的 Azure Active Directory 应用。
4. 输入应用程序的名称并选择“注册”。
5. 创建应用程序后，在“管理器”下打开“证书和机密” 。
6. 通过选择“客户端机密”下的“新建客户端机密”来创建新的客户端机密 。
7. 向客户端机密添加说明，选择过期时间，然后选择“添加”。
8. 保留此值供将来使用。

   > [!NOTE]
   > 关闭该页面后，该值将被屏蔽。 如果返回到该页面，你将无法检索客户端机密的值。 而必须生成新的客户端机密。

9. 返回到新创建的应用程序的概述页面，并复制以下值以供将来使用：
    1. 应用程序（客户端）ID
    1. 目录（租户）ID

## <a name="provide-permissions-to-the-application"></a>向应用程序分配权限

1. 在 Azure 门户中，搜索“Purview 帐户”。
2. 选择要在其中对 SQL 数据库和 Synapse 进行分类的 Purview 帐户。
3. 打开“访问控制(IAM)”，然后选择“添加” 。

4. 选择“添加角色分配”。
5. 在“角色”部分，搜索“Purview 数据读取器”并将其选中 。
6. 在“选择”部分，搜索之前创建的应用程序并将其选中，然后点击“保存” 。

## <a name="extract-the-classification-from-azure-purview"></a>从 Azure Purview 提取分类

1. 打开 Purview 帐户，然后在“主页”页面中，搜索你想要在其中复制标签的 Azure SQL 数据库或 Azure Synapse Analytics。
2. 将 qualifiedName 复制到“属性”下，并保留以供将来使用。
3. 打开 PowerShell shell。

4. 根据 SQL 资产类型（Azure SQL 数据库或 Azure Synapse）复制以下脚本之一。
5. 用上面复制的值填充参数：

   a. $TenantID：第 1 部分，第 9 步
   
   b. $ClientID：第 1 部分，第 9 步
   
   c. $TenantID：第 1 部分，第 8 步
   
   d. $purviewAccountName：第 2 部分，第 2 步
   
   e. $sqlDatabaseName：第 3 部分，第 2 步

6. 复制脚本的输出以供将来使用。

### <a name="for-azure-sql-database"></a>对于 Azure SQL 数据库

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$sqlDatabaseName="mssql://sql_server_name.database.windows.net/db_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_db?attr:qualifiedName=" + $sqlDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

### <a name="for-azure-synapse-analytics"></a>对于 Azure Synapse Analytics

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$dwDatabaseName="mssql://dw_server_name.database.windows.net/dw_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_dw?attr:qualifiedName=" + $dwDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_dw_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

## <a name="run-the-t-sql-command-on-your-sql-asset"></a>在 SQL 资产上运行 T-SQL 命令

1. 使用你选择的工具连接到 Azure SQL 数据库或 Azure Synapse。
2. 运行从上一部分复制的 T-SQL 命令。

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

有关 Azure Purview 的详细信息，请参阅 [Azure Purview 文档](https://docs.microsoft.com/azure/purview/)。
