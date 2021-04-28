---
title: 将调用 Web API 的守护程序应用移到生产环境 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何将调用 Web API 的守护程序应用移到生产环境
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e53ae26e4ed5bf42a42daa527c81949c876ab5e1
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064010"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>调用 Web API 的守护程序应用 - 移到生产环境

了解如何获取并使用用于服务到服务调用的令牌以后，即可学习如何将应用移到生产环境。

## <a name="deployment---multitenant-daemon-apps"></a>部署 - 多租户守护程序应用

如果你是 ISV，要创建可以在多个租户中运行的守护程序应用程序，需要确保租户管理员执行以下操作：

- 为应用程序预配服务主体。
- 授予对应用程序的许可。

需向客户解释如何执行这些操作。 有关详细信息，请参阅[请求整个租户的许可](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>代码示例

# <a name="net"></a>[.NET](#tab/dotnet)

- 以下内容的参考文档：
  - 实例化 [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)。
  - 调用 [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder?view=azure-dotnet&preserve-view=true)。
- 其他示例/教程：
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) 提供一个较小的 .NET Core 守护程序控制台应用程序，该应用程序显示某个租户的用户在查询 Microsoft Graph。

    ![示例守护程序应用拓扑](media/scenario-daemon-app/daemon-app-sample.svg)

    同一示例还通过证书演示了变体：

    ![示例守护程序应用拓扑 - 证书](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) 具有一个 ASP.NET MVC Web 应用程序，该应用程序使用应用程序的标识来同步 Microsoft Graph 的数据，而不是代表用户来同步。 此示例还演示了管理员许可过程。

    ![拓扑](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="java"></a>[Java](#tab/java)

尝试使用快速入门：[使用应用的标识获取令牌并从 Java 控制台应用中调用 Microsoft Graph API](quickstart-v2-java-daemon.md)。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

- 有关详细信息，请参阅：
  - 了解[配置](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)
  - 实例化 [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)
  - [常见问题解答](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/faq.md)
- 其他示例/教程：
  - [MSAL 节点控制台守护程序示例](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console)

# <a name="python"></a>[Python](#tab/python)

尝试快速入门 [使用应用的标识获取令牌并从 Python 控制台应用中调用 Microsoft Graph API](quickstart-v2-python-daemon.md)。

---

## <a name="next-steps"></a>后续步骤

下面是一些介绍详细信息的链接：

# <a name="net"></a>[.NET](#tab/dotnet)

尝试使用快速入门：[使用应用的标识获取令牌并从 .NET Core 控制台应用中调用 Microsoft Graph API](quickstart-v2-netcore-daemon.md)。

# <a name="java"></a>[Java](#tab/java)

尝试使用快速入门：[使用应用的标识获取令牌并从 Java 控制台应用中调用 Microsoft Graph API](quickstart-v2-java-daemon.md)。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

尝试使用快速入门：[使用应用的标识获取令牌并从 Node.js 控制台应用中调用 Microsoft Graph API](quickstart-v2-nodejs-console.md)。

# <a name="python"></a>[Python](#tab/python)

尝试快速入门 [使用应用的标识获取令牌并从 Python 控制台应用中调用 Microsoft Graph API](quickstart-v2-python-daemon.md)。

---
