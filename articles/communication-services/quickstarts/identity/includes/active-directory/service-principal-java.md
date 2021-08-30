---
ms.openlocfilehash: 4ca3be985b0f4821e18607a5815c0237ad3bf0f0
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656978"
---
## <a name="additional-prerequisites-for-java"></a>Java 的其他必备组件
对于 Java，还需要：
- [Java 开发工具包 (JDK)](/azure/developer/java/fundamentals/java-jdk-install) 8 或更高版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。

> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/use-managed-Identity) 上查找此快速入门的最终代码

## <a name="setting-up"></a>设置

### <a name="create-a-new-java-application"></a>创建新的 Java 应用程序

打开终端或命令窗口。 导航到要在其中创建 Java 应用程序的目录。 运行以下命令以从 maven-archetype-quickstart 模板生成 Java 项目。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

你会注意到，“生成”任务创建了与 `artifactId` 名称相同的目录。 在此目录下，src/main/java 目录包含项目源代码，`src/test/java directory` 包含测试源，`pom.xml` 文件是项目的项目对象模型 (POM)。

### <a name="install-the-package"></a>安装包

在文本编辑器中打开 pom.xml 文件。 将以下依赖项元素添加到依赖项组。

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

### <a name="use-the-sdk-packages"></a>使用 SDK 包

向代码添加以下 `import` 指令，以使用 Azure 标识和 Azure 通信 SDK。

```java
import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.communication.sms.models.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.util.*;
```

## <a name="create-a-defaultazurecredential"></a>创建 DefaultAzureCredential

我们将在本快速入门中使用 [DefaultAzureCredential](/java/api/com.azure.identity.defaultazurecredential)。 此凭据适用于生产环境和开发环境。 由于每个操作都需要，因此可以在 `App.java` 类中创建它。 在 `App.java` 类的顶部，添加以下内容。

```java
private TokenCredential credential = new DefaultAzureCredentialBuilder().build();
```

## <a name="issue-a-token-with-service-principals"></a>使用服务主体颁发令牌

现在，我们将添加使用已创建的凭据的代码来颁发 VoIP 访问令牌。 稍后我们将调用此代码；

```java
    public AccessToken createIdentityAndGetTokenAsync(String endpoint) {
          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          return communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
    }
```

## <a name="send-an-sms-with-service-principals"></a>使用服务主体发送短信

作为另一个使用服务主体的示例，我们将添加此代码，该代码使用相同的凭据来发送短信：

```java
     public SmsSendResult sendSms(String endpoint, String from, String to, String message) {
          SmsClient smsClient = new SmsClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          // Send the message and check the response for a message id
          return smsClient.send(from, to, message);
     }
```
## <a name="write-the-main-method"></a>编写 Main 方法

你的 `App.java` 应该已经有了一个 Main 方法，让我们添加一些代码，这些代码将调用以前创建的代码来演示如何使用服务主体：
```java
    public static void main(String[] args) {
          App instance = new App();
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com/";

          System.out.println("Retrieving new Access Token, using Service Principals");
          AccessToken token = instance.createIdentityAndGetTokenAsync(endpoint);
          System.out.println("Retrieved Access Token: "+ token.getToken());

          System.out.println("Sending SMS using Service Principals");
          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Service Principals");
          System.out.println("Sms id: "+ result.getMessageId());
          System.out.println("Send Result Successful: "+ result.isSuccessful());
    }
```

最终 `App.java` 应如下所示：

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.communication.sms.models.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.util.*;

public class App 
{

    private TokenCredential credential = new DefaultAzureCredentialBuilder().build();

    public SmsSendResult sendSms(String endpoint, String from, String to, String message) {
          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(this.credential)
               .buildClient();

          // Send the message and check the response for a message id
          return smsClient.send(from, to, message);
    }
    public AccessToken createIdentityAndGetTokenAsync(String endpoint) {
          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          return communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
    }

    public static void main(String[] args) {
          App instance = new App();
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com/";

          System.out.println("Retrieving new Access Token, using Service Principals");
          AccessToken token = instance.createIdentityAndGetTokenAsync(endpoint);
          System.out.println("Retrieved Access Token: "+ token.getToken());

          System.out.println("Sending SMS using Service Principals");
          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Service Principals");
          System.out.println("Sms id: "+ result.getMessageId());
          System.out.println("Send Result Successful: "+ result.isSuccessful());
    }
}
```

## <a name="run-the-code"></a>运行代码

导航到包含 pom.xml 文件的目录，并使用以下 `mvn` 命令编译该项目。

```console
mvn compile
```

然后，生成包。

```console
mvn package
```

运行以下 `mvn` 命令以执行应用。

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

最终输出应如下所示：
```
Retrieving new Access Token, using Service Principals
Retrieved Access Token: ey..A
Sending SMS using using Service Principals
Sms id: Outgoing_202104...33f8ae1f_noam
Send Result Successful: true
```