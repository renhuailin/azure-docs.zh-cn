---
ms.openlocfilehash: 0fd97fe0eebb23130513409698b75d2ee7b98a6f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657603"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>将托管标识添加到通信服务解决方案 (JS) 

### <a name="install-the-client-library-packages"></a>安装客户端库包

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-client-library-packages"></a>使用客户端库包

向代码添加以下 `import` 指令，以便使用 Azure 标识和 Azure 存储客户端库。

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { SmsClient } from "@azure/communication-sms";
```

下面的示例使用的是 [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential)。 此凭据适用于生产环境和开发环境。

若要在开发环境中注册应用程序并设置环境变量，请参阅 [使用托管标识授予访问权限](../managed-identity-from-cli.md)  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>创建一个标识，并使用托管标识颁发令牌

下面的代码示例演示如何创建具有托管标识的服务客户端对象，然后使用客户端为新用户颁发令牌：

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserWithToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>使用托管标识发送短信

下面的代码示例演示如何创建具有托管标识的服务客户端对象，然后使用客户端发送短信：

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SendRequest = { 
          from: fromNumber, 
          to: [toNumber], 
          message: message 
     };

     const response = await smsClient.send(
          sendRequest, 
          {} //Optional SendOptions
          );
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解身份验证](../concepts/authentication.md)

你可能还想要：

- [详细了解 Azure 基于角色的访问控制](../../../../articles/role-based-access-control/index.yml)
- [详细了解用于 JS 的 Azure 标识库](/javascript/api/overview/azure/identity-readme)
- [创建用户访问令牌](../../quickstarts/access-tokens.md)
- [发送短信](../../quickstarts/telephony-sms/send.md)
- [了解有关短信的详细信息](../../concepts/telephony-sms/concepts.md)

