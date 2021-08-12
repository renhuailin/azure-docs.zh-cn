---
title: 教程`:`使用托管标识通过 SAS 凭据访问 Azure 存储 - Azure AD
description: 本教程介绍了如何使用 Windows VM 系统分配的托管标识通过 SAS 凭据（而不是存储帐户访问密钥）访问 Azure 存储。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/24/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell, subject-rbac-steps
ms.openlocfilehash: 7f5ae30f7bb476b5bc3c76c2a22d4dda2fc402d8
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112966313"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>教程：使用 Windows VM 系统分配的托管标识通过 SAS 凭据访问 Azure 存储

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教程介绍如何使用系统分配的标识为 Windows 虚拟机 (VM) 获取存储共享访问签名 (SAS) 凭据。 具体而言，是[服务 SAS 凭据](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures)。 

服务 SAS 提供了在不公开帐户访问密钥的情况下授权特定的服务（在我们的示例中为 blob 服务）在有限时间内访问存储帐户中对象的权限。 可以像平常在执行存储操作时一样使用 SAS 凭据，例如使用存储 SDK 时。 对于本教程，我们将演示使用 Azure 存储 PowerShell 上传和下载 blob。 将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建存储帐户
> * 向 VM 授予对资源管理器中的存储帐户 SAS 的访问权限 
> * 使用 VM 的标识获取一个访问令牌，并使用它从资源管理器检索 SAS 

## <a name="prerequisites"></a>先决条件

- 具备托管标识相关知识。 如果不熟悉 Azure 资源功能的托管标识，请参阅此[概述](overview.md)。 
- 一个 Azure 帐户，[注册免费 Azure 帐户](https://azure.microsoft.com/free/)。
- 在相应范围（订阅或资源组）内具有“所有者”权限，以执行所需的资源创建和角色管理步骤。 如需角色分配方面的帮助，请参阅[分配 Azure 角色以管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)。
- 还需要启用了系统分配的托管标识的 Windows 虚拟机。
  - 如需为本教程创建虚拟机，则可以按照标题为[创建启用了系统分配的标识的虚拟机](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)的文章进行操作

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>创建存储帐户 

如果还没有存储帐户，现在将创建存储帐户。 也可以跳过此步骤，并向 VM 的系统分配的托管标识授予对现有存储帐户 SAS 凭据的访问权限。 

1. 单击 Azure 门户左上角的“+/创建新服务”按钮。
2. 依次单击“存储”、“存储帐户”，并将显示新的“创建存储帐户”面板。
3. 输入存储帐户的名称，稍后将使用该名称。  
4. “部署模型”和“帐户类型”应分别设置为“资源管理器”和“通用” 。 
5. 确保“订阅”和“资源组”与上一步中创建 VM 时指定的名称匹配。  
6. 单击“创建”。

    ![新建存储帐户](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>在存储帐户中创建 Blob 容器

稍后我们会将文件上传并下载到新存储帐户。 由于文件需要 blob 存储，我们需要创建用于存储文件的 blob 容器。

1. 导航回新创建的存储帐户。
2. 在左侧面板上，单击“Blob 服务”下的“容器”链接。
3. 单击页面顶部的“+ 容器”，将滑出“新建容器”面板。
4. 为容器指定名称，选择访问级别，单击“确定”  。 在本教程中的后面部分将使用所指定的名称。 

    ![创建存储容器](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>授权 VM 的系统分配的托管标识使用存储 SAS 

Azure 存储原本不支持 Azure AD 身份验证。  但是，可以使用托管标识从资源管理器检索存储 SAS，然后使用 SAS 来访问存储。  在此步骤中，将向 VM 的系统分配的托管标识授予对存储帐户 SAS 的访问权限。   

1. 导航回新创建的存储帐户。   
1. 单击“访问控制(IAM)”  。
1. 单击“添加” > “添加角色分配”以打开“添加角色分配”页。 
1. 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | 存储帐户参与者 |
    | 将访问权限分配到 | 托管标识 |
    | 系统分配 | 虚拟机 |
    | Select | &lt;你的 Windows 虚拟机&gt; |

    ![Azure 门户中的“添加角色分配”页。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>使用 VM 标识获取访问令牌，并使用它调用 Azure 资源管理器 

在本教程的剩余部分中，我们将从 VM 入手。

在此部分中，将需要使用 Azure 资源管理器 PowerShell cmdlet。  如果尚未安装，请[下载最新版本](/powershell/azure/)，然后再继续。

1. 在 Azure 门户中，导航到“虚拟机”，转到 Windows 虚拟机，然后在“概述”页中单击顶部的“连接”。
2. 输入创建 Windows VM 时添加的用户名和密码。 
3. 现在，已经创建了与虚拟机的远程桌面连接。
4. 在远程会话中打开 PowerShell，并使用 Invoke-WebRequest 从 Azure 资源终结点的本地托管标识获取 Azure 资源管理器令牌。

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > “资源”参数的值必须完全匹配 Azure AD 预期的值。 如果使用 Azure 资源管理器资源 ID，必须在 URI 的结尾添加斜线。
    
    接下来，提取“内容”元素，该元素以 JavaScript 对象表示法 (JSON) 格式字符串的形式存储在 $response 对象中。 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    接下来，从响应中提取访问令牌。
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>从 Azure 资源管理器中获取 SAS 凭据，以便调用存储 

现在，使用在上一部分中检索到的访问令牌通过 PowerShell 调用资源管理器，来创建存储 SAS 凭据。 具有 SAS 凭据后，我们便可以调用存储操作。

对于此请求，我们将使用以下 HTTP 请求参数来创建 SAS 凭据：

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

这些参数包括在针对 SAS 凭据的请求的 POST 正文中。 有关用于创建 SAS 凭据的参数的详细信息，请参阅 [List Service SAS REST reference](/rest/api/storagerp/storageaccounts/listservicesas)（列出服务 SAS REST 参考）。

首先，将参数转换为 JSON，然后调用存储 `listServiceSas` 终结点来创建 SAS 凭据：

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> URL 区分大小写。因此，请确保使用的大小写与之前在命名资源组时使用的大小写完全相同，包括“resourceGroups”使用的是大写“G”。 

现在，我们可以从响应中提取 SAS 凭据：

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

如果检查 SAS 凭据，将会看到类似以下内容的内容：

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

接下来，我们将创建一个名为“test.txt”的文件。 然后，通过 `New-AzStorageContent` cmdlet 使用 SAS 凭据进行身份验证，将该文件上传到 blob 容器，然后下载该文件。

```bash
echo "This is a test text file." > test.txt
```

请务必首先使用 `Install-Module Azure.Storage` 安装 Azure 存储 cmdlet。 然后，可以使用 `Set-AzStorageBlobContent` PowerShell cmdlet 上传刚创建的 blob：

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

响应：

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

也可以使用 `Get-AzStorageBlobContent` PowerShell cmdlet 下载刚上传的 blob：

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

响应：

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何使用 Windows VM 系统分配的托管标识通过 SAS 凭据来访问 Azure 存储。  若要深入了解 Azure 存储 SAS，请参阅：

> [!div class="nextstepaction"]
>[使用共享访问签名 (SAS)](../../storage/common/storage-sas-overview.md)
