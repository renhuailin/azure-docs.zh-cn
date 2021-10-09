---
title: 编写应用身份验证码
titleSuffix: Azure Digital Twins
description: 请参阅如何编写客户端应用程序中的验证码
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a1e397acfe8118c339b45d6c786ae2c0b2cc82e8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124827020"
---
# <a name="write-client-app-authentication-code"></a>编写客户端应用验证码

[设置 Azure 数字孪生实例和身份验证](how-to-set-up-instance-portal.md)后，可创建将用于与实例进行交互的客户端应用程序。 设置入门客户端项目后，需要针对 Azure 数字孪生实例在该客户端应用中编写代码以对应用进行身份验证。

Azure 数字孪生使用[基于 OAUTH 2.0 的 Azure AD 安全令牌](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)进行身份验证。 若要对 SDK 进行身份验证，需要获取具有 Azure 数字孪生正确权限的持有者令牌，并将其与 API 调用一起传递。 

本文介绍如何使用 `Azure.Identity` 客户端库获取凭据。 尽管本文介绍的是 C# 代码示例（例如你为 [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 编写的内容），但无论你使用的是何种 SDK，都可以使用 `Azure.Identity` 版本（有关可用于 Azure 数字孪生的 SDK 的详细信息，请参阅 [Azure 数字孪生 API 和 SDK](concepts-apis-sdks.md)）。

## <a name="prerequisites"></a>先决条件

首先，请完成[设置实例和身份验证](how-to-set-up-instance-portal.md)中的设置步骤。 此设置将确保你拥有 Azure 数字孪生实例，并且你的用户具有访问权限。 完成此设置后，便可编写客户端应用代码。

若要继续，你需要一个可在其中编写代码的客户端应用项目。 如果尚未设置客户端应用项目，请使用你选择的语言创建一个基本项目，以在本教程中使用。

## <a name="authenticate-using-azureidentity-library"></a>使用 Azure.Identity 库进行身份验证

`Azure.Identity` 是一个客户端库，提供多种凭据获取方法，你可使用这些方法来获取持有者令牌并对 SDK 进行身份验证。 尽管本文提供的是 C# 示例，但你可查看多种语言的 `Azure.Identity`，其中包括：

* [.NET (C#)](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)
* [Java](/java/api/overview/azure/identity-readme?view=azure-java-stable&preserve-view=true)
* [JavaScript](/javascript/api/overview/azure/identity-readme?view=azure-node-latest&preserve-view=true)
* [Python](/python/api/overview/azure/identity-readme?view=azure-python&preserve-view=true)

`Azure.Identity` 中的三种常用凭据获取方法是：

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) 为即将部署到 Azure 的应用程序提供默认的 `TokenCredential` 身份验证流，若在本地部署，建议选择此方法。 还可启用它来尝试本文中建议的另外两种方法；它包装 `ManagedIdentityCredential` 并可使用配置变量访问 `InteractiveBrowserCredential`。
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) 非常适用于需要[托管标识 (MSI)](../active-directory/managed-identities-azure-resources/overview.md) 的情况，是使用 Azure Functions 和部署到 Azure 服务时的很好的候选项。
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) 适用于交互式应用程序，可用于创建经过身份验证的 SDK 客户端。

本文的余下内容演示如何在 [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 中使用这些方法。

### <a name="add-azureidentity-to-your-net-project"></a>向 .NET 项目添加 Azure.Identity

若要将 .NET 项目设置为使用 `Azure.Identity` 进行身份验证，请完成以下步骤：

1. 将 SDK 包 `Azure.DigitalTwins.Core` 和 `Azure.Identity` 包包含在项目中。 可使用 Visual Studio 包管理器或 `dotnet` 命令行工具包含这些包，具体取决于你选择的工具。 

1. 向项目代码添加以下 using 语句：

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

然后，添加代码以使用 `Azure.Identity` 中的某个方法来获取凭据。 以下各部分将更详细地介绍每一种方法的使用。

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential 方法

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) 为即将部署到 Azure 的应用程序提供默认的 `TokenCredential` 身份验证流，若在本地部署，建议选择此方法。

若要使用默认的 Azure 凭据，则需要 Azure 数字孪生实例的 URL（[查找说明](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)）。

下面是向项目添加 `DefaultAzureCredential` 的代码示例：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>设置本地 Azure 凭据

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential 方法

[ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) 方法适用于需要[托管标识 (MSI)](../active-directory/managed-identities-azure-resources/overview.md) 的情况，例如[使用 Azure Functions 进行身份验证](#authenticate-azure-functions)。

这意味着，可在同一项目中像 `DefaultAzureCredential` 或 `InteractiveBrowserCredential` 一样使用 `ManagedIdentityCredential`，对项目的不同部分进行身份验证。

若要使用默认的 Azure 凭据，则需要 Azure 数字孪生实例的 URL（[查找说明](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)）。 你可能还需要[应用注册](./how-to-create-app-registration-portal.md)和应用注册的[应用程序（客户端）ID](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id)。

在 Azure 函数中，可使用如下所示的托管标识凭据：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential 方法

[InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) 方法适用于交互式应用程序，并将打开一个 Web 浏览器进行身份验证。 如果需要交互式身份验证，可使用此方法而不是 `DefaultAzureCredential`。

若要使用交互式浏览器凭据，你将需要具有 Azure 数字孪生 API 权限的应用注册。 有关如何设置此应用注册的步骤，请参阅[创建应用注册](./how-to-create-app-registration-portal.md)。 设置应用注册后，需要以下内容：
* [应用注册的应用程序（客户端）ID](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id)
* [应用注册的目录（租户）ID](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id)
* [Azure 数字孪生实例的 URL](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

下面是使用 `InteractiveBrowserCredential` 创建经过身份验证的 SDK 客户端的代码示例。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> 虽然你可将客户端 ID、租户 ID 和实例 URL 直接放置在代码中（如上所示），但建议让代码从配置文件或环境变量中获取这些值。

## <a name="authenticate-azure-functions"></a>使用 Azure Functions 进行身份验证

本部分包含使用 Azure Functions 进行身份验证上下文中的一些重要配置选项。 首先，你将了解建议的类级别变量和验证码，使函数可以访问 Azure 数字孪生。 然后，你将了解在函数的代码发布到 Azure 后要完成的最后一些函数的配置步骤。 

### <a name="write-application-code"></a>编写应用程序代码

编写 Azure 函数时，请考虑将以下变量和代码添加到函数：

* **用于将 Azure 数字孪生服务 URL 读作环境变量或配置设置的代码**。 最好从[应用程序设置/环境变量](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)读取服务 URL，而不是在函数中对其进行硬编码。 在 Azure 函数中，用于读取环境变量的代码可能如下所示： 

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

    稍后，在发布函数后，你将创建并设置环境变量的值，供此代码读取。 有关如何执行此操作的说明，请跳到[配置应用程序设置](#configure-application-settings)。

* **用于保存 HttpClient 实例的静态变量。** HttpClient 的创建成本相对较高，因此你可能希望使用验证码创建一次，以避免在每次调用函数时创建。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* 托管标识凭据。 创建一个托管标识凭据，函数将使用该凭据访问 Azure 数字孪生。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

    稍后，在发布函数后，你将确保函数的标识有权访问 Azure 数字孪生 API。 有关如何执行此操作的说明，请跳到[分配访问角色](#assign-an-access-role)。    

* 本地变量 DigitalTwinsClient。 在函数内添加该变量，用于保存 Azure 数字孪生客户端实例。 请勿在类中将此变量设置为静态。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* 对 adtInstanceUrl 的 NULL 检查。 添加 NULL 检查，然后将函数逻辑包装在 try/catch 块中以捕获所有异常。

将这些变量添加到函数后，函数代码可能如以下示例所示。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

完成函数代码后（包括添加身份验证和函数逻辑），[将应用发布到 Azure](../azure-functions/functions-develop-vs.md#publish-to-azure)

### <a name="configure-published-app"></a>配置已发布的应用

最后，为已发布的 Azure 函数完成以下配置步骤，以确保该函数可以访问 Azure 数字孪生实例。

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

## <a name="authenticate-across-tenants"></a>跨租户进行身份验证

Azure 数字孪生是一项仅支持一个 [Azure Active Directory (Azure AD) 租户](../active-directory/develop/quickstart-create-new-tenant.md)的服务：Azure 数字孪生实例所在的订阅的主租户。

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

如果需要使用属于实例中不同租户的服务主体或用户帐户来访问 Azure 数字孪生实例，可以让其他租户的每个联合标识从 Azure 数字孪生实例的“主”租户请求令牌。 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

还可以在代码的凭据选项中指定主租户。 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>其他凭据方法

如果上面突出显示的身份验证方案不能满足你的应用需求，可浏览 [Microsoft 标识平台](../active-directory/develop/v2-overview.md#getting-started)中提供的其他类型的身份验证。 此平台的文档介绍了按应用程序类型组织的更多身份验证方案。

## <a name="next-steps"></a>后续步骤

详细了解 Azure 数字孪生中安全性的工作原理：
* [Azure 数字孪生解决方案的安全性](concepts-security.md)

或者，现在身份验证已设置，接下来请在实例中创建和管理模型：
* [管理 DTDL 模型](how-to-manage-model.md)