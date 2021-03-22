---
ms.openlocfilehash: bb3925c5c642f2a6d00f8f5b7fe6471676c23c30
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486574"
---
## <a name="add-managed-identity-to-your-communication-services-solution-java"></a>将托管标识添加到通信服务解决方案 (Java)

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

向代码添加以下 `import` 指令，以使用 Azure 标识和 Azure 通信客户端库。

```java
import com.azure.identity.*;
import com.azure.communication.sms.*;
import com.azure.communication.identity.*;
import com.azure.communication.common.*;
```

下面的示例使用的是 [DefaultAzureCredential](/java/api/azure.identity.defaultazurecredential)。 此凭据适用于生产环境和开发环境。

需要 `AZURE_CLIENT_SECRET`、`AZURE_CLIENT_ID` 和 `AZURE_TENANT_ID` 环境变量才能创建 `DefaultAzureCredential` 对象。 若要在开发环境中创建注册应用程序并设置环境变量，请参阅[使用托管标识授予访问权限](../managed-identity-from-cli.md)。

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>创建一个标识，并使用托管标识颁发令牌

下面的代码示例演示如何使用托管标识创建服务客户端对象。
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

下面的代码示例演示如何使用托管标识创建服务客户端对象，然后使用客户端发送短信：

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

