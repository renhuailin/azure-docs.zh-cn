---
title: 教程：使用 Azure CLI（预览版）添加角色分配条件以限制对 Blob 的访问（预览版） - Azure ABAC
titleSuffix: Azure Storage
description: 使用 Azure CLI 和 Azure 基于特性的访问控制 (Azure ABAC) 添加角色分配条件以限制对 Blob 的访问。
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: 4050d9452a8253bbfe468b180a8eaa701e2a3684
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587454"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-azure-cli-preview"></a>教程：使用 Azure CLI（预览版）添加角色分配条件以限制对 Blob 的访问（预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在大多数情况下，角色分配将授予访问 Azure 资源所需的权限。 但在某些情况下，你可能希望通过添加角色分配条件来提供更精细的访问控制。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> - 将条件添加到角色分配
> - 基于 blob 索引标记限制对 blob 的访问

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

## <a name="step-1-sign-in-to-azure"></a>步骤 1 - 登录 Azure

1. 使用 [az login](/cli/azure/reference-index#az_login) 命令，并按照显示的说明以[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)或[所有者](../../role-based-access-control/built-in-roles.md#owner)身份登录到你的目录。

    ```azurecli
    az login
    ```

1. 使用 [az account show](/cli/azure/account#az_account_show) 获取订阅的 ID。

    ```azurecli
    az account show
    ```

1. 确定订阅 ID 并初始化变量。

    ```azurecli
    subscriptionId="<subscriptionId>"
    ```

## <a name="step-2-create-a-user"></a>步骤 2：创建用户

1. 使用 [az ad user create](/cli/azure/ad/user#az_ad_user_create) 创建用户或查找现有用户。 本教程使用 Chandra 作为示例。

1. 初始化用户对象 ID 的变量。

    ```azurecli
    userObjectId="<userObjectId>"
    ```

## <a name="step-3-set-up-storage"></a>步骤 3：设置存储

可以使用 Azure AD 凭据或存储帐户访问密钥通过 Azure CLI 授予对 Blob 存储的访问权限。 本文介绍如何使用 Azure AD 授权 Blob 存储操作。 有关详细信息，请参阅[快速入门：使用 Azure CLI 创建、下载和列出 Blob](../blobs/storage-quickstart-blobs-cli.md)

1. 使用 [az storage account](/cli/azure/storage/account)创建与 Blob 索引功能兼容的存储帐户。 有关详细信息，请参阅[使用 blob 索引标记（预览版）管理和查找 Azure Blob 数据](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)。

1. 使用 [az storage container](/cli/azure/storage/container) 在存储帐户中创建新的 Blob 容器，并将“公共访问级别”设置为“专用(不允许匿名访问)”。

1. 使用 [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) 将文本文件上传到容器。

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

    ```azurecli
    resourceGroup="<resourceGroup>"
    storageAccountName="<storageAccountName>"
    containerName="<containerName>"
    blobNameCascade="<blobNameCascade>"
    blobNameBaker="<blobNameBaker>"
    ```

## <a name="step-4-assign-a-role-with-a-condition"></a>步骤 4：分配带条件的角色

1. 初始化[存储 Blob 数据读取者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色变量。

    ```azurecli
    roleDefinitionName="Storage Blob Data Reader"
    roleDefinitionId="2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
    ```

1. 初始化资源组的范围。

    ```azurecli
    scope="/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
    ```

1. 初始化条件。

    ```azurecli
    condition="((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<\$key_case_sensitive\$>] StringEquals 'Cascade'))"
    ```

    在 Bash 中，如果启用历史记录扩展，则可能会看到消息 `bash: !: event not found`，因为有感叹号 (!)。 在这种情况下，可以使用命令 `set +H` 禁用历史记录扩展。 若要重新启用历史记录扩展，请使用 `set -H`。

    在 Bash 中，美元符号 ($) 对于扩展有特殊的含义。 如果条件包含美元符号 ($)，则可能需要使用反斜杠 (\\) 作为前缀。 例如，此条件使用美元符号来描述标记键名称。 如需详细了解 Bash 中引号的规则，请参阅[双引号](https://www.gnu.org/software/bash/manual/html_node/Double-Quotes.html)。

1. 初始化条件版本和说明。

    ```azurecli
    conditionVersion="2.0"
    description="Read access to blobs with the tag Project=Cascade"
    ```

1. 使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 将带条件的[存储 Blob 数据读取者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色分配到资源组范围的用户。

    ```azurecli
    az role assignment create --assignee-object-id $userObjectId --scope $scope --role $roleDefinitionId --description "$description" --condition "$condition" --condition-version $conditionVersion
    ```

    这是一个输出示例：

    ```azurecli
    {
      "canDelegate": null,
      "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))",
      "conditionVersion": "2.0",
      "description": "Read access to blobs with the tag Project=Cascade",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "name": "{roleAssignmentId}",
      "principalId": "{userObjectId}",
      "principalType": "User",
      "resourceGroup": "{resourceGroup}",
      "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

## <a name="step-5-optional-view-the-condition-in-the-azure-portal"></a>步骤 5：（可选）在 Azure 门户中查看条件

1. 在 Azure 门户中打开资源组。

1. 单击“访问控制(IAM)”。

1. 在“角色分配”选项卡上找到角色分配。

1. 在“条件”列中，单击“查看/编辑”以查看条件 。

    ![Azure 门户中“添加角色分配条件”的屏幕截图。](./media/shared/condition-view.png)

## <a name="step-6-test-the-condition"></a>步骤 6：测试条件

1. 打开新的命令窗口。

1. 使用 [az login](/cli/azure/reference-index#az_login) 以 Chandra 的身份登录。

    ```azurecli
    az login
    ```

1. 使用你所用的名称初始化以下变量。

    ```azurecli
    storageAccountName="<storageAccountName>"
    containerName="<containerName>"
    blobNameBaker="<blobNameBaker>"
    blobNameCascade="<blobNameCascade>"
    ```

1. 使用 [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) 尝试读取 Baker 项目的文件属性。

    ```azurecli
    az storage blob show --account-name $storageAccountName --container-name $containerName --name $blobNameBaker --auth-mode login
    ```

    下面是一个输出示例。 请注意，由于添加了上述条件，因此无法读取该文件。

    ```azurecli
    You do not have the required permissions needed to perform this operation.
    Depending on your operation, you may need to be assigned one of the following roles:
        "Storage Blob Data Contributor"
        "Storage Blob Data Reader"
        "Storage Queue Data Contributor"
        "Storage Queue Data Reader"

    If you want to use the old authentication method and allow querying for the right account key, please use the "--auth-mode" parameter and "key" value.
    ```

1. 读取 Cascade 项目的文件属性。

    ```azurecli
    az storage blob show --account-name $storageAccountName --container-name $containerName --name $blobNameCascade --auth-mode login 
    ```

    下面是一个输出示例。 请注意，由于文件带有 Project=Cascade 标记，因此可以读取该文件的属性。

    ```azurecli
    {
      "container": "<containerName>",
      "content": "",
      "deleted": false,
      "encryptedMetadata": null,
      "encryptionKeySha256": null,
      "encryptionScope": null,
      "isAppendBlobSealed": null,
      "isCurrentVersion": null,
      "lastAccessedOn": null,
      "metadata": {},
      "name": "<blobNameCascade>",
      "objectReplicationDestinationPolicy": null,
      "objectReplicationSourceProperties": [],
      "properties": {
        "appendBlobCommittedBlockCount": null,
        "blobTier": "Hot",
        "blobTierChangeTime": null,
        "blobTierInferred": true,
        "blobType": "BlockBlob",
        "contentLength": 7,
        "contentRange": null,

      ...

    }
    ```

## <a name="step-7-optional-edit-the-condition"></a>步骤 7：（可选）编辑条件

1. 在其他命令窗口中，使用 [az role assignment lis](/cli/azure/role/assignment#az_role_assignment_list) 获取添加的角色分配。

    ```azurecli
    az role assignment list --assignee $userObjectId --resource-group $resourceGroup
    ```

    输出将如下所示：

    ```azurecli
    [
      {
        "canDelegate": null,
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))",
        "conditionVersion": "2.0",
        "description": "Read access to blobs with the tag Project=Cascade",
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{userObjectId}",
        "principalName": "chandra@contoso.com",
        "principalType": "User",
        "resourceGroup": "{resourceGroup}",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ]
    ```

1. 使用以下格式创建 JSON 文件，并更新 `condition` 和 `description` 属性。

    ```json
    {
        "canDelegate": null,
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Baker'))",
        "conditionVersion": "2.0",
        "description": "Read access to blobs with the tag Project=Cascade or Project=Baker",
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{userObjectId}",
        "principalName": "chandra@contoso.com",
        "principalType": "User",
        "resourceGroup": "{resourceGroup}",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
        "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

1. 使用 [az role assignment update](/cli/azure/role/assignment#az_role_assignment_update) 更新角色分配的条件。

    ```azurecli
    az role assignment update --role-assignment "./path/roleassignment.json"
    ```

## <a name="step-8-clean-up-resources"></a>步骤 8：清理资源

1. 使用 [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete) 删除添加的角色分配和条件。

    ```azurecli
    az role assignment delete --assignee $userObjectId --role "$roleDefinitionName" --resource-group $resourceGroup
    ```

1. 删除创建的存储帐户。

1. 删除创建的用户。

## <a name="next-steps"></a>后续步骤

- [示例 Azure 角色分配条件](storage-auth-abac-examples.md)
- [Azure 存储中 Azure 角色分配条件的操作和特性（预览版）](storage-auth-abac-attributes.md)
- [Azure 角色分配条件格式和语法](../../role-based-access-control/conditions-format.md)
