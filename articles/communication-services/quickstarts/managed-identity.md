---
title: " ( .NET) 使用通信服务中的托管标识"
titleSuffix: An Azure Communication Services quickstart
description: 通过托管标识，你可以从 Azure Vm 中运行的应用程序、函数应用和其他资源授权 Azure 通信服务访问。
services: azure-communication-services
author: stefang931
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 12/04/2020
ms.author: gistefan
ms.reviewer: mikben
ms.openlocfilehash: ee691d4809a68a0ba60f60a2240b76a1e53104bc
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171567"
---
# <a name="use-managed-identities-net"></a>使用托管标识 (.NET)

使用 .NET 中的托管标识开始使用 Azure 通信服务。 通信服务管理和 SMS 客户端库支持 Azure Active Directory (通过 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)Azure AD) 身份验证。

本快速入门介绍如何从支持托管标识的 Azure 环境中向管理和 SMS 客户端库授予访问权限。 还介绍了如何在开发环境中测试代码。

## <a name="prerequisites"></a>先决条件

 - 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)
 - 活动的通信服务资源和连接字符串。 [创建通信服务资源](./create-communication-resource.md?pivots=platform-azp&tabs=windows)。

## <a name="setting-up"></a>设置

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>在虚拟机或应用服务上启用托管标识

应在你授权的 Azure 资源上启用托管标识。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [应用服务](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>将 Azure 角色分配到 Azure 门户

1. 导航到 Azure 门户。
1. 导航到 Azure 通信服务资源。
1. 导航到访问控制 (IAM) 菜单-> + 添加-> 添加角色分配。
1. 选择 "参与者" 角色 (这是唯一受支持的角色) 。
1. 选择 "用户分配的托管标识" (或 "系统分配的托管标识" ) 然后选择所需的标识。 保存所选内容。

![托管标识角色](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>通过 PowerShell 分配 Azure 角色

若要使用 PowerShell 分配角色和权限，请参阅 [使用 Azure PowerShell 添加或删除 Azure 角色分配](../../../articles/role-based-access-control/role-assignments-powershell.md)

## <a name="add-managed-identity-to-your-communication-services-solution"></a>将托管标识添加到通信服务解决方案

### <a name="install-the-client-library-packages"></a>安装客户端库包

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Configuration
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>使用客户端库包

向代码添加以下 `using` 指令，以便使用 Azure 标识和 Azure 存储客户端库。

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Configuration;
using Azure.Communication.Sms;
```

下面的示例使用的是 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)。 此凭据适用于生产环境和开发环境。

### <a name="create-an-identity-and-issue-a-token"></a>创建标识并颁发令牌

下面的代码示例演示如何创建具有 Azure Active Directory 令牌的服务客户端对象，然后使用客户端为新用户颁发令牌：

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndIssueTokenAsync(Uri resourceEndpoint) 
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
     
          var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-azure-active-directory-tokens"></a>使用 Azure Active Directory 令牌发送短信

下面的代码示例演示如何创建具有 Azure Active Directory 令牌的服务客户端对象，然后使用客户端发送短信：

```csharp

     public async Task SendSmsAsync(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解身份验证](../concepts/authentication.md)

你可能还想要：

- [详细了解 Azure 基于角色的访问控制](../../../articles/role-based-access-control/index.yml)
- [详细了解适用于 .NET 的 Azure 标识库](/dotnet/api/overview/azure/identity-readme)
- [创建用户访问令牌](../quickstarts/access-tokens.md)
- [发送短信](../quickstarts/telephony-sms/send.md)
- [了解有关短信的详细信息](../concepts/telephony-sms/concepts.md)