---
title: 教程：使用 Azure PowerShell 添加角色分配条件以限制对 Blob 的访问（预览版）- Azure ABAC
titleSuffix: Azure Storage
description: 使用 Azure PowerShell 和 Azure 基于特性的访问控制 (Azure ABAC) 添加角色分配条件以限制对 Blob 的访问。
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: d6cb1980c93e5161f02b79b05f1128ba777027c6
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112281946"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-azure-powershell-preview"></a>教程：使用 Azure PowerShell 添加角色分配条件以限制对 Blob 的访问（预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在大多数情况下，角色分配将授予访问 Azure 资源所需的权限。 但在某些情况下，你可能希望通过添加角色分配条件来提供更精细的访问控制。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 将条件添加到角色分配
> * 基于 blob 索引标记限制对 blob 的访问

## <a name="prerequisites"></a>先决条件

有关添加或编辑角色分配条件的先决条件的信息，请参阅[有关条件的先决条件](../../role-based-access-control/conditions-prerequisites.md)。

## <a name="condition"></a>条件

在本教程中，你将使用特定的标记来限制对 blob 的访问。 例如，将条件添加到角色分配，使 Chandra 只能读取带有 Project=Cascade 标记的文件。

![使用条件的角色分配的示意图。](./media/shared/condition-role-assignment-rg.png)

如果 Chandra 尝试读取不带 Project=Cascade 标记的 blob，则访问将被拒绝。

![显示对带有 Project=Cascade 标记的 blob 进行读取访问的示意图。](./media/shared/condition-access.png)

下面是条件在代码中的大致形式：

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

## <a name="step-1-install-prerequisites"></a>步骤 1：安装必备组件

1. 打开 PowerShell 窗口。

1. 使用 [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule) 检查已安装模块的版本。

    ```azurepowershell
    Get-InstalledModule -Name Az
    Get-InstalledModule -Name Az.Resources
    Get-InstalledModule -Name Az.Storage
    ```

