---
ms.openlocfilehash: 3626ca4cc3e7377f1c6778bc77e5e48ef0dcad0c
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2021
ms.locfileid: "103439058"
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
import { CommunicationIdentityClient, CommunicationUserToken } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

下面的示例使用的是 [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential)。 此凭据适用于生产环境和开发环境。

若要在开发环境中注册应用程序并设置环境变量，请参阅[使用托管标识授予访问权限](../managed-identity-from-cli.md)  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>创建一个标识，并使用托管标识颁发令牌

下面的代码示例演示如何使用托管标识创建服务客户端对象，然后使用客户端为新用户颁发令牌：

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserAndToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>使用托管标识发送短信

下面的代码示例演示如何使用托管标识创建服务客户端对象，然后使用客户端发送短信：

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SmsSendRequest = { 
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

