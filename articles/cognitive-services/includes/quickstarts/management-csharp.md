---
title: 快速入门：适用于 .NET 的 Azure 管理客户端库
description: 在本快速入门中，开始使用适用于 .NET 的 Azure 管理客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/04/2021
ms.author: pafarley
ms.openlocfilehash: 42571364deb7eac1a9bb552e158f5e4680125568
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442321"
---
[参考文档](/dotnet/api/overview/azure/cognitiveservices/management) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/) | [示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="c-prerequisites"></a>C# 先决条件

* 一个有效的 Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 的当前版本。
* [!INCLUDE [contributor-requirement](./contributor-requirement.md)]
* [!INCLUDE [terms-azure-portal](./terms-azure-portal.md)]

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-c-application"></a>新建 C# 应用程序

创建新的 .NET Core 应用程序。 在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `azure-management-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*program.cs*。 

```console
dotnet new console -n azure-management-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>安装客户端库

在应用程序目录中，使用以下命令安装适用于 .NET 的 Azure 管理客户端库：

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

如果你使用的是 Visual Studio IDE，客户端库可用作可下载的 NuGet 包。

### <a name="import-libraries"></a>导入库

打开“program.cs”并将以下 `using` 语句添加到文件顶部：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>验证客户端

将以下字段添加到“program.cs”的根目录，并使用创建的服务主体和 Azure 帐户信息填写其值。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

然后，在 Main 方法中，使用这些值构造 CognitiveServicesManagementClient 对象。 所有 Azure 管理操作都需要此对象。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>调用管理方法

将以下代码添加到 Main 方法中，以列出可用资源、创建示例资源、列出拥有的资源，然后删除示例资源。 你将在后续步骤中定义这些方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-c"></a>创建认知服务资源 (C#)

若要创建并订阅新的认知服务资源，请使用 Create 方法。 此方法向传入的资源组添加新的可计费资源。 创建新资源时，需要知道要使用的服务的种类，以及其定价层（或 SKU）和 Azure 位置。 下面的方法使用所有这些参数并创建资源。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>选择服务和定价层

创建新资源时，需要知道要使用的服务的种类，以及所需的[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/)（或 SKU）。 创建资源时，将此信息和其他信息用作参数。 可以通过在脚本中调用以下方法来获取可用认知服务种类的列表：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>查看资源

若要查看 Azure 帐户下的所有资源（跨所有资源组），请使用以下方法：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>删除资源

下面的方法从给定的资源组中删除指定的资源。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

如果需要恢复已删除的资源，请参阅[恢复已删除的认知服务资源](../../manage-resources.md)。

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnet
dotnet run
```

## <a name="see-also"></a>另请参阅

* 有关如何安全地使用认知服务的说明，请参阅 **[对 Azure 认知服务的请求进行身份验证](../../authentication.md)** 。
* 请参阅 **[什么是 Azure 认知服务？](../../what-are-cognitive-services.md)** ，以获取认知服务中不同类别的列表。
* 若要查看认知服务支持的自然语言列表，请参阅 **[自然语言支持](../../language-support.md)** 。
* 请参阅 **[使用认知服务作为容器](../../cognitive-services-container-support.md)** 以了解如何使用本地认知服务。
* 请参阅 **[计划和管理认知服务的成本](../../plan-manage-costs.md)** ，以估计使用认知服务的成本。
* 有关管理 SDK 的更多详细信息，请参阅 [认知服务管理 SDK 参考文档](/dotnet/api/overview/azure/cognitiveservices/management)。
