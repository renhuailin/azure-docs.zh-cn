---
ms.openlocfilehash: f34320c0ba70da94315858ed09ffd1ca3e90d846
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656969"
---
> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/use-managed-Identity) 上查找此快速入门的最终代码

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `ActiveDirectoryQuickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：`Program.cs`。

```console
dotnet new console -o ActiveDirectoryAuthenticationQuickstart
```

将目录更改为新创建的应用文件夹，并使用 `dotnet build` 命令编译应用程序。

```console
cd ActiveDirectoryAuthenticationQuickstart
dotnet build
```

### <a name="install-the-sdk-packages"></a>安装 SDK 包

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-sdk-packages"></a>使用 SDK 包

向 `Program.cs` 添加以下 `using` 指令，以使用 Azure 标识和 Azure 存储 SDK。

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
using Azure;
```

## <a name="create-a-defaultazurecredential"></a>创建 DefaultAzureCredential

我们将在本快速入门中使用 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)。 此凭据适用于生产环境和开发环境。 由于每个操作都需要，因此可以在 `Program.cs` 类中创建它。 在文件的顶部，添加以下内容。

```csharp
private DefaultAzureCredential credential = new DefaultAzureCredential();
```

## <a name="issue-a-token-with-service-principals"></a>使用服务主体颁发令牌

现在，我们将添加使用已创建的凭据的代码来颁发 VoIP 访问令牌。 稍后我们将调用此代码。

```csharp
public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
{
    var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
    var identityResponse = client.CreateUser();
    var identity = identityResponse.Value;

    var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

    return tokenResponse;
}
```

## <a name="send-an-sms-with-service-principals"></a>使用服务主体发送短信

作为另一个使用服务主体的示例，我们将添加此代码，该代码使用相同的凭据来发送短信：

```csharp
public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
{
    SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
    SmsSendResult sendResult = smsClient.Send(
            from: from,
            to: to,
            message: message,
            new SmsSendOptions(enableDeliveryReport: true) // optional
        );

    return sendResult;
}
```

## <a name="write-the-main-method"></a>编写 Main 方法

你的 `Program.cs` 应该已经有了一个 Main 方法，让我们添加一些代码，这些代码将调用以前创建的代码来演示如何使用服务主体：

```csharp
static void Main(string[] args)
{
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    Uri endpoint = new("https://<RESOURCENAME>.communication.azure.com/");

    // We need an instance of the program class to use within this method.
    Program instance = new();

    Console.WriteLine("Retrieving new Access Token, using Service Principals");
    Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
    Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

    Console.WriteLine("Sending SMS using Service Principals");

    // You will need a phone number from your resource to send an SMS.
    SmsSendResult result = instance.SendSms(endpoint, "<Your ACS Phone Number>", "<The Phone Number you'd like to send the SMS to.>", "Hello from using Service Principals");
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

最终的 `Program.cs` 文件应如下所示：

```csharp
class Program
     {
          private DefaultAzureCredential credential = new DefaultAzureCredential();
          static void Main(string[] args)
          {
               // You can find your endpoint and access key from your resource in the Azure portal
               // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
               Uri endpoint = new("https://acstestingrifox.communication.azure.com/");

               // We need an instance of the program class to use within this method.
               Program instance = new();

               Console.WriteLine("Retrieving new Access Token, using Service Principals");
               Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
               Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

               Console.WriteLine("Sending SMS using Service Principals");

               // You will need a phone number from your resource to send an SMS.
               SmsSendResult result = instance.SendSms(endpoint, "<Your ACS Phone Number>", "<The Phone Number you'd like to send the SMS to.>", "Hello from Service Principals");
               Console.WriteLine($"Sms id: {result.MessageId}");
               Console.WriteLine($"Send Result Successful: {result.Successful}");
          }
          public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
          {
               var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
               var identityResponse = client.CreateUser();
               var identity = identityResponse.Value;

               var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

               return tokenResponse;
          }
          public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
          {
               SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
               SmsSendResult sendResult = smsClient.Send(
                    from: from,
                    to: to,
                    message: message,
                    new SmsSendOptions(enableDeliveryReport: true) // optional
               );

               return sendResult;
          }
    }
```

## <a name="run-the-program"></a>运行程序

现在应能够使用应用程序文件夹中的 `dotnet run` 来运行应用程序。 输出应如下所示：
```
Retrieving new Access Token, using Service Principals
Retrieved Access Token: ey....
Sending SMS using Service Principals
Sms id: Outgoing_..._noam
Send Result Successful: True
```