1. 如果需要，请使用 [Install-Module](/powershell/module/powershellget/install-module) 安装所需版本的 [Az](https://www.powershellgallery.com/packages/Az/)、[Az.Resources](https://www.powershellgallery.com/packages/Az.Resources/) 和 [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview) 模块。

    ```azurepowershell
    Install-Module -Name Az -RequiredVersion 5.5.0
    Install-Module -Name Az.Resources -RequiredVersion 3.2.1
    Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
    ```

1. 关闭再重新打开 PowerShell 以刷新会话。

## <a name="step-2-sign-in-to-azure"></a>步骤 2：登录到 Azure

1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 命令，并按照显示的说明以[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)或[所有者](../../role-based-access-control/built-in-roles.md#owner)身份登录到你的目录。

    ```azurepowershell
    Connect-AzAccount
    ```

1. 使用 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) 列出你的所有订阅。

    ```azurepowershell
    Get-AzSubscription
    ```

1. 确定订阅 ID 并初始化变量。

    ```azurepowershell
    $subscriptionId = "<subscriptionId>"
    ```

1. 将订阅设置为活动订阅。

    ```azurepowershell
    $context = Get-AzSubscription -SubscriptionId $subscriptionId
    Set-AzContext $context
    ```

## <a name="step-3-create-a-user"></a>步骤 3：创建用户

1. 使用 [New-AzureADUser](/powershell/module/azuread/new-azureaduser) 创建用户，或查找现有用户。 本教程使用 Chandra 作为示例。

1. 初始化用户对象 ID 的变量。

    ```azurepowershell
    $userObjectId = "<userObjectId>"
    ```

## <a name="step-4-set-up-storage"></a>步骤 4：设置存储

1. 使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 创建与 Blob 索引功能兼容的存储帐户。 有关详细信息，请参阅[使用 Blob 索引标记（预览版）管理和查找 Azure Blob 数据](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)。

1. 使用 [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) 在存储帐户中创建新的 Blob 容器，并将“公共访问级别”设置为“专用(不允许匿名访问)”。

1. 使用 [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) 将文本文件上传到该容器。

1. 将以下 Blob 索引标记添加到文本文件。 有关详细信息，请参阅[使用 Blob 索引标记（预览版）管理和查找 Azure Blob 存储中的数据](../blobs/storage-blob-index-how-to.md)。

    > [!NOTE]
    > Blob 还支持存储用户定义的任意键-值元数据。 尽管元数据与 Blob 索引标记类似，但你也必须将 Blob 索引标记与条件配合使用。 

    | 密钥 | 值 |
    | --- | --- |
    | Project  | Cascade |

1. 将第二个文本文件上传到容器。

1. 将以下 Blob 索引标记添加到第二个文本文件。

    | 密钥 | 值 |
    | --- | --- |
    | Project  | Baker |

1. 使用你所用的名称初始化以下变量。

    ```azurepowershell
    $resourceGroup = "<resourceGroup>"
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameCascade = "<blobNameCascade>"
    $blobNameBaker = "<blobNameBaker>"
    ```

## <a name="step-5-assign-a-role-with-a-condition"></a>步骤 5：分配带条件的角色

1. 初始化[存储 Blob 数据读取者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色变量。

    ```azurepowershell
    $roleDefinitionName = "Storage Blob Data Reader"
    $roleDefinitionId = "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
    ```

1. 初始化资源组的范围。

    ```azurepowershell
    $scope = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
    ```

1. 初始化条件。

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
    ```

    在 PowerShell 中，如果条件包含美元符号 ($)，则必须使用反撇号 (\`) 作为其前缀。 例如，此条件使用美元符号来描述标记键名称。

1. 初始化条件版本和说明。

    ```azurepowershell
    $conditionVersion = "2.0"
    $description = "Read access to blobs with the tag Project=Cascade"
    ```

1. 使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 将带条件的[存储 Blob 数据读取者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色分配到资源组范围的用户。

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $userObjectId -Scope $scope -RoleDefinitionId $roleDefinitionId -Description $description -Condition $condition -ConditionVersion $conditionVersion
    ```

    这是一个输出示例：
    
    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microso
                         ft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : Chandra
    SignInName         : chandra@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access to blobs with the tag Project=Cascade
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND
                         @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR
                         (@Resource[Microsoft.Storage/storageAccounts/blobServices/co
                         ntainers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))
    ```

## <a name="step-6-optional-view-the-condition-in-the-azure-portal"></a>步骤 6：（可选）在 Azure 门户中查看条件

1. 在 Azure 门户中打开资源组。

1. 单击“访问控制(IAM)”。

1. 在“角色分配”选项卡上找到角色分配。

1. 在“条件”列中，单击“查看/编辑”以查看条件 。

    ![Azure 门户中“添加角色分配条件”的屏幕截图。](./media/shared/condition-view.png)

## <a name="step-7-test-the-condition"></a>步骤 7：测试条件

1. 打开一个新的 PowerShell 窗口。

1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 以 Chandra 的身份登录。

    ```azurepowershell
    Connect-AzAccount
    ```

1. 使用你所用的名称初始化以下变量。

    ```azurepowershell
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameBaker = "<blobNameBaker>"
    $blobNameCascade = "<blobNameCascade>"
    ```

1. 使用 [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) 创建特定的上下文，以便更轻松地访问你的存储帐户。

    ```azurepowershell
    $bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
    ```

1. 使用 [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) 尝试读取 Baker 项目的文件。

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Context $bearerCtx 
    ```

    下面是一个输出示例。 请注意，由于添加了上述条件，因此无法读取该文件。
    
    ```azurepowershell
    Get-AzStorageBlob : This request is not authorized to perform this operation using this permission. HTTP Status Code:
    403 - HTTP Error Message: This request is not authorized to perform this operation using this permission.
    ErrorCode: AuthorizationPermissionMismatch
    ErrorMessage: This request is not authorized to perform this operation using this permission.
    RequestId: <requestId>
    Time: Sat, 24 Apr 2021 13:26:25 GMT
    At line:1 char:1
    + Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Con ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzStorageBlob], StorageException
        + FullyQualifiedErrorId : StorageException,Microsoft.WindowsAzure.Commands.Storage.Blob.Cmdlet.GetAzureStorageBlob
       Command
    ```
    
1. 读取 Cascade 项目的文件。

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameCascade -Context $bearerCtx 
    ```

    下面是一个输出示例。 你会发现，由于该项目带有 Project=Cascade 标记，因此可以读取该文件。
    
    ```azurepowershell
       AccountName: <storageAccountName>, ContainerName: <containerName>
    
    Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotT
                                                                                                                  ime
    ----                 --------  ------          -----------                    ------------         ---------- ---------
    CascadeFile.txt      BlockBlob 7               text/plain                     2021-04-24 05:35:24Z Hot
    ```

## <a name="step-8-optional-edit-the-condition"></a>步骤 8：（可选）编辑条件

1. 在另一个 PowerShell 窗口中，使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 获取添加的角色分配。

    ```azurepowershell
    $testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectId
    ```

1. 编辑条件。

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Baker'))"
    ```

1. 初始化条件和说明。

    ```azurepowershell
    $testRa.Condition = $condition
    $testRa.Description = "Read access to blobs with the tag Project=Cascade or Project=Baker"
    ```

1. 使用 [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) 更新角色分配的条件。

    ```azurepowershell
    Set-AzRoleAssignment -InputObject $testRa -PassThru
    ```

    这是一个输出示例：

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microso
                         ft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : Chandra
    SignInName         : chandra@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access to blobs with the tag Project=Cascade or Project=Baker
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND
                         @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR
                         (@Resource[Microsoft.Storage/storageAccounts/blobServices/co
                         ntainers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade' OR @Resource[Microsoft.S
                         torage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>]
                         StringEquals 'Baker'))
    ```
    
## <a name="step-9-clean-up-resources"></a>步骤 9：清理资源

1. 使用 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) 删除添加的角色分配和条件。

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $userObjectId -RoleDefinitionName $roleDefinitionName -ResourceGroupName $resourceGroup
    ```

1. 删除创建的存储帐户。

1. 删除创建的用户。

## <a name="next-steps"></a>后续步骤

- [示例 Azure 角色分配条件](storage-auth-abac-examples.md)
- [Azure 存储中 Azure 角色分配条件的操作和特性（预览版）](storage-auth-abac-attributes.md)
- [Azure 角色分配条件格式和语法](../../role-based-access-control/conditions-format.md)
