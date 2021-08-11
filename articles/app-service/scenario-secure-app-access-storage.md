---
title: 教程 - Web 应用使用托管标识访问存储 | Azure
description: 在本教程中，你将了解如何使用托管标识为某个应用访问 Azure 存储。
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 06/16/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurecli, devx-track-azurepowershell, subject-rbac-steps
ms.openlocfilehash: b6d04053d4b63552c2329a675c2557e6f1cd8fee
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290027"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>教程：从 Web 应用访问 Azure 存储

了解如何使用托管标识为 Azure 应用服务上运行的 Web 应用（不是登录用户）访问 Azure 存储。

:::image type="content" alt-text="关系图展示如何访问存储。" source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

你要从 Web 应用添加对 Azure 数据平面（Azure 存储、Azure SQL 数据库、Azure Key Vault 或其他服务）的访问权限。 你可以使用共享密钥，但之后需要考虑操作安全性：谁可以创建、部署和管理机密。 还可能会将此密钥签入 GitHub，黑客知道如何扫描它。 向 Web 应用授予数据访问权限的更安全方法是使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

Azure Active Directory (Azure AD) 中的托管标识允许应用程序服务通过基于角色的访问控制 (RBAC) 访问资源，而不要求使用应用凭据。 向 Web 应用分配托管标识之后，Azure 会负责创建和分发证书。 用户无需费心管理机密或应用凭据。

在本教程中，你将了解：

> [!div class="checklist"]
>
> * 在 Web 应用上创建系统分配的托管标识。
> * 创建存储帐户和 Azure Blob 存储容器。
> * 使用托管标识从 Web 应用访问存储。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 在[启用了应用服务身份验证/授权模块](scenario-secure-app-authentication-app-service.md)的 Azure 应用服务上运行的 Web 应用程序。

## <a name="enable-managed-identity-on-an-app"></a>在应用上启用托管标识

如果通过 Visual Studio 创建和发布 Web 应用，则已在应用上启用了托管标识。 在应用服务中，在左侧窗格中选择“标识”，然后选择“系统分配” 。 验证“状态”是否设置为“打开” 。 如果不是，请依次选择“保存”和“是”以启用系统分配的托管标识 。 启用托管标识后，状态将设置为“启用”并且对象 ID 可用。

:::image type="content" alt-text="屏幕截图中显示“系统分配的标识”选项。" source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

此步骤创建一个新的对象 ID，该 ID 不同于在“身份验证/授权”窗格中创建的应用 ID。 复制系统分配的托管标识的对象 ID。 稍后需要用到此信息。

## <a name="create-a-storage-account-and-blob-storage-container"></a>创建存储帐户和 Blob 存储容器

现在可以创建存储帐户和 Blob 存储容器。

每个存储帐户都必须属于 Azure 资源组。 资源组是对 Azure 资源进行分组的逻辑容器。 创建存储帐户时，可以选择创建新的资源组，或者使用现有的资源组。 本文介绍如何创建新资源组。

可以使用常规用途 v2 存储帐户访问所有 Azure 存储服务：Blob、文件、队列、表和磁盘。 本文所述的步骤将创建常规用途 v2 存储帐户，但创建任何类型的存储帐户的步骤都相似。

Azure 存储中的 Blob 已组织成容器。 你需要先创建容器，然后才能在本教程的稍后部分上传 Blob。

# <a name="portal"></a>[Portal](#tab/azure-portal)

若要在 Azure 门户中创建常规用途 v2 存储帐户，请执行以下步骤。

1. 在 Azure 门户菜单中，选择“所有服务”  。 在资源列表中输入“存储帐户”。 开始键入时，会根据输入筛选该列表。 选择“存储帐户”  。

1. 在显示的“存储帐户”窗口中，选择“添加” 。

1. 选择要在其中创建存储帐户的订阅。

1. 在“资源组”字段下，从下拉菜单中选择包含你的 Web 应用的资源组。

1. 然后，输入存储帐户的名称。 所选名称在 Azure 中必须唯一。 该名称还必须为 3 到 24 个字符，并且只能包含数字和小写字母。

1. 选择存储帐户的位置或使用默认位置。

1. 将这些字段设置为其默认值：

    |字段|Value|
    |--|--|
    |部署模型|Resource Manager|
    |性能|标准|
    |帐户种类|StorageV2（常规用途 v2）|
    |复制|读取访问异地冗余存储 (RA-GRS)|
    |访问层|热|

1. 选择“查看+创建”可查看存储帐户设置并创建帐户。

1. 选择“创建” 。

若要在 Azure 存储中创建 Blob 存储容器，请执行以下步骤。

1. 转到 Azure 门户中的新存储帐户。

1. 在存储帐户的左侧菜单中滚动到“Blob 服务”部分，然后选择“容器” 。

1. 选择“+ 容器”。 

