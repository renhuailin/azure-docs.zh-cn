---
ms.openlocfilehash: 2c816f005dea452c3ffa2889aa7d2742a5762759
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657605"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a> ( .Net) 将托管标识添加到通信服务解决方案

### <a name="install-the-client-library-packages"></a>安装客户端库包

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>使用客户端库包

向代码添加以下 `using` 指令，以便使用 Azure 标识和 Azure 存储客户端库。

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

下面的示例使用的是 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)。 此凭据适用于生产环境和开发环境。

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`需要和 `AZURE_TENANT_ID` 环境变量来创建 `DefaultAzureCredential` 对象。 若要在开发环境中创建已注册的应用程序并设置环境变量，请参阅 [使用托管标识授予访问权限](../managed-identity-from-cli.md)。

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>创建一个标识，并使用托管标识颁发令牌

下面的代码示例演示如何创建具有 Azure Active Directory 标记的服务客户端对象。

然后，使用客户端为新用户颁发令牌：

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndGetTokenAsync(Uri resourceEdnpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>使用托管标识发送短信

下面的代码示例演示如何使用托管标识创建 SMS 服务客户端对象，然后使用客户端发送短信：

```csharp
     public async Task SendSms(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

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

- [详细了解 Azure 基于角色的访问控制](../../../../articles/role-based-access-control/index.yml)
- [详细了解适用于 .NET 的 Azure 标识库](/dotnet/api/overview/azure/identity-readme)
- [创建用户访问令牌](../../quickstarts/access-tokens.md)
- [发送短信](../telephony-sms/send.md)
- [了解有关短信的详细信息](../../concepts/telephony-sms/concepts.md)
