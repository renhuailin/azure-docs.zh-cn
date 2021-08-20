---
ms.openlocfilehash: 6e470aaaad9879116460180b15f45b1419f51418
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "114200942"
---
> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers) 上查找此快速入门的最终代码

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 适用于你的操作系统的最新版本 [.NET Core 客户端库](https://dotnet.microsoft.com/download/dotnet-core)。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../../create-communication-resource.md)。

### <a name="prerequisite-check"></a>先决条件检查

- 在终端或命令窗口中，运行 `dotnet` 命令来查看是否安装了 .NET 客户端。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `PhoneNumbersQuickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：**Program.cs**。

```console
dotnet new console -o PhoneNumbersQuickstart
```

将目录更改为新创建的应用文件夹，并使用 `dotnet build` 命令编译应用程序。

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>安装包

如果仍在应用程序目录中，使用 `dotnet add package` 命令安装适用于 .NET 包的 Azure 通信电话号码客户端库。

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0
```

将 `using` 指令添加到 Program.cs 顶部以包括命名空间。

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
```

将 `Main` 函数签名更新为 async。

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>验证客户端

电话号码客户端可以使用从 [Azure 门户][azure_portal] 中的 Azure 通信服务资源获取的连接字符串进行身份验证。

```csharp
// Get a connection string to our Azure Communication Services resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

电话号码客户端还可以使用 Azure Active Directory 身份验证进行身份验证。 在此选项中，`AZURE_CLIENT_SECRET`、`AZURE_CLIENT_ID` 和 `AZURE_TENANT_ID` 环境变量需要设置为可执行身份验证。

```csharp
// Get an endpoint to our Azure Communication Services resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>管理电话号码

### <a name="search-for-available-phone-numbers"></a>搜索可用的电话号码

为了购买电话号码，必须首先搜索可用的电话号码。 若要搜索电话号码，请提供区号、分配类型、[电话号码功能](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)、[电话号码类型](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)和数量。 请注意，对于免费电话号码类型，可以选择是否提供区号。

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>购买电话号码

电话号码的搜索结果是 `PhoneNumberSearchResult` 条。 此结果包含一个 `SearchId`，可将其传递给采购编号 API 以获取搜索中的数量。 请注意，调用购买电话号码 API 将导致系统向你的 Azure 帐户收费。

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionResponseAsync();
```

### <a name="get-phone-numbers"></a>获取电话号码

购买号码后，你可以从客户端进行检索。
```csharp
var getPhoneNumberResponse = await client.GetPurchasedPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

你还可以检索所有购买的电话号码。
``` csharp
var purchasedPhoneNumbers = client.GetPurchasedPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>更新电话号码功能

你可以使用购买的编号更新功能。

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>发布电话号码

你可以发布购买的电话号码。

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionResponseAsync();
````

## <a name="run-the-code"></a>运行代码

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```console
dotnet run
```

## <a name="sample-code"></a>代码示例

可以从 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers) 下载示例应用