1. 键入新容器的名称。 容器名称必须小写，必须以字母或数字开头，并且只能包含字母、数字和短划线 (-) 字符。

1. 设置容器的公共访问权限级别。 默认级别为“专用(禁止匿名访问)”。 

1. 选择“确定”  创建容器。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要创建常规用途 v2 存储帐户和 Blob 存储容器，请运行以下脚本。 指定包含 Web 应用的资源组的名称。 为存储帐户输入名称。 所选名称在 Azure 中必须唯一。 该名称还必须为 3 到 24 个字符，并且只能包含数字和小写字母。

指定存储帐户的位置。 若要查看对订阅有效的位置列表，请运行 ```Get-AzLocation | select Location```。 容器名称必须小写，必须以字母或数字开头，并且只能包含字母、数字和短划线 (-) 字符。

请务必将尖括号中的占位符值替换为你自己的值。

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要创建常规用途 v2 存储帐户和 Blob 存储容器，请运行以下脚本。 指定包含 Web 应用的资源组的名称。 为存储帐户输入名称。 所选名称在 Azure 中必须唯一。 该名称还必须为 3 到 24 个字符，并且只能包含数字和小写字母。 

指定存储帐户的位置。 容器名称必须小写，必须以字母或数字开头，并且只能包含字母、数字和短划线 (-) 字符。

下面的示例使用 Azure AD 帐户授权操作创建容器。 创建容器之前，请将存储 Blob 数据参与者角色分配给自己。 即使你是帐户所有者，也需要显式权限才能对存储帐户执行数据操作。

请务必将尖括号中的占位符值替换为你自己的值。

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>授予对存储帐户的访问权限

你需要先向 Web 应用授予对存储帐户的访问权限，然后才能创建、读取或删除 Blob。 在上一步骤中，你使用托管标识配置了在应用服务上运行的 Web 应用。 使用 Azure RBAC，可以向托管标识授予对其他资源的访问权限，这一点与所有安全主体一样。 “存储 Blob 数据参与者”角色向 Web 应用（由系统分配的托管标识表示）授予对 Blob 容器和数据的读取、写入和删除访问权限。

# <a name="portal"></a>[Portal](#tab/azure-portal)

在 [Azure 门户](https://portal.azure.com)中，进入你的存储帐户，向 Web 应用授予访问权限。 依次选择左侧窗格中的“访问控制(IAM)”、“角色分配” 。 你将看到有权访问存储帐户的用户的列表。 现在，你要向机器人（需要访问存储帐户的应用服务）添加角色分配。 选择“添加” > “添加角色分配”，打开“添加角色分配”页面  。

在订阅范围将“存储 Blob 数据参与者”角色分配给应用服务。   有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。

Web 应用现在可以访问存储帐户。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

运行以下脚本，在存储帐户上为 Web 应用（由系统分配的托管标识表示）分配“存储 Blob 数据参与者”角色。

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

运行以下脚本，在存储帐户上为 Web 应用（由系统分配的托管标识表示）分配“存储 Blob 数据参与者”角色。

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>访问 Blob 存储 (.NET)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 类用于获取代码的令牌凭据，以授权对 Azure 存储的请求。 创建 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 类的实例，该类使用托管标识提取令牌并将其附加到服务客户端。 下面的代码示例获取经过身份验证的令牌凭据，并使用它创建服务客户端对象，该对象将上传新的 Blob。

若要查看作为示例应用程序一部分的代码，请参阅 [GitHub 上的示例](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity)。

### <a name="install-client-library-packages"></a>安装客户端库包

安装 [Blob 存储 NuGet 包](https://www.nuget.org/packages/Azure.Storage.Blobs/)以便与 Blob 存储一起使用，并安装[适用于 .NET NuGet 包的 Azure 标识客户端库](https://www.nuget.org/packages/Azure.Identity/)来使用 Azure AD 凭据进行身份验证。 使用 .NET Core 命令行接口或 Visual Studio 中的包管理器控制台，在项目中安装客户端库。

# <a name="command-line"></a>[命令行](#tab/command-line)

打开一个命令行，并切换到包含项目文件的目录。

运行安装命令。

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[包管理器](#tab/package-manager)

在 Visual Studio 中打开项目或解决方案，并使用“工具” > “NuGet 包管理器” > “包管理器控制台”命令打开控制台  。

运行安装命令。
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>示例

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>清理资源

如果已完成本教程，并且不再需要 Web 应用或相关资源，请[清理创建的资源](scenario-secure-app-clean-up-resources.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
>
> * 创建系统分配的托管标识。
> * 创建存储帐户和 Blob 存储容器。
> * 使用托管标识从 Web 应用访问存储。

> [!div class="nextstepaction"]
> [应用服务代表用户访问 Microsoft Graph](scenario-secure-app-access-microsoft-graph-as-user.md)