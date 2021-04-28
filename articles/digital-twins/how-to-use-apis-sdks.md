---
title: 使用 Azure 数字孪生 API 和 SDK
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure 数字孪生 API 和 SDK。
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 21247f6b396cb1f7016c74cbec528149c0583724
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587198"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>使用 Azure 数字孪生 API 和 SDK

Azure 数字孪生配备有“控制平面 API”和“数据平面 API”，用于管理实例及其元素 。 
* 控制平面 API 是 [Azure 资源管理器 (ARM) ](../azure-resource-manager/management/overview.md) API，涵盖创建实例和删除实例等资源管理操作。 
* 数据平面 API 是 Azure 数字孪生 API，用于管理模型、孪生体和图形等数据管理操作。

本文将概述可用的 API 以及与它们进行交互的方法。 可通过 [Postman](how-to-use-postman.md) 等工具或通过 SDK 直接将 REST API 与其关联的 Swagger 配合使用。

## <a name="overview-control-plane-apis"></a>概述：控制平面 API

控制平面 API 是用于将 Azure 数字孪生实例作为一个整体进行管理的 [ARM](../azure-resource-manager/management/overview.md) API，因此它们涵盖了创建实例或删除实例等操作。 你还将使用这些 API 来创建和删除终结点。

最新的控制平面 API 版本为 2020-12-01。

若要使用控制平面 API：
* 可参考[控制平面 Swagger 存储库](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable)中最新的 Swagger 文件夹来直接调用 API。 此文件夹还包括一个示例文件夹，其中显示了用法。
* 当前可通过以下方式访问控制 API 的 SDK：
  - [ **.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/)（[参考 [自动生成]](/dotnet/api/overview/azure/digitaltwins/management)）（[源](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins)）
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins)（[参考 [自动生成]](/java/api/overview/azure/digitaltwins)）（[源](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins)）
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins)（[源](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins)）
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/)（[源](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins)）
  - [**Go**](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt)（[源](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt)）

