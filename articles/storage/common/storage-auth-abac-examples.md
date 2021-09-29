---
title: 示例 Azure 角色分配条件（预览版）- Azure RBAC
titleSuffix: Azure Storage
description: Azure 基于特性的访问控制 (Azure ABAC) 的示例 Azure 角色分配条件。
services: storage
author: rolyon
ms.service: storage
ms.topic: conceptual
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: a04c205370ee07900b649ed0a3f6f4bf3a892685
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577817"
---
# <a name="example-azure-role-assignment-conditions-preview"></a>示例 Azure 角色分配条件（预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文列出了角色分配条件的一些示例。

## <a name="prerequisites"></a>先决条件

有关添加或编辑角色分配条件的先决条件的信息，请参阅[条件的先决条件](../../role-based-access-control/conditions-prerequisites.md)。

## <a name="example-1-read-access-to-blobs-with-a-tag"></a>示例 1：对带有标记的 blob 进行读取访问

该条件允许用户读取 blob 索引标记键为 Project 和标记值为 Cascade 的 blob。 不允许尝试访问没有该键值标记的 blob。

> [!TIP]
> Blob 还支持存储用户定义的任意键值元数据。 尽管元数据与 blob 索引标记类似，但必须使用带条件的 blob 索引标记。 有关详细信息，请查看[通过 blob 索引标记管理和查找 Azure Blob 数据（预览）](../blobs/storage-manage-find-blobs.md)。

![示例 1 条件图，其中显示了对带有标记的某些 blob 进行读取访问。](./media/storage-auth-abac-examples/example-1.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
    )
)
```

#### <a name="azure-portal"></a>Azure 门户

以下是通过 Azure 门户添加此条件的设置。

| 条件 #1 | 设置 |
| --- | --- |
| 操作 | 从具有标记条件的 blob 中读取内容 |
| 特性源 | 资源 |
| 特性 | blob 索引标记 [键中的值] |
| 密钥 | {keyName} |
| 操作员 | StringEquals |
| 值 | {keyValue} |

![Azure 门户中示例 1 条件编辑器的屏幕截图。](./media/storage-auth-abac-examples/example-1-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

以下介绍如何使用 Azure PowerShell 添加此条件。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

下面说明如何测试此条件。

```azurepowershell
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
Get-AzStorageBlob -Container <containerName> -Blob <blobName> -Context $bearerCtx 
```

## <a name="example-2-new-blobs-must-include-a-tag"></a>示例 2：新 blob 必须包含标记

该条件要求所有新的 blob 必须包含 blob 索引标记键 Project 和标记值 Cascade。

> [!TIP]
> Blob 还支持存储用户定义的任意键值元数据。 尽管元数据与 blob 索引标记类似，但必须使用带条件的 blob 索引标记。 有关详细信息，请查看[通过 blob 索引标记管理和查找 Azure Blob 数据（预览）](../blobs/storage-manage-find-blobs.md)。

有两个权限允许创建新的 blob，因此必须以获取这两个权限作为目标。 必须将该条件添加到含以下权限之一的任何角色分配。

- /blobs/write（创建或更新）
- /blobs/add/action（创建）

![示例 2 条件图，其中显示了新的 blob 必须包含标记。](./media/storage-auth-abac-examples/example-2.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
 )
)
```

#### <a name="azure-portal"></a>Azure 门户

以下是通过 Azure 门户添加此条件的设置。

| 条件 #1 | 设置 |
| --- | --- |
| 操作 | 写入到具有 blob 索引标记的 blob<br/>将内容写入到具有 blob 索引标记的 blob |
| 特性源 | 请求 |
| 特性 | blob 索引标记 [键中的值] |
| 密钥 | {keyName} |
| 操作员 | StringEquals |
| 值 | {keyValue} |

