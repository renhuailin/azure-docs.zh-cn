---
title: 从 Azure CLI 创建 Azure Active Directory 托管标识应用程序
titleSuffix: An Azure Communication Services quickstart
description: 通过托管标识，你可以从 Azure Vm 中运行的应用程序、函数应用和其他资源授权 Azure 通信服务访问。
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 02/25/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: f60c7b0b4c89833d489a435fde85896a8f8bd72f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662194"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>使用托管标识向开发环境中的通信资源授权访问权限

Azure 标识客户端库提供了 Azure Active Directory (Azure AD Azure SDK) 令牌身份验证支持。 适用于 .NET、Java、Python 和 JavaScript 的 Azure 通信服务客户端库的最新版本与 Azure 标识库集成，提供一种简单而安全的方法来获取用于授权 Azure 通信服务请求的 OAuth 2.0 令牌。

Azure 标识客户端库的优点在于，无论应用程序是在开发环境中运行还是在 Azure 中运行，都可以使用相同的代码跨多个服务进行身份验证。 Azure 标识客户端库对安全主体进行身份验证。 代码在 Azure 中运行时，安全主体是 Azure 资源的托管标识。 在开发环境中，不存在托管标识，因此客户端库将对用户或已注册的应用程序进行身份验证，以便进行测试。

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
- [应用服务](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>对开发环境中的已注册应用程序进行身份验证

如果开发环境不支持单一登录或通过 web 浏览器登录，则可以使用已注册的应用程序从开发环境进行身份验证。

### <a name="creating-an-azure-active-directory-registered-application"></a>创建 Azure Active Directory 注册的应用程序

若要从 Azure CLI 创建已注册的应用程序，需要登录到要在其中进行操作的 Azure 帐户。 为此，可以使用 `az login` 命令，并在浏览器中输入你的凭据。 从 CLI 登录到 Azure 帐户后，可以调用 `az ad sp create-for-rbac` 命令创建已注册的应用程序。

以下示例使用 Azure CLI 创建新的已注册应用程序

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

`az ad sp create-for-rbac`命令将返回 JSON 格式的服务主体属性列表。 复制这些值，以便在下一步中使用它们来创建必要的环境变量。

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
|`AZURE_CLIENT_ID`|`appId` 生成的 JSON 中的值 
|`AZURE_TENANT_ID`|`tenant` 生成的 JSON 中的值
|`AZURE_CLIENT_SECRET`|`password` 生成的 JSON 中的值

> [!IMPORTANT]
> 设置环境变量后，关闭并重新打开控制台窗口。 如果你使用的是 Visual Studio 或其他开发环境，则可能需要重新启动它，才能注册新的环境变量。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解身份验证](../concepts/authentication.md)

你可能还想要：

- [详细了解 Azure 标识库](/dotnet/api/overview/azure/identity-readme)
