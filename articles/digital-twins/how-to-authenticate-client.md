---
title: 编写应用身份验证码
titleSuffix: Azure Digital Twins
description: 请参阅如何编写客户端应用程序中的验证码
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f153544d27621e4978b496d74f8d79e4e0bf2024
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108207782"
---
# <a name="write-client-app-authentication-code"></a>编写客户端应用验证码

[设置 Azure 数字孪生实例和身份验证](how-to-set-up-instance-portal.md)后，可创建将用于与实例进行交互的客户端应用程序。 设置入门客户端项目后，需要针对 Azure 数字孪生实例在该客户端应用中编写代码以对应用进行身份验证。

Azure 数字孪生使用[基于 OAUTH 2.0 的 Azure AD 安全令牌](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)执行身份验证。 若要对 SDK 进行身份验证，需要获取具有 Azure 数字孪生正确权限的持有者令牌，并将其与 API 调用一起传递。 

本文介绍如何使用 `Azure.Identity` 客户端库获取凭据。 尽管本文介绍的是 C# 代码示例（如你为 [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) 编写的内容），但无论你使用的是何种 SDK，都可使用 `Azure.Identity` 版本（有关可用于 Azure 数字孪生的 SDK 的详细信息，请参阅操作指南：使用 Azure 数字孪生 API 和 SDK。

## <a name="prerequisites"></a>先决条件

首先，请完成操作指南：设置实例和身份验证中的设置步骤。 这将确保你拥有 Azure 数字孪生实例，并且你的用户具有访问权限。 完成此设置后，便可编写客户端应用代码。

若要继续，你需要一个可在其中编写代码的客户端应用项目。 如果尚未设置客户端应用项目，请使用你选择的语言创建一个基本项目，以在本教程中使用。

## <a name="common-authentication-methods-with-azureidentity"></a>采用 Azure.Identity 的常用身份验证方法

`Azure.Identity` 是一个客户端库，提供多种凭据获取方法，你可使用这些方法来获取持有者令牌并对 SDK 进行身份验证。 尽管本文提供的是 C# 示例，但你可查看多种语言的 `Azure.Identity`，其中包括：

* [.NET (C#)](/dotnet/api/azure.identity)
* [Java](/java/api/overview/azure/identity-readme)
* [JavaScript](/javascript/api/overview/azure/identity-readme)
* [Python](/python/api/overview/azure/identity-readme)

`Azure.Identity` 中的三种常用凭据获取方法是：

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 为即将部署到 Azure 的应用程序提供默认的 `TokenCredential` 身份验证流，若在本地部署，建议选择此方法。 还可启用它来尝试本文中建议的另外两种方法；它包装 `ManagedIdentityCredential` 并可使用配置变量访问 `InteractiveBrowserCredential`。
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) 非常适用于需要[托管标识 (MSI)](../active-directory/managed-identities-azure-resources/overview.md) 的情况，是使用 Azure Functions 和部署到 Azure 服务时的很好的候选项。
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) 适用于交互式应用程序，可用于创建经过身份验证的 SDK 客户端

以下示例演示如何在 .NET (C#) SDK 中使用这些方法。

## <a name="authentication-examples-net-c-sdk"></a>身份验证示例：NET (C#) SDK

本部分通过一个 C# 示例说明如何使用提供的 .NET SDK 编写验证码。

首先，将 SDK 包 `Azure.DigitalTwins.Core` 和包 `Azure.Identity` 包含在项目中。 可使用 Visual Studio 包管理器或 `dotnet` 命令行工具包含这些包，具体取决于你选择的工具。 

还需要向项目代码添加以下 using 语句：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

然后，添加代码以使用 `Azure.Identity` 中的某个方法来获取凭据。

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential 方法

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 为即将部署到 Azure 的应用程序提供默认的 `TokenCredential` 身份验证流，若在本地部署，建议选择此方法。

若要使用默认的 Azure 凭据，则需要 Azure 数字孪生实例的 URL（[查找说明](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)）。

下面是向项目添加 `DefaultAzureCredential` 的代码示例：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>设置本地 Azure 凭据

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential 方法

[ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) 方法适用于需要[托管标识 (MSI)](../active-directory/managed-identities-azure-resources/overview.md) 的情况，例如使用 Azure Functions 时。

这意味着，可在同一项目中像 `DefaultAzureCredential` 或 `InteractiveBrowserCredential` 一样使用 `ManagedIdentityCredential`，对项目的不同部分进行身份验证。

若要使用默认的 Azure 凭据，则需要 Azure 数字孪生实例的 URL（[查找说明](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)）。

在 Azure 函数中，可使用如下所示的托管标识凭据：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential 方法

[InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) 方法适用于交互式应用程序，并将打开一个 Web 浏览器进行身份验证。 如果需要交互式身份验证，可使用此方法而不是 `DefaultAzureCredential`。

若要使用交互式浏览器凭据，你将需要具有 Azure 数字孪生 API 权限的应用注册。 有关如何设置此应用注册的步骤，请参阅操作指南：创建应用注册。 设置应用注册后，需要以下内容：
* 应用注册的应用程序（客户端）ID（[查找说明](how-to-create-app-registration.md#collect-client-id-and-tenant-id)）
* 应用注册的目录（租户）ID（[查找说明](how-to-create-app-registration.md#collect-client-id-and-tenant-id)）
* Azure 数字孪生实例的 URL（[查找说明](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)）

下面是使用 `InteractiveBrowserCredential` 创建经过身份验证的 SDK 客户端的代码示例。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> 虽然你可将客户端 ID、租户 ID 和实例 URL 直接放置在代码中（如上所示），但建议让代码从配置文件或环境变量中获取这些值。

#### <a name="other-notes-about-authenticating-azure-functions"></a>有关 Azure Functions 身份验证的其他说明

请参阅操作指南：设置用于处理数据的 Azure 函数以获取更完整的示例，该示例介绍函数上下文中的一些重要配置选项。

此外，若要在函数中使用身份验证，请记得：
* [启用托管标识](../app-service/overview-managed-identity.md?tabs=dotnet)
* 使用[环境变量](/sandbox/functions-recipes/environment-variables?tabs=csharp)（视情况而定）
* 向函数应用分配权限，使其能够访问数字孪生 API。 有关 Azure Functions 过程的详细信息，请参阅操作指南：设置用于处理数据的 Azure 函数。

## <a name="authenticate-across-tenants"></a>跨租户进行身份验证

Azure 数字孪生是一项仅支持一个 [Azure Active Directory (Azure AD) 租户](../active-directory/develop/quickstart-create-new-tenant.md)的服务：Azure 数字孪生实例所在的订阅的主租户。

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

如果需要使用属于实例中不同租户的服务主体或用户帐户来访问 Azure 数字孪生实例，可以让其他租户的每个联合标识从 Azure 数字孪生实例的“主”租户请求令牌。 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

还可以在代码的凭据选项中指定主租户。 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>其他凭据方法

如果上面突出显示的身份验证方案不能满足你的应用需求，可浏览 Microsoft 标识平台中提供的其他类型的身份验证。 此平台的文档介绍了按应用程序类型组织的更多身份验证方案。

## <a name="next-steps"></a>后续步骤

详细了解 Azure 数字孪生中安全性的工作原理：
* [概念：Azure 数字孪生解决方案的安全性](concepts-security.md)

或者，现在身份验证已设置，接下来请在实例中创建和管理模型：
* [操作指南：管理 DTDL 模型](how-to-manage-model.md)