![Azure 门户中示例 2 条件 1 编辑器的屏幕截图。](./media/storage-auth-abac-examples/example-2-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

以下介绍如何使用 Azure PowerShell 添加此条件。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

下面说明如何测试此条件。

```azurepowershell
$localSrcFile = # path to an example file, can be an empty txt
$ungrantedTag = @{'Project'='Baker'}
$grantedTag = @{'Project'='Cascade'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example2 -Blob "Example2.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example2 -Blob "Example2.txt" -Tag $grantedTag -Context $bearerCtx
```

## <a name="example-3-existing-blobs-must-have-tag-keys"></a>示例 3：现有 blob 必须具有标记键

该条件要求使用至少一个允许的 blob 索引标记键（Project 或 Program）标记任何现有 blob。 该条件适用于向现有 blob 添加治理。

> [!TIP]
> Blob 还支持存储用户定义的任意键值元数据。 尽管元数据与 blob 索引标记类似，但必须使用带条件的 blob 索引标记。 有关详细信息，请查看[通过 blob 索引标记管理和查找 Azure Blob 数据（预览）](../blobs/storage-manage-find-blobs.md)。

有两个权限允许更新现有 blob 上的标记，因此必须以获取这两个权限为目标。 必须将该条件添加到含以下权限之一的任何角色分配。

- /blobs/write（更新或创建，不能排除创建）
- /blobs/tags/write

![示例 3 条件图，其中显示了现有 blob 必须具有标记键。](./media/storage-auth-abac-examples/example-3.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&$keys$&] ForAllOfAnyValues:StringEquals {'Project', 'Program'}
 )
)
```

#### <a name="azure-portal"></a>Azure 门户

以下是通过 Azure 门户添加此条件的设置。

| 条件 #1 | 设置 |
| --- | --- |
| 操作 | 写入到具有 blob 索引标记的 blob<br/>写入 blob 索引标记 |
| 特性源 | 请求 |
| 特性 | blob 索引标记 [键] |
| 操作员 | ForAllOfAnyValues:StringEquals |
| 值 | {keyName1}<br/>{keyName2} |

![Azure 门户中示例 3 条件 1 编辑器的屏幕截图。](./media/storage-auth-abac-examples/example-3-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

以下介绍如何使用 Azure PowerShell 添加此条件。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&`$keys`$&] ForAllOfAnyValues:StringEquals {'Project', 'Program'}))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

下面说明如何测试此条件。

```azurepowershell
$localSrcFile = # path to an example file, can be an empty txt
$ungrantedTag = @{'Mode'='Baker'}
$grantedTag = @{'Program'='Alpine';'Project'='Cascade'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example3 -Blob "Example3.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example3 -Blob "Example3.txt" -Tag $grantedTag -Context $bearerCtx
```

## <a name="example-4-existing-blobs-must-have-a-tag-key-and-values"></a>示例 4：现有 blob 必须具有标记键和值

该条件要求任何现有 blob 具有 blob 索引标记键 Project 和标记值 Cascade、Baker 或 Skagit。 该条件适用于向现有 blob 添加治理。

> [!TIP]
> Blob 还支持存储用户定义的任意键值元数据。 尽管元数据与 blob 索引标记类似，但必须使用带条件的 blob 索引标记。 有关详细信息，请查看[通过 blob 索引标记管理和查找 Azure Blob 数据（预览）](../blobs/storage-manage-find-blobs.md)。

有两个权限允许更新现有 blob 上的标记，因此必须以获取这两个权限为目标。 必须将该条件添加到含以下权限之一的任何角色分配。

- /blobs/write（更新或创建，不能排除创建）
- /blobs/tags/write

![示例 4 条件图，其中显示了现有 blob 必须具有标记键和值。](./media/storage-auth-abac-examples/example-4.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&$keys$&] ForAnyOfAnyValues:StringEquals {'Project'}
  AND
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] ForAllOfAnyValues:StringEquals {'Cascade', 'Baker', 'Skagit'}
 )
)
```

#### <a name="azure-portal"></a>Azure 门户

以下是通过 Azure 门户添加此条件的设置。

| 条件 #1 | 设置 |
| --- | --- |
| 操作 | 写入到具有 blob 索引标记的 blob<br/>写入 blob 索引标记 |
| 特性源 | 请求 |
| 特性 | blob 索引标记 [键] |
| 操作员 | ForAnyOfAnyValues:StringEquals |
| 值 | {keyName} |
| 操作员 | 且 |
| **表达式 2** |  |
| 特性源 | 请求 |
| 特性 | blob 索引标记 [键中的值] |
| 密钥 | {keyName} |
| 操作员 | ForAllOfAnyValues:StringEquals |
| 值 | {keyValue1}<br/>{keyValue2}<br/>{keyValue3} |

![Azure 门户中示例 4 条件 1 编辑器的屏幕截图。](./media/storage-auth-abac-examples/example-4-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

以下介绍如何使用 Azure PowerShell 添加此条件。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&`$keys`$&] ForAnyOfAnyValues:StringEquals {'Project'} AND @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] ForAllOfAnyValues:StringEquals {'Cascade', 'Baker', 'Skagit'}))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

下面说明如何测试此条件。

