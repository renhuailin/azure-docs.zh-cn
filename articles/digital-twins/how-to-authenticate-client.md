---
title: 编写应用身份验证码
titleSuffix: Azure Digital Twins
description: 请参阅如何在客户端应用程序中编写身份验证代码
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5a7cfec6acb4cd8735c039f5eab30bac4ccf55b0
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556139"
---
# <a name="write-client-app-authentication-code"></a>编写客户端应用身份验证代码

[设置 Azure 数字孪生实例和身份验证](how-to-set-up-instance-portal.md)后，可以创建将用于与实例进行交互的客户端应用程序。 设置入门客户端项目后，需要 **在该客户端应用程序中编写代码，以** 对 Azure 数字孪生实例进行身份验证。

Azure 数字孪生使用 [基于 OAUTH 2.0 Azure AD 安全令牌](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)执行身份验证。 若要对你的 SDK 进行身份验证，你将需要获取具有 Azure 数字孪生权限的持有者令牌，并将其传递给你的 API 调用。 

本文介绍如何使用 `Azure.Identity` 客户端库获取凭据。 尽管本文介绍了 c # 中的代码示例（如你编写的 [.net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)），但你可以使用版本的，无论你使用的是 `Azure.Identity` 何种 sdk (有关可用于 Azure 数字孪生的 sdk 的详细信息，请参阅 [*如何：使用 azure 数字孪生 api 和 sdk*](how-to-use-apis-sdks.md)) 。

## <a name="prerequisites"></a>先决条件

首先，请完成 [*操作方法：设置实例和身份验证*](how-to-set-up-instance-portal.md)中的设置步骤。 这将确保你有 Azure 数字孪生实例，并且你的用户具有访问权限。 完成此设置后，就可以编写客户端应用代码了。

若要继续，你需要一个客户端应用程序项目，可在其中编写代码。 如果尚未设置客户端应用程序项目，请使用与本教程一起使用的所选语言创建基本项目。

## <a name="common-authentication-methods-with-azureidentity"></a>采用 Azure 身份验证的常见身份验证方法

`Azure.Identity` 是一个客户端库，提供若干凭据获取方法，你可以使用这些方法来获取持有者令牌并使用 SDK 进行身份验证。 尽管本文提供了 c # 中的示例，但你可以查看 `Azure.Identity` 多种语言，其中包括 .。。

* [.NET (C#)](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet)
* [Java](/java/api/overview/azure/identity-readme?preserve-view=true&view=azure-java-stable)
* [JavaScript](/javascript/api/overview/azure/identity-readme?preserve-view=true&view=azure-node-latest)
* [Python](/python/api/overview/azure/identity-readme?preserve-view=true&view=azure-python)

中有三个常见的凭据获取方法 `Azure.Identity` ：

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) 为 `TokenCredential` 将部署到 Azure 的应用程序提供默认的身份验证流，这是 **用于本地开发的推荐选择**。 还可以启用它以尝试本文中建议的其他两种方法;它 `ManagedIdentityCredential` 使用配置变量进行包装和访问 `InteractiveBrowserCredential` 。
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet) 非常适用于你需要 [ (MSI) 的托管标识 ](../active-directory/managed-identities-azure-resources/overview.md)，这是使用 Azure Functions 和部署到 Azure 服务的良好候选项。
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet) 适用于交互式应用程序，可用于创建经过身份验证的 SDK 客户端

下面的示例演示如何将其中的每个与 .NET (c # ) SDK 一起使用。

## <a name="authentication-examples-net-c-sdk"></a>身份验证示例： .NET (c # ) SDK

本部分介绍使用提供的 .NET SDK 编写身份验证代码的 c # 示例。

首先，将 SDK 包 `Azure.DigitalTwins.Core` 和包包含 `Azure.Identity` 在项目中。 根据您选择的工具，您可以使用 Visual Studio 包管理器或 `dotnet` 命令行工具包含包。 

还需要向项目代码添加以下 using 语句：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

然后，添加代码以使用中的方法之一来获取凭据 `Azure.Identity` 。

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential 方法

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) 为 `TokenCredential` 将部署到 Azure 的应用程序提供默认的身份验证流，这是 **用于本地开发的推荐选择**。

若要使用默认 Azure 凭据，需要使用 Azure 数字孪生实例的 URL ([说明查找](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) 。

下面是将添加到项目的代码示例 `DefaultAzureCredential` ：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>设置本地 Azure 凭据

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential 方法

如果需要[ (MSI) 的托管标识](../active-directory/managed-identities-azure-resources/overview.md)（例如，使用 Azure Functions 时）， [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet)方法非常有用。

这意味着，可以在与 `ManagedIdentityCredential` 或相同的项目中 `DefaultAzureCredential` 使用 `InteractiveBrowserCredential` ，以对项目的不同部分进行身份验证。

若要使用默认 Azure 凭据，需要使用 Azure 数字孪生实例的 URL ([说明查找](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) 。

在 Azure 函数中，可以使用如下所示的托管标识凭据：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential 方法

[InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet)方法适用于交互式应用程序，并将打开一个 web 浏览器用于身份验证。 如果需要交互式身份验证，则可以使用此来代替 `DefaultAzureCredential` 。

若要使用交互式浏览器凭据，你将需要具有 Azure 数字孪生 Api 权限的 **应用注册** 。 有关如何设置此应用注册的步骤，请参阅 [*如何：创建应用注册*](how-to-create-app-registration.md)。 设置应用注册后，需要 .。。
* 应用注册的 *应用程序 (客户端) ID* ([查找](how-to-create-app-registration.md#collect-client-id-and-tenant-id)) 
* 应用注册的 *目录 (租户) ID* ([查找](how-to-create-app-registration.md#collect-client-id-and-tenant-id)) 
* Azure 数字孪生实例的 URL ([查找](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) 

下面是使用创建经过身份验证的 SDK 客户端的代码示例 `InteractiveBrowserCredential` 。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> 虽然你可以将客户端 ID、租户 ID 和实例 URL 直接放置在代码中（如上所示），但最好是让你的代码从配置文件或环境变量获取这些值。

#### <a name="other-notes-about-authenticating-azure-functions"></a>有关身份验证的其他说明 Azure Functions

请参阅 [*如何：设置用于处理数据的 Azure 函数*](how-to-create-azure-function.md) 以获取更完整的示例，该示例介绍函数上下文中的一些重要配置选项。

此外，若要在函数中使用身份验证，请记住：
* [启用托管标识](../app-service/overview-managed-identity.md?tabs=dotnet)
* 使用适当的[环境变量](/sandbox/functions-recipes/environment-variables?tabs=csharp)
* 向函数应用分配权限，使其能够访问数字孪生 Api。 有关 Azure Functions 过程的详细信息，请参阅 [*如何：设置用于处理数据的 Azure 函数*](how-to-create-azure-function.md)。

## <a name="other-credential-methods"></a>其他凭据方法

如果上面突出显示的身份验证方案不满足你的应用程序的需求，则可以浏览 [**Microsoft 标识平台**](../active-directory/develop/v2-overview.md#getting-started)中提供的其他类型的身份验证。 此平台的文档介绍了按应用程序类型组织的其他身份验证方案。

## <a name="next-steps"></a>后续步骤

详细了解如何在 Azure 数字孪生中运行安全性：
* [*概念： Azure 数字孪生解决方案的安全性*](concepts-security.md)

或者，现在已经设置了身份验证，接下来请在实例中创建和管理模型：
* [*操作说明：管理 DTDL 模型*](how-to-manage-model.md)