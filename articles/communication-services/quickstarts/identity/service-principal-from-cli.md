---
title: 从 Azure CLI 创建 Azure Active Directory 服务主体
titleSuffix: An Azure Communication Services quickstart
description: 在本快速入门中，我们将创建应用程序和服务主体，以便使用 Azure 通信服务进行身份验证。
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.subservice: identity
ms.topic: quickstart
ms.date: 06/30/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: c2b8dd4a40205178b3656477e89189d6f0f0bfeb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635520"
---
# <a name="quickstart-authenticate-using-azure-active-directory-azure-cli"></a>快速入门：使用 Azure Active Directory (Azure CLI) 进行身份验证

Azure 标识 SDK 为 Azure SDK 包提供 Azure Active Directory (Azure AD) 令牌身份验证支持。 适用于 .NET、Java、Python 和 JavaScript 的最新版本 Azure 通信服务 SDK 与 Azure 标识库集成，提供了一种简单而安全的方法来获取用于授权 Azure 通信服务请求的 OAuth 2.0 令牌。

Azure 标识 SDK 的优点在于，它使你可以使用相同的代码跨多项服务验证你的应用程序是在开发环境中运行还是在 Azure 中运行。 

Azure 标识 SDK 可以通过多种方法进行身份验证。 在开发中，我们将使用绑定到已注册应用程序的服务主体，并将凭据存储在环境变量中，这适用于测试和开发环境。

## <a name="prerequisites"></a>先决条件

 - Azure CLI。 [安装指南](/cli/azure/install-azure-cli)
 - 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)

## <a name="setting-up"></a>设置

将 Active Directory 用于其他 Azure 资源时，应使用托管标识。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器 SDK](../../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [应用程序服务](../../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>在开发环境中对已注册的应用程序进行身份验证

如果开发环境不支持单一登录或通过 Web 浏览器登录，可以使用已注册的应用程序从开发环境进行身份验证。

### <a name="creating-an-azure-active-directory-registered-application"></a>创建 Azure Active Directory 已注册的应用程序

若要通过 Azure CLI 创建已注册的应用程序，需要登录到要在其中进行操作的 Azure 帐户。 为此，可以使用 `az login` 命令，并在浏览器中输入你的凭据。 使用 CLI 登录到 Azure 帐户后，可以调用 `az ad sp create-for-rbac` 命令创建已注册的应用程序和服务主体。

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

Azure 标识 SDK 会在运行时读取三个环境变量中的值，以对应用程序进行身份验证。 下表介绍了为每个环境变量设置的值。

| 环境变量  | Value                                    |
| --------------------- | ---------------------------------------- |
| `AZURE_CLIENT_ID`     | 生成的 JSON 中的 `appId` 值    |
| `AZURE_TENANT_ID`     | 生成的 JSON 中的 `tenant` 值   |
| `AZURE_CLIENT_SECRET` | 生成的 JSON 中的 `password` 值 |

> [!IMPORTANT]
> 设置环境变量后，关闭并重新打开控制台窗口。 如果使用的是 Visual Studio 或其他开发环境，则可能需要重启以便环境能够注册新的环境变量。

设置这些变量后，应该就能够在代码中使用 DefaultAzureCredential 对象向你选择的服务客户端进行身份验证。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解身份验证](../../concepts/authentication.md)

你可能还想要：

- [详细了解 Azure 标识库](/dotnet/api/overview/azure/identity-readme)