```azurepowershell
$localSrcFile = <pathToLocalFile>
$ungrantedTag = @{'Project'='Alpine'}
$grantedTag1 = @{'Project'='Cascade'}
$grantedTag2 = @{'Project'='Baker'}
$grantedTag3 = @{'Project'='Skagit'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag1 -Context $bearerCtx
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag2 -Context $bearerCtx
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag3 -Context $bearerCtx
```

## <a name="example-5-read-write-or-delete-blobs-in-named-containers"></a>示例 5：读取、写入或删除命名容器中的 blob

该条件允许用户读取、写入或删除名为 blob-example-container 的存储容器中的 blob。 该条件适用于与订阅中的其他用户共享特定存储容器。

有四个权限允许读取、写入和删除现有 Blob，因此必须以获取这四个权限为目标。 必须将该条件添加到含以下权限之一的任何角色分配。

- /blobs/delete
- /blobs/read
- /blobs/write（更新或创建）
- /blobs/add/action（创建）

在该条件下不使用子操作，因为只有在基于标记创作条件时才需要子操作。

![示例 5 条件图，其中显示了读取、写入或删除命名容器中的 blob。](./media/storage-auth-abac-examples/example-5.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})
 )
 OR 
 (
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'
 )
)
```

#### <a name="azure-portal"></a>Azure 门户

以下是通过 Azure 门户添加此条件的设置。

| 条件 #1 | 设置 |
| --- | --- |
| 操作 | 删除 blob<br/>读取 blob<br/>写入到 blob<br/>创建 blob 或快照，或追加数据 |
| 特性源 | 资源 |
| 特性 | 容器名称 |
| 操作员 | StringEquals |
| 值 | {containerName} |

![Azure 门户中示例 5 条件 1 编辑器的屏幕截图。](./media/storage-auth-abac-examples/example-5-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

以下介绍如何使用 Azure PowerShell 添加此条件。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

下面说明如何测试此条件。

```azurepowershell
$localSrcFile = <pathToLocalFile>
$grantedContainer = "blobs-example-container"
$ungrantedContainer = "ungranted"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Ungranted Container actions
$content = Set-AzStorageBlobContent -File $localSrcFile -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Get-AzStorageBlobContent -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Remove-AzStorageBlob -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
# Granted Container actions
$content = Set-AzStorageBlobContent -File $localSrcFile -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Remove-AzStorageBlob -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
```

## <a name="example-6-read-access-to-blobs-in-named-containers-with-a-path"></a>示例 6：使用路径对命名容器中的 blob 进行读取访问

该条件允许使用 blob 路径 readonly/* 对名为 blob-example-container 的存储容器进行读取访问。 该条件适用于与订阅中的其他用户共享存储容器的特定部分以进行读取访问。

必须将该条件添加到含以下权限的任何角色分配。

- /blobs/read

![示例 6 条件图，其中显示了使用路径对命名容器中的 blob 进行读取访问。](./media/storage-auth-abac-examples/example-6.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'
        AND
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'readonly/*'
    )
)
```

#### <a name="azure-portal"></a>Azure 门户

以下是通过 Azure 门户添加此条件的设置。

| 条件 #1 | 设置 |
| --- | --- |
| 操作 | 读取 blob |
| 特性源 | 资源 |
| 特性 | 容器名称 |
| 操作员 | StringEquals |
| 值 | {containerName} |
| **表达式 2** |  |
| 操作员 | 且 |
| 特性源 | 资源 |
| 特性 | Blob 路径 |
| 操作员 | StringLike |
| 值 | {pathString} |

