---
title: 教程：使用 Azure 门户添加角色分配条件来限制对 Blob 的访问（预览）- Azure ABAC
titleSuffix: Azure Storage
description: 使用 Azure 门户和 Azure 基于特性的访问控制 (Azure ABAC) 添加角色分配条件以限制对 blob 的访问。
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 09/24/2021
ms.openlocfilehash: 0ab670b9ef6309b8db01f4a53f41c703dea69423
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092259"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-the-azure-portal-preview"></a>教程：使用 Azure 门户添加角色分配条件来限制对 Blob 的访问（预览）

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

下面是条件在代码中的呈现效果：

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEqualsIgnoreCase 'Cascade'
    )
)
```

## <a name="step-1-create-a-user"></a>步骤 1：创建用户

1. 以订阅所有者的身份登录到 Azure 门户。

1. 单击“Azure Active Directory”。

1. 创建用户或查找现有用户。 本教程使用 Chandra 作为示例。

## <a name="step-2-set-up-storage"></a>步骤 2：设置存储

1. 创建兼容 blob 索引标记功能的存储帐户，此功能当前为公共预览版。 有关详细信息，请查看[通过 Blob 索引标记管理和查找 Azure Blob 数据（预览）](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)。

1. 在存储帐户中创建新容器，并将“公共访问级别”设置为“专用(不允许匿名访问)”。

1. 在容器中，单击“上传”以打开“上传 blob”窗格。

1. 查找要上传的文本文件。

1. 单击“高级”以展开窗格。

1. 在“Blob 索引标记”部分，将以下 blob 索引标记添加到文本文件。

    如果未看到“Blob 索引标记”部分，并且刚注册了订阅，可能需要等待几分钟，更改才会生效。 有关详细信息，请参阅[使用 blob 索引标记（预览版）管理和查找 Azure Blob 存储中的数据](../blobs/storage-blob-index-how-to.md)。

    > [!NOTE]
    > Blob 还支持存储用户定义的任意键-值元数据。 尽管元数据与 Blob 索引标记类似，但你也必须将 Blob 索引标记与条件配合使用。

    | 密钥 | 值 |
    | --- | --- |
    | Project  | Cascade |

   ![显示含有“Blob 索引标记”部分的“上传 blob”窗格的屏幕截图。](./media/storage-auth-abac-portal/container-upload-blob.png)

1. 选择“上传”按钮以上传文件。

1. 上传第二个文本文件。

1. 将以下 blob 索引标记添加到第二个文本文件。

    | 密钥 | 值 |
    | --- | --- |
    | Project  | Baker |

## <a name="step-3-assign-a-storage-blob-data-role"></a>步骤 3：分配存储 blob 数据角色

1. 打开资源组。

1. 单击“访问控制(IAM)”。

1. 单击“角色分配”选项卡以查看在此范围内的角色分配。

1. 单击“添加” > “添加角色分配(预览版)” 。

   ![“添加”>“添加角色分配”菜单（预览版）的屏幕截图。](./media/storage-auth-abac-portal/add-role-assignment-menu-preview.png)

    随即打开“添加角色分配”窗格。

1. 在“角色”选项卡上，选择“[存储 Blob 数据读取者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)”角色。

    ![含有“角色”选项卡的“添加角色分配”页面的屏幕截图。](./media/storage-auth-abac-portal/roles.png)

1. 在“成员”选项卡上，选择之前创建的用户。

   ![含有“成员”选项卡的“添加角色分配”页面的屏幕截图。](./media/storage-auth-abac-portal/members.png)

1. （可选）在“说明”框中，输入“对标记为 Project=Cascade 的 blob 的读取访问权限” 。

1. 单击“下一步”。

## <a name="step-4-add-a-condition"></a>步骤 4：添加条件

1. 在“条件(可选)”选项卡上，单击“添加条件” 。

    ![用于添加新条件的“添加角色分配条件”页面的屏幕截图。](./media/storage-auth-abac-portal/condition-add-new.png)

    随即显示“添加角色分配条件”页面。

1. 在“添加操作”部分，单击“选择操作”。

    随即会显示“选择操作”窗格。 此窗格是基于角色分配（将作为条件的目标）进行了筛选的数据操作列表。

    ![“选择操作”窗格屏幕截图，其中选中了一个操作。](./media/storage-auth-abac-portal/condition-actions-select.png)

1. 在“读取 blob”下，单击“从具有标记条件的 blob 中读取内容”，然后单击“选择” 。

1. 在“生成表达式”部分，单击“添加表达式”。

    随即展开“表达式”部分。

1. 指定以下表达式设置：

    | 设置 | 值 |
    | --- | --- |
    | 特性源 | 资源 |
    | 特性 | blob 索引标记 [键中的值] |
    | 密钥 | Project |
    | 运算符 | StringEqualsIgnoreCase |
    | 值 | Cascade |

    ![“blob 索引标记”的“生成表达式”部分的屏幕截图。](./media/storage-auth-abac-portal/condition-expressions.png)

1. 向上滚动到“编辑器类型”，然后单击“代码” 。

    条件显示为代码。 可在此代码编辑器中对条件进行更改。 若要返回到可视化编辑器，请单击“可视化”。

    ![代码编辑器中显示的条件的屏幕截图。](./media/storage-auth-abac-portal/condition-code.png)

1. 单击“保存”以添加条件并返回“添加角色分配”页面。

1. 单击“下一步”。

1. 在“查看 + 分配”选项卡上，单击“查看 + 分配”以分配具有条件的角色 。

    片刻之后，会在所选范围内为安全主体分配角色。

    ![分配角色后的角色分配列表的屏幕截图。](./media/storage-auth-abac-portal/rg-role-assignments-condition.png)

## <a name="step-5-test-the-condition"></a>步骤 5：测试条件

若要测试条件，需使用 Azure PowerShell。

1. 打开 PowerShell 窗口。

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
    Get-AzStorageBlob: This request is not authorized to perform this operation using this permission. HTTP Status Code: 403 - HTTP Error Message: This request is not authorized to perform this operation using this permission.
    ErrorCode: AuthorizationPermissionMismatch
    ErrorMessage: This request is not authorized to perform this operation using this permission.
    RequestId: <requestId>
    Time: Sun, 13 Sep 2020 12:33:42 GMT
    ```

1. 读取 Cascade 项目的文件。

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameCascade -Context $bearerCtx 
    ```

    下面是一个输出示例。 请注意，由于该项目带有 Project=Cascade 标记，因此可以读取该文件。

    ```azurepowershell
       AccountName: <storageAccountName>, ContainerName: <containerName>

    Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotT
                                                                                                                  ime
    ----                 --------  ------          -----------                    ------------         ---------- ---------
    CascadeFile.txt      BlockBlob 7               text/plain                     2021-04-24 05:35:24Z Hot
    ```

## <a name="step-6-clean-up-resources"></a>步骤 6：清理资源

1. 删除添加的角色分配。

1. 删除创建的测试存储帐户。

1. 删除创建的用户。

## <a name="next-steps"></a>后续步骤

- [示例 Azure 角色分配条件](storage-auth-abac-examples.md)
- [Azure 存储中 Azure 角色分配条件的操作和特性（预览版）](storage-auth-abac-attributes.md)
- [Azure 角色分配条件格式和语法](../../role-based-access-control/conditions-format.md)
