---
title: 通过 Azure CLI 创建 Azure Active Directory 托管标识应用程序
titleSuffix: An Azure Communication Services quickstart
description: 通过托管标识，你可以授权 Azure 通信服务从 Azure VM、函数应用和其他资源中运行的应用程序进行访问。 本快速入门重点介绍如何使用 Azure CLI 管理标识。
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: e708536395807fc74dc5bfd73836e050832cca39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493333"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>使用托管标识向开发环境中的通信资源授予访问权限

Azure 标识客户端库为 Azure SDK 提供 Azure Active Directory (Azure AD) 令牌身份验证支持。 适用于 .NET、Java、Python 和 JavaScript 的最新版本的 Azure 通信服务客户端库与 Azure 标识库集成，提供了一种简单而安全的方法来获取用于授权 Azure 通信服务请求的 OAuth 2.0 令牌。

Azure 标识客户端库的优点在于，它使你可以使用相同的代码跨多项服务验证你的应用程序是在开发环境中运行还是在 Azure 中运行。 Azure 标识客户端库对安全主体进行身份验证。 代码在 Azure 中运行时，安全主体是 Azure 资源的托管标识。 在开发环境中，不存在托管标识，因此客户端库将对用户或已注册的应用程序进行身份验证，以便进行测试。

## <a name="prerequisites"></a>先决条件

 - Azure CLI。 [安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)
 - 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)

## <a name="setting-up"></a>设置

应在你授权的 Azure 资源上启用托管标识。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [应用程序服务](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>在开发环境中对已注册的应用程序进行身份验证

如果开发环境不支持单一登录或通过 Web 浏览器登录，可以使用已注册的应用程序从开发环境进行身份验证。

### <a name="creating-an-azure-active-directory-registered-application"></a>创建 Azure Active Directory 已注册的应用程序

若要通过 Azure CLI 创建已注册的应用程序，需要登录到要在其中进行操作的 Azure 帐户。 为此，可以使用 `az login` 命令，并在浏览器中输入你的凭据。 使用 CLI 登录到 Azure 帐户后，可以调用 `az ad sp create-for-rbac` 命令创建已注册的应用程序。

以下示例使用 Azure CLI 创建新的已注册的应用程序

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

`az ad sp create-for-rbac` 命令将返回 JSON 格式的服务主体属性列表。 复制这些值，以便在下一步中使用它们来创建必要的环境变量。

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> 传播 Azure 角色分配可能需要几分钟的时间。

#### <a name="set-environment-variables"></a>设置环境变量。

Azure 标识客户端库会在运行时读取三个环境变量中的值，以对应用程序进行身份验证。 下表介绍了为每个环境变量设置的值。

|环境变量|Value
|-|-
|`AZURE_CLIENT_ID`|生成的 JSON 中的 `appId` 值 
|`AZURE_TENANT_ID`|生成的 JSON 中的 `tenant` 值
|`AZURE_CLIENT_SECRET`|生成的 JSON 中的 `password` 值

> [!IMPORTANT]
> 设置环境变量后，关闭并重新打开控制台窗口。 如果使用的是 Visual Studio 或其他开发环境，则可能需要重启以便环境能够注册新的环境变量。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解身份验证](../concepts/authentication.md)

你可能还想要：

- [详细了解 Azure 标识库](/dotnet/api/overview/azure/identity-readme)
