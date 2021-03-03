---
ms.openlocfilehash: c5d83cc709c334e15a1c13e64ba17ef24835fed0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657606"
---
## <a name="add-managed-identity-to-your-communication-services-solution-java"></a> (Java) 将托管标识添加到通信服务解决方案

### <a name="install-the-client-library-packages"></a>安装客户端库包
在 pom.xml 文件中，将以下依赖项元素添加到依赖项组。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="use-the-client-library-packages"></a>使用客户端库包

将以下 `import` 指令添加到代码以使用 Azure 标识和 Azure 通信客户端库。

```java
import com.azure.identity.*;
import com.azure.communication.sms.*;
import com.azure.communication.identity.*;
import com.azure.communication.common.*;
```

下面的示例使用的是 [DefaultAzureCredential](/java/api/azure.identity.defaultazurecredential)。 此凭据适用于生产环境和开发环境。

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`需要和 `AZURE_TENANT_ID` 环境变量来创建 `DefaultAzureCredential` 对象。 若要在开发环境中创建已注册的应用程序并设置环境变量，请参阅 [使用托管标识授予访问权限](../managed-identity-from-cli.md)。

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>创建一个标识，并使用托管标识颁发令牌

下面的代码示例演示如何创建具有托管标识的服务客户端对象。
然后，使用客户端为新用户颁发令牌：

```java
     public AccessToken createIdentityAndGetTokenAsync() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          AccessToken userToken = communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
          return userToken;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>使用托管标识发送短信

下面的代码示例演示如何创建具有托管标识的服务客户端对象，然后使用客户端发送短信：

```java
     public SendSmsResponse sendSms() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          // Send the message and check the response for a message id
          SendSmsResponse response = smsClient.sendMessage(
               new PhoneNumberIdentifier("<leased-phone-number>"),
               to,
               "your message",
               options /* Optional */
          );
          return response;
    }
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解身份验证](../concepts/authentication.md)

你可能还想要：

- [详细了解 Azure 基于角色的访问控制](../../../../articles/role-based-access-control/index.yml)
- [详细了解用于 Java 的 Azure 标识库](/java/api/overview/azure/identity-readme)
- [创建用户访问令牌](../../quickstarts/access-tokens.md)
- [发送短信](../../quickstarts/telephony-sms/send.md)
- [了解有关短信的详细信息](../../concepts/telephony-sms/concepts.md)
