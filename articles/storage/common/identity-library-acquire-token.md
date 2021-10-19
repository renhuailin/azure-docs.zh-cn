---
title: 使用 Azure 标识库获取用于授权的访问令牌
titleSuffix: Azure Storage
description: 了解如何使用 Azure 标识客户端库获取访问令牌，以便应用程序使用它来授予对 Azure 存储数据的访问权限。 借助 Azure 标识库，可以在开发环境或 Azure 中使用相同的代码来获取访问令牌。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: bd1df757c03552024490b0de13988b4dc63115a7
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859565"
---
# <a name="use-the-azure-identity-library-to-get-an-access-token-for-authorization"></a>使用 Azure 标识库获取用于授权的访问令牌

Azure 标识客户端库借助 [Azure SDK](https://github.com/Azure/azure-sdk) 简化了用于 Azure Active Directory (Azure AD) 授权的 OAuth 2.0 访问令牌的获取过程。 适用于 .NET、Java、Python 和 JavaScript 的最新版本的 Azure 存储客户端库与适用于每种对应语言的 Azure 标识库集成，提供了一种简单而安全的方法来获取用于授权 Azure 存储请求的访问令牌。

Azure 标识客户端库的优点在于，它使你可以使用相同的代码来获取访问令牌，无论你的应用程序是在开发环境中运行还是在 Azure 中运行。 适用于 .NET 的 Azure 标识客户端库返回安全主体的访问令牌。 当代码在 Azure 中运行时，安全主体可以是 Azure 资源的托管标识、服务主体或者用户或组。 在开发环境中，客户端库为用户主体或服务主体提供用于测试的访问令牌。

Azure 标识客户端库返回的访问令牌封装在令牌凭据中。 然后，可以使用该令牌凭据来获取服务客户端对象，以用于对 Azure 存储执行授权的操作。 获取访问令牌和令牌凭据的一种简单方法就是使用 Azure 标识客户端库提供的 DefaultAzureCredential 类。 此类的实例尝试以各种常用方式获取令牌凭据，它适用于开发环境和 Azure。

有关适用于对应语言的 Azure 标识客户端库的详细信息，请参阅以下文章之一：

- [适用于 .NET 的 Azure 标识客户端库](/dotnet/api/overview/azure/identity-readme)
- [适用于 Java 的 Azure 标识客户端库](/java/api/overview/azure/identity-readme)
- [适用于 Python 的 Azure 标识客户端库](/python/api/overview/azure/identity-readme)
- [适用于 JavaScript 的 Azure 标识客户端库](/javascript/api/overview/azure/identity-readme)

## <a name="assign-azure-roles-for-access-to-data"></a>分配可访问数据的 Azure 角色

当 Azure AD 安全主体尝试访问 Azure 存储帐户中的数据时，该安全主体必须具有对数据资源的访问权限。 不管安全主体是 Azure 中的托管标识还是在开发环境中运行代码的 Azure AD 用户帐户，都必须为安全主体分配一个 Azure 角色，由该角色授权访问 Azure 存储中的数据。 有关通过 Azure RBAC 分配数据访问权限的信息，请参阅以下文章之一：

- [分配 Azure 角色以访问 blob 数据](../blobs/assign-azure-role-data-access.md)
- [分配用于访问队列数据的 Azure 角色](../queues/assign-azure-role-data-access.md)
- [分配用于访问表数据（预览版）的 Azure 角色](../tables/assign-azure-role-data-access.md)

> [!NOTE]
> 创建 Azure 存储帐户时，系统不会自动向你分配通过 Azure AD 访问数据的权限。 你必须为自己显式分配一个用于 Azure 存储的 Azure 角色。 可以在订阅、资源组、存储帐户、容器、队列或表级别分配它。

## <a name="authenticate-the-user-in-the-development-environment"></a>在开发环境中对用户进行身份验证

代码在开发环境中运行时，可能会自动处理身份验证，也可能需要浏览器登录才能进行身份验证，具体取决于使用哪些工具。 例如，Microsoft Visual Studio 和 Microsoft Visual Studio Code 支持单一登录 (SSO)，使活动 Azure AD 用户帐户自动用于身份验证。 有关 SSO 的详细信息，请参阅[单一登录到应用程序](../../active-directory/manage-apps/what-is-single-sign-on.md)。

还可以创建服务主体并设置开发环境可在运行时读取的环境变量。

## <a name="authenticate-a-service-principal-in-the-development-environment"></a>在开发环境中对服务主体进行身份验证

如果开发环境不支持单一登录或通过 Web 浏览器登录，可以使用服务主体从开发环境进行身份验证。 创建服务主体后，将服务主体返回的值添加到环境变量。

### <a name="create-the-service-principal"></a>创建服务主体

若要通过 Azure CLI 来创建服务主体并分配 Azure 角色，请调用 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 命令。 提供要分配给新服务主体的 Azure 存储数据访问角色。 此外，请提供角色分配的范围。 若要详细了解为 Azure 存储提供的内置角色，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。

如果没有足够的权限将角色分配给服务主体，可能需要请求帐户所有者或管理员来执行相关角色分配。

下面的示例使用 Azure CLI 创建新服务主体，并为其分配了存储帐户范围内的“存储 Blob 数据参与者”角色：

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

`az ad sp create-for-rbac` 命令返回 JSON 格式的服务主体属性列表。 复制这些值，以便在下一步中使用它们来创建必要的环境变量。

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

有关如何创建服务主体的详细信息，请参阅以下文章之一：

- [在门户中创建 Azure AD 应用程序和服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)
- [使用 Azure PowerShell 创建 Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps)
- [使用 Azure CLI 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)

> [!IMPORTANT]
> Azure 角色分配需要几分钟时间来进行传播。

### <a name="set-environment-variables"></a>设置环境变量。

Azure 标识客户端库会在运行时读取三个环境变量中的值，以对服务主体进行身份验证。 下表介绍了为每个环境变量设置的值。

|环境变量|Value
|-|-
|`AZURE_CLIENT_ID`|服务主体的应用 ID
|`AZURE_TENANT_ID`|服务主体的 Azure AD 租户 ID
|`AZURE_CLIENT_SECRET`|为服务主体生成的密码

> [!IMPORTANT]
> 设置环境变量后，关闭并重新打开控制台窗口。 如果使用的是 Visual Studio 或其他开发环境，则可能需要重新启动开发环境以便环境能够注册新的环境变量。

有关详细信息，请参阅[在门户中创建 Azure 应用标识](../../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="get-an-access-token-for-authorization"></a>获取用于授权的访问令牌

Azure 标识客户端库提供类，这些类可用于获取通过 Azure AD 授权请求所使用的访问令牌。 Azure 存储客户端库包括服务客户端对象构造函数，这些对象使用令牌凭据作为参数。 可以将这两者与 Azure AD 凭据一起使用，轻松授权 Azure 存储请求。

对于应用程序需要在开发环境和 Azure 中获取访问令牌的最简单方案，建议使用 DefaultAzureCredential 类。 对于其他方案，也提供了大量其他类型的令牌凭据。

以下示例演示如何使用 DefaultAzureCredential 在 .NET 中获取令牌。 然后，应用程序使用该令牌创建新的服务客户端，随后使用该客户端创建 Blob 容器。 尽管此示例使用 .NET 和 Blob 存储服务，但 DefaultAzureCredential 类与其他语言和其他 Azure 服务的行为类似。

```csharp
static void CreateBlobContainer(string accountName, string containerName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a token credential and create a service client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                  new DefaultAzureCredential());

    // Create the container if it does not exist.
    containerClient.CreateIfNotExists();
}
```  

有关使用 DefaultAzureCredential 类授权托管标识访问 Azure 存储的详细信息，请参阅[适用于 .NET 的 Azure 标识客户端库](/dotnet/api/overview/azure/identity-readme)。

## <a name="see-also"></a>请参阅

- [Azure AD 中的应用和服务主体](../../active-directory/develop/app-objects-and-service-principals.md)
- [Microsoft 标识平台身份验证库](../../active-directory/develop/reference-v2-libraries.md)