还可通过 [Azure 门户](https://portal.azure.com)和 [CLI](how-to-use-cli.md) 与 Azure 数字孪生交互，运用控制平面 API。

## <a name="overview-data-plane-apis"></a>概述：数据平面 API

数据平面 API 是用于管理 Azure 数字孪生实例中的元素的 Azure 数字孪生 API。 其中包括创建路由、上传模型、创建关系和管理孪生体等操作。 这些 API 大致可分为以下几类：
* **DigitalTwinModels** - DigitalTwinModels 类别包含用于管理 Azure 数字孪生实例中的 [模型](concepts-models.md)的 API。 管理活动包括上传、验证、检索和删除在 DTDL 中创作的模型。
* **DigitalTwins** - DigitalTwins 类别包含让开发人员能够在 Azure 数字孪生实例中创建、修改和删除 [数字孪生体](concepts-twins-graph.md)及其关系的 API。
* **查询** - 查询类别让开发人员能够跨关系 [查找孪生图中的数字孪生体集](how-to-query-graph.md)。
* **事件路由** - 事件路由类别包含通过系统向下游服务 [路由数据](concepts-route-events.md)的 API。

最新的数据平面 API 版本为 2020-10-31。

若要使用数据平面 API：
* 可通过以下方式直接调用 API：
   - 参考[数据平面 Swagger 存储库](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)中最新的 Swagger 文件夹。 此文件夹还包括一个示例文件夹，其中显示了用法。 
   - 查看 [API 参考文档](/rest/api/azure-digitaltwins/)。
* 可使用 .NET (C#) SDK。 若要使用 .NET SDK：
   - 可从 NuGet 中查看并添加包：[Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core)。 
   - 可查看 [SDK 参考文档](/dotnet/api/overview/azure/digitaltwins/client)。
   - 可在 GitHub 中找到 SDK 源（包括示例文件夹）：[适用于 .NET 的 Azure IoT 数字孪生客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)。 
   - 继续阅读本文的 [.NET (C#) SDK（数据平面）](#net-c-sdk-data-plane)部分，可查看详细信息和用法示例。
* 可使用 Java SDK。 若要使用 Java SDK：
   - 可从 Maven 查看和安装包：[`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - 可查看 [SDK 参考文档](/java/api/overview/azure/digitaltwins/client)
   - 可在 GitHub 中找到 SDK 源：[适用于 Java 的 Azure IoT 数字孪生客户端库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* 可使用 JavaScript SDK。 若要使用 JavaScript SDK：
   - 可从 npm 查看并安装包：[适用于 JavaScript 的 Azure 数字孪生核心客户端库](https://www.npmjs.com/package/@azure/digital-twins-core)。
   - 可查看 [SDK 参考文档](/javascript/api/@azure/digital-twins-core/)。
   - 可在 GitHub 中找到 SDK 源：[适用于 JavaScript 的 Azure 数字孪生核心客户端库](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* 可使用 Python SDK。 若要使用 Python SDK：
   - 可从 PyPi 查看并安装包：[适用于 Python 的 Azure 数字孪生核心客户端库](https://pypi.org/project/azure-digitaltwins-core/)。
   - 可查看 [SDK 参考文档](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)。
   - 可在 GitHub 中找到 SDK 源：[适用于 Python 的 Azure 数字孪生核心客户端库](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* 可使用 AutoRest 生成其他语言 SDK。 按照[操作指南：使用 AutoRest 为 Azure 数字孪生创建自定义 SDK](how-to-create-custom-sdks.md)中的说明进行操作。

还可通过 [CLI](how-to-use-cli.md) 与 Azure 数字孪生交互，运用数据平面 API。

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK（数据平面）

Azure 数字孪生 .NET (C#) SDK 是用于 .NET 的 Azure SDK 的一部分。 它是开源 SDK，基于 Azure 数字孪生数据平面 API。

> [!NOTE]
> 有关 SDK 设计的详细信息，请参阅常规的 [Azure SDK 设计原则](https://azure.github.io/azure-sdk/general_introduction.html)和具体的 [.NET 设计准则](https://azure.github.io/azure-sdk/dotnet_introduction.html)。

若要使用 SDK，请在项目中包括 NuGet 包 Azure.DigitalTwins.Core。 还需要最新版本的 Azure.Identity 包。 在 Visual Studio 中，可使用 NuGet 包管理器添加这些包（通过“工具”>“NuGet 管理器”>“管理解决方案的 NuGet 包”进行访问）。 或者，可在 .NET 命令行工具中使用下方 NuGet 包链接中找到的命令，将这些包添加到项目中：
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 这是用于[适用于 .NET 的 Azure 数字孪生 SDK](/dotnet/api/overview/azure/digitaltwins/client) 的包。 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). 该库提供可帮助进行 Azure 身份验证的工具。

有关如何在实践中使用 API 的详细演练步骤，请参阅[教程：编写客户端应用代码](tutorial-code.md)。 

### <a name="net-sdk-usage-examples"></a>.NET SDK 用法示例

下面是演示如何使用 .NET SDK 的一些代码示例。

对服务进行身份验证：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

上传模型：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

列出模型：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

创建孪生体：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

查询孪生体并循环遍历结果：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

有关此示例应用代码的演练步骤，请参阅 [*教程：编写客户端应用代码*](tutorial-code.md)。 

还可在[适用于 .NET (C#) SDK 的 GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)中找到更多示例。

#### <a name="serialization-helpers"></a>序列化帮助程序

序列化帮助程序是可在 SDK 内使用的帮助程序函数，用于快速创建或反序列化孪生数据以访问基本信息。 由于核心 SDK 方法默认以 JSON 形式返回孪生数据，因此，使用这些帮助程序类有助于进一步细分孪生数据。

可用的帮助程序类包括：
* `BasicDigitalTwin`：一般表示数字孪生体的核心数据
* `BasicDigitalTwinComponent`：一般表示 `BasicDigitalTwin` 的 `Contents` 属性中的一个组件
* `BasicRelationship`：一般表示关系的核心数据
* `DigitalTwinsJsonPropertyName`：包含在自定义数字孪生类型的 JSON 序列化和反序列化中使用的字符串常数

##### <a name="deserialize-a-digital-twin"></a>反序列化数字孪生

始终可使用你选择的 JSON 库（如 `System.Text.Json` 或 `Newtonsoft.Json`）反序列化孪生数据。 若要对孪生体进行基本访问，帮助程序类可使此操作更方便。

使用 `BasicDigitalTwin` 帮助程序类，还可通过 `Dictionary<string, object>` 访问孪生体上定义的属性。 若要列出孪生体的属性，可使用：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

> [!NOTE]
> `BasicDigitalTwin` 使用 `System.Text.Json` 特性。 若要将 `BasicDigitalTwin` 用于 [DigitalTwinsClient](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true)，必须使用默认构造函数初始化客户端，或者，如果要自定义序列化程序选项，请使用 [JsonObjectSerializer](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true)。

##### <a name="create-a-digital-twin"></a>创建数字孪生体

使用 `BasicDigitalTwin` 类，可准备用于创建孪生实例的数据：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

上面的代码等效于以下“手动”变体：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>反序列化关系

始终可将关系数据反序列化为你选择的类型。 若要对关系进行基本访问，请使用类型 `BasicRelationship`。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

使用 `BasicRelationship` 帮助程序类，还可通过 `IDictionary<string, object>` 访问关系上定义的属性。 若要列出属性，可使用：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>创建关系

使用 `BasicRelationship` 类，还可准备用于在孪生实例上创建关系的数据：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>为孪生体更新创建修补程序

若要更新对孪生和关系的调用，请使用 [JSON 修补程序](http://jsonpatch.com/)结构。 若要创建 JSON 修补程序操作列表，可使用 `JsonPatchDocument`，如下所示。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>常规 API/SDK 使用说明

> [!NOTE]
> 请注意，Azure 数字孪生目前不支持跨域资源共享 (CORS)。 有关影响和解决策略的详细信息，请参阅“概念：Azure 数字孪生解决方案的安全性”的[跨域资源共享 (CORS)](concepts-security.md#cross-origin-resource-sharing-cors) 部分 。

以下列表提供了有关使用 API 和 SDK 的更多详细信息和一般准则。

* 可使用 HTTP REST 测试工具（如 Postman）直接对 Azure 数字孪生 API 进行调用。 有关此过程的详细信息，请参阅[操作指南：通过 Postman 发出请求](how-to-use-postman.md)。
* 若要使用 SDK，请实例化 `DigitalTwinsClient` 类。 构造函数需要凭据，可使用各种身份验证方法在 `Azure.Identity` 包中获得。 有关 `Azure.Identity` 的详细信息，请参阅其[命名空间文档](/dotnet/api/azure.identity)。 
* 你可能会发现 `InteractiveBrowserCredential` 在入门时非常有用，但还有其他几个选项，包括[托管标识](/dotnet/api/azure.identity.interactivebrowsercredential)的凭据，你可使用此类凭据针对 Azure 数字孪生对[使用 MSI 设置的 Azure 函数](../app-service/overview-managed-identity.md?tabs=dotnet)进行身份验证。 有关 `InteractiveBrowserCredential` 的详细信息，请参阅其[类文档](/dotnet/api/azure.identity.interactivebrowsercredential)。
* 请求 Azure 数字孪生 API 的用户或者服务主体需要属于 Azure 数字孪生实例所在的相同 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 租户。 为了防止对 Azure 数字孪生终结点进行恶意扫描，将针对使用发起租户外部的访问令牌进行的请求返回“404 找不到子域”错误消息。 即使已通过 [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md) 协作向用户或服务主体授予 Azure 数字孪生数据所有者或 Azure 数字孪生数据读取者角色，也会返回此错误。 有关如何在多个租户之间实现访问的信息，请参阅 [*如何：编写应用身份验证代码*](how-to-authenticate-client.md#authenticate-across-tenants)。
* 所有服务 API 调用都公开为 `DigitalTwinsClient` 类的成员函数。
* 所有服务函数都存在于同步和异步版本中。
* 如果返回状态为 400 或更高，所有服务函数均会引发异常。 请确保将调用包装到 `try` 部分，并至少捕获 `RequestFailedExceptions`。 有关此类异常的详细信息，请参阅[此处](/dotnet/api/azure.requestfailedexception)。
* 大多数服务方法会返回 `Response<T>` 或 `Task<Response<T>>`（针对异步调用），其中 `T` 是服务调用的返回对象的类。 [`Response`](/dotnet/api/azure.response-1) 类封装服务返回并在其 `Value` 字段中显示返回值。  
* 包含分页结果的服务方法返回 `Pageable<T>` 或 `AsyncPageable<T>` 作为结果。 有关 `Pageable<T>` 类的详细信息，请参阅[此处](/dotnet/api/azure.pageable-1)；有关 `AsyncPageable<T>` 的详细信息，请参阅[此处](/dotnet/api/azure.asyncpageable-1)。
* 可使用 `await foreach` 循环来循环访问分页结果。 有关此进程的详细信息，请参阅[此处](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)。
* 基础 SDK 为 `Azure.Core`。 请参阅 [Azure 命名空间文档](/dotnet/api/azure)，了解 SDK 基础结构和类型。


服务方法尽可能返回强类型对象。 但由于 Azure 数字孪生是基于用户在运行时自定义配置的模型（通过上传到服务的 DTDL 模型），所以许多服务 API 采用并返回 JSON 格式的孪生数据。

## <a name="monitor-api-metrics"></a>监视 API 指标

可在 [Azure 门户](https://portal.azure.com/)中查看请求、延迟和失败率等 API 指标。 

在门户主页上，搜索 Azure 数字孪生实例以获取其详细信息。 在 Azure 数字孪生实例的菜单中选择“指标”选项以打开指标”页。

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="显示 Azure 数字孪生的“指标”页的屏幕截图":::

在此处，你可查看实例的指标并创建自定义视图。

## <a name="next-steps"></a>后续步骤

请参阅如何使用 Postman 直接请求 API：
* [操作指南：通过 Postman 发出请求](how-to-use-postman.md)

或使用本教程创建客户端应用，练习使用 .NET SDK：
* [*教程：* 为客户端应用编写代码](tutorial-code.md)
