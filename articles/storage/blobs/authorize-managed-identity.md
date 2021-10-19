---
title: 使用托管标识授予对 Blob 数据的访问权限
titleSuffix: Azure Storage
description: 使用 Azure 资源的托管标识从 Azure VM、函数应用等位置中运行的应用程序授予对 Blob 数据的访问权限。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 774bdecb65b885bcf7e930729ba2152b0bbbca39
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859575"
---
# <a name="authorize-access-to-blob-data-with-managed-identities-for-azure-resources"></a>使用 Azure 资源托管标识授予对 Blob 数据的访问权限

Azure Blob 存储支持使用 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)进行 Azure Active Directory (Azure AD) 身份验证。 Azure 资源的托管标识可以从 Azure 虚拟机 (VM)、函数应用、虚拟机规模集和其他服务中运行的应用程序使用 Azure AD 凭据授予对 Blob 数据的访问权限。 将 Azure 资源的托管标识与 Azure AD 身份验证结合使用，可避免将凭据随在云中运行的应用程序一起存储。

本文介绍如何使用 Azure 资源的托管标识从 Azure VM 中授予对 Blob 数据的访问权限。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识

在使用 Azure 资源的托管标识从 VM 中授予对 Blob 的访问权限之前，必须首先在 VM 上启用 Azure 资源的托管标识。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

有关托管标识的详细信息，请参阅 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="assign-an-rbac-role-to-a-managed-identity"></a>将 RBAC 角色分配到托管标识

当 Azure AD 安全主体尝试访问 Azure 存储帐户中的数据时，该安全主体必须具有对数据资源的访问权限。 不管安全主体是 Azure 中的托管标识还是在开发环境中运行代码的 Azure AD 用户帐户，都必须为安全主体分配一个 Azure 角色，由该角色授权访问 Azure 存储中的数据。 有关通过 Azure RBAC 分配数据访问权限的信息，请参阅[分配用于访问 Blob 数据的 Azure 角色](assign-azure-role-data-access.md)。

> [!NOTE]
> 创建 Azure 存储帐户时，系统不会自动向你分配通过 Azure AD 访问数据的权限。 你必须为自己显式分配一个用于 Azure 存储的 Azure 角色。 可以在订阅、资源组、存储帐户、容器级别分配该角色。

## <a name="use-a-managed-identity-to-create-a-block-blob-in-net"></a>在 .NET 中使用托管标识创建块 Blob

Azure 标识客户端库借助 [Azure SDK](https://github.com/Azure/azure-sdk) 简化了用于 Azure Active Directory (Azure AD) 授权的 OAuth 2.0 访问令牌的获取过程。 使用 Azure 标识客户端库获取访问令牌时，无论应用程序是在开发环境中运行还是在 Azure 中运行，都可以使用相同的代码来获取令牌。 有关详细信息，请参阅[使用 Azure 标识库获取用于授权的访问令牌](../common/identity-library-acquire-token.md)。

若要获取令牌，以便代码用它来授权对 Azure 存储的请求，请创建 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 类的实例。 然后，可以使用该令牌创建有权在 Azure 存储中执行数据操作的服务客户端对象。 有关使用 **DefaultAzureCredential** 类授权托管标识访问 Azure 存储的详细信息，请参阅[适用于 .NET 的 Azure 标识客户端库](/dotnet/api/overview/azure/identity-readme)。

下面的代码示例演示如何获取访问令牌并使用它来创建服务客户端对象，然后使用该服务客户端上传新的 Blob：

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a service client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> 若要使用 Azure AD 授权对 Blob 数据的请求，必须对这些请求使用 HTTPS。

## <a name="next-steps"></a>后续步骤

- [分配 Azure 角色以访问 blob 数据](assign-azure-role-data-access.md)
- [从本机或 Web 应用程序授予对 Blob 或队列数据的访问权限](../common/storage-auth-aad-app.md)
- [教程：使用托管标识从应用服务访问存储](../../app-service/scenario-secure-app-access-storage.md)