![Azure 门户中示例 6 条件 1 编辑器的屏幕截图。](./media/storage-auth-abac-examples/example-6-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

以下介绍如何使用 Azure PowerShell 添加此条件。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' AND @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'readonly/*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

下面说明如何测试此条件。

```azurepowershell
$grantedContainer = "blobs-example-container"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to get ungranted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "Ungranted.txt" -Context $bearerCtx
# Try to get granted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "readonly/Example6.txt" -Context $bearerCtx
```

## <a name="example-7-write-access-to-blobs-in-named-containers-with-a-path"></a>示例 7：使用路径对命名容器中的 blob 进行写入访问

该条件允许合作伙伴（Azure AD 来宾用户）通过 uploads/contoso/* 路径将文件放入名为 Contosocorp 的存储容器。 该条件适用于允许其他用户将数据放入存储容器。

必须将该条件添加到含以下权限的任何角色分配。

- /blobs/write（创建或更新）
- /blobs/add/action（创建）

![示例 7 条件图，其中显示了使用路径对命名容器中的 blob 进行写入访问。](./media/storage-auth-abac-examples/example-7.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})
 )
 OR 
 (
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'contosocorp'
  AND
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'uploads/contoso/*'
 )
)
```

#### <a name="azure-portal"></a>Azure 门户

以下是通过 Azure 门户添加此条件的设置。

| 条件 #1 | 设置 |
| --- | --- |
| 操作 | 写入到 blob<br/>创建 blob 或快照，或追加数据 |
| 特性源 | 资源 |
| 特性 | 容器名称 |
| 操作员 | StringEquals |
| 值 | {containerName} |
| **表达式 2** |  |
| 操作员 | 且 |
| 特性源 | 资源 |
| 特性 | Blob 路径 |
| 操作员 | StringLike |
| 值 | {pathString} |

![Azure 门户中示例 7 条件 1 编辑器的屏幕截图。](./media/storage-auth-abac-examples/example-7-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

以下介绍如何使用 Azure PowerShell 添加此条件。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'contosocorp' AND @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'uploads/contoso/*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

下面说明如何测试此条件。

```azurepowershell
$grantedContainer = "contosocorp"
$localSrcFile = <pathToLocalFile>
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to set ungranted blob
$content = Set-AzStorageBlobContent -Container $grantedContainer -Blob "Example7.txt" -Context $bearerCtx -File $localSrcFile
# Try to set granted blob
$content = Set-AzStorageBlobContent -Container $grantedContainer -Blob "uploads/contoso/Example7.txt" -Context $bearerCtx -File $localSrcFile
```

## <a name="example-8-read-access-to-blobs-with-a-tag-and-a-path"></a>示例 8：对带有标记和路径的 blob 进行读取访问

该条件允许用户读取 blob 索引标记键为 Program，标记值为 Alpine 以及使用日志的 blob 路径的 blob。日志的 blob 路径还包括 blob 名称。

> [!TIP]
> Blob 还支持存储用户定义的任意键值元数据。 尽管元数据与 blob 索引标记类似，但必须使用带条件的 blob 索引标记。 有关详细信息，请查看[通过 blob 索引标记管理和查找 Azure Blob 数据（预览）](../blobs/storage-manage-find-blobs.md)。

必须将该条件添加到含以下权限的任何角色分配。

- /blobs/read

![示例 8 条件图，其中显示了对带有标记和路径的 blob 进行读取访问。](./media/storage-auth-abac-examples/example-8.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Program<$key_case_sensitive$>] StringEquals 'Alpine'
    )
)
AND
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'logs*'
    )
)
```

#### <a name="azure-portal"></a>Azure 门户

以下是通过 Azure 门户添加此条件的设置。

| 条件 #1 | 设置 |
| --- | --- |
| 操作 | 从具有标记条件的 blob 中读取内容 |
| 特性源 | 资源 |
| 特性 | blob 索引标记 [键中的值] |
| 密钥 | {keyName} |
| 操作员 | StringEquals |
| 值 | {keyValue} |

![Azure 门户中示例 8 条件 1 编辑器的屏幕截图。](./media/storage-auth-abac-examples/example-8-condition-1-portal.png)

| 条件 #2 | 设置 |
| --- | --- |
| 操作 | 读取 blob |
| 特性源 | 资源 |
| 特性 | Blob 路径 |
| 操作员 | StringLike |
| 值 | {pathString} |

![Azure 门户中示例 8 条件 2 编辑器的屏幕截图。](./media/storage-auth-abac-examples/example-8-condition-2-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

以下介绍如何使用 Azure PowerShell 添加此条件。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Program<`$key_case_sensitive`$>] StringEquals 'Alpine')) AND ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'logs*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

下面说明如何测试此条件。

```azurepowershell
$grantedContainer = "contosocorp"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to get ungranted blobs
# Wrong name but right tags
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "AlpineFile.txt" -Context $bearerCtx
# Right name but wrong tags
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "logsAlpine.txt" -Context $bearerCtx
# Try to get granted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "logs/AlpineFile.txt" -Context $bearerCtx
```

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure 门户添加角色分配条件来限制对 Blob 的访问（预览版）](storage-auth-abac-portal.md)
- [Azure 存储中 Azure 角色分配条件的操作和特性（预览版）](storage-auth-abac-attributes.md)
- [Azure 角色分配条件格式和语法（预览版）](../../role-based-access-control/conditions-format.md)
