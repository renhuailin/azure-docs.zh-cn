---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: bccc61f0099a8d96fd830b48c55d54a67560667b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966433"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/dotnet/)。
- 适用于操作系统的最新版本 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)。
- 获取最新版本的 [.NET 标识 SDK](/dotnet/api/azure.identity)。
- 获取最新版本的 [.NET 管理 SDK](../../concepts/sdk-options.md)。

## <a name="installing-the-sdk"></a>安装 SDK

首先，在 C# 项目中加入通信服务管理 SDK：

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>订阅 ID

你需要知道 Azure 订阅的 ID。 可以从门户获取此内容：

1.  登录到 Azure 帐户
2.  选择左侧边栏中的“订阅”
3.  选择所需的任何订阅
4.  单击“概览”
5.  选择订阅 ID

在本快速入门中，我们假设你已将订阅 ID 存储在名为 `AZURE_SUBSCRIPTION_ID` 的环境变量中。

## <a name="authentication"></a>身份验证

若要与 Azure 通信服务进行通信，你必须首先向 Azure 验证自己的身份。 通常使用服务主体标识执行此操作。

### <a name="option-1-managed-identity"></a>选项 1：托管标识

如果你的代码在 Azure 中作为服务运行，则进行身份验证的最简单方法是从 Azure 获取托管标识。 详细了解[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)。

[支持托管标识的 Azure 服务](../../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

[如何使用应用服务和 Azure Functions 的托管标识](../../../app-service/overview-managed-identity.md?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[系统分配的托管标识](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[用户分配的托管标识](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)

你创建的托管标识的 ClientId 必须显式传递到 `ManagedIdentityCredential`。

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>选项 2：Service Principal

你可能希望使用自己管理的服务主体向 Azure 进行身份验证，而不是使用托管标识。 通过有关[在 Azure Active Directory 中创建和管理服务主体](../../../active-directory/develop/howto-create-service-principal-portal.md)的文档了解详细信息。

创建服务主体之后，需要从 Azure 门户收集有关该主体的以下信息：

- **客户端 ID**
- **客户端机密**
- **租户 ID**

分别将这些值存储在名为 `AZURE_CLIENT_ID`、`AZURE_CLIENT_SECRET` 和 `AZURE_TENANT_ID` 的环境变量中。 随后可以创建类似于下面这样的通信服务管理客户端：

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>选项 3：用户标识

如果要代表交互用户调用 Azure，而不是使用服务标识，则可以使用以下代码创建 Azure 通信服务管理客户端。 这会打开一个浏览器窗口，用于提示用户输入其 MSA 或 Azure AD 凭据。

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>管理通信服务资源

### <a name="interacting-with-azure-resources"></a>与 Azure 资源交互

现在你已进行了身份验证，可以使用管理客户端进行 API 调用。

对于以下每个示例，我们会将通信服务资源分配给现有资源组。

如需创建资源组，你可以使用 [Azure 门户](../../../azure-resource-manager/management/manage-resource-groups-portal.md)或 [Azure 资源管理器 SDK](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md) 完成此操作。

### <a name="create-and-manage-a-communication-services-resource"></a>创建和管理通信服务资源

我们的通信服务管理 SDK 客户端实例 (``Azure.ResourceManager.Communication.CommunicationManagementClient``) 可以用于对通信服务资源执行操作。

#### <a name="create-a-communication-services-resource"></a>创建通信服务资源

创建通信服务资源时，会指定资源组名称和资源名称。 请注意，`Location` 属性始终是 `global`，并且在公共预览版期间，`DataLocation` 值必须是 `UnitedStates`。

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>更新通信服务资源

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>列出所有通信服务资源

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>删除通信服务资源

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>管理密钥和连接字符串

每个通信服务资源都有一对访问密钥和对应的连接字符串。 这些密钥可以使用管理 SDK 进行访问，随后由其他通信服务 SDK 用于向 Azure 通信服务验证自己的身份。

#### <a name="get-access-keys-for-a-communication-services-resource"></a>获取通信服务资源的访问密钥

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>注册通信服务资源的访问密钥

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```