---
title: 使用 .NET SDK 创建 Purview 帐户
description: 使用 .NET SDK 创建 Azure Purview 帐户。
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: 04ed5cef351c81355a2390dd0b983c162f2b9532
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387132"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>快速入门：使用 .NET SDK 创建 Purview 帐户

本快速入门介绍了如何使用 .NET SDK 创建一个 Azure Purview 帐户。 

> [!IMPORTANT]
> Azure Purview 当前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="prerequisites"></a>必备条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 你拥有 [Azure Active Directory 租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* 你的帐户必须有权在订阅中创建资源

* 如果 Azure Policy 阻止所有应用程序创建存储帐户和 EventHub 命名空间，则需要使用标记设置策略例外，可以在创建 Purview 帐户的过程中输入此信息  。 主要原因是，对于创建的每个 Purview 帐户，它需要创建一个托管资源组，并在此资源组中创建存储帐户和 EventHub 命名空间。 有关详细信息，请参阅[创建目录门户](create-catalog-portal.md)

### <a name="visual-studio"></a>Visual Studio

本文中的演练使用 Visual Studio 2019。 Visual Studio 2013、2015 或 2017 的过程略有不同。

### <a name="azure-net-sdk"></a>Azure .NET SDK

在计算机上下载并安装 [Azure .NET SDK](https://azure.microsoft.com/downloads/)。

## <a name="create-an-application-in-azure-active-directory"></a>在 Azure Active Directory 中创建应用程序

从“如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体”中的各部分开始，按照说明执行以下任务：

1. 在[创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)中，创建一个应用程序来表示正在本教程中创建的 .NET 应用程序。 对于登录 URL，可以提供虚拟 URL，如本文中所示 (`https://contoso.org/exampleapp`)。
2. 在[获取用于登录的值](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)中，获取应用程序 ID 和租户 ID，并记下这些值，稍后要在本教程中使用它们 。 
3. 在[证书与机密](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)中，获取身份验证密钥，并记此值，稍后要在本教程中使用它。
4. 在[将应用程序分配给角色](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)中，在订阅级别将应用程序分配到“参与者”角色，让该应用程序可以在订阅中创建数据工厂。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

接下来，在 Visual Studio 中创建 C# 控制台应用程序：

1. 启动 **Visual Studio**。
2. 在“开始”窗口中，选择“创建新项目” > “控制台应用(.NET Framework)” 。 需要 .NET 4.5.2 或更高版本。
3. 在“项目名称”中，输入 ADFv2QuickStart 。
4. 选择“创建”来创建项目。

## <a name="install-nuget-packages"></a>安装 NuGet 包

1. 选择“工具” > “NuGet 包管理器” > “包管理器控制台”。
2. 在“包管理器控制台”窗格中，运行以下命令来安装包。 有关详细信息，请参阅 [Microsoft.Azure.Management.Purview NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/)。

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>创建 Purview 客户端

1. 打开 **Program.cs**，包括以下语句来添加对命名空间的引用。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. 将以下代码添加到 **Main** 方法以设置变量。 将占位符替换为自己的值。 若要查看目前提供 Purview 的 Azure 区域列表，请在 Azure Purview 搜索，然后在以下页面上选择感兴趣的区域：[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/)。

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. 将以下代码添加到“Main”方法，此方法可创建 PurviewManagementClient 类的实例。 你可以使用此对象创建 Purview 帐户。

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>创建 Purview 帐户

将以下代码添加到“Main”方法，此方法可创建 purview 帐户。 

```csharp
    // Create a purview Account
    Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
    Account account = new Account()
    {
    Location = region,
    Identity = new Identity(type: "SystemAssigned"),
    Sku = new AccountSku(name: "Standard", capacity: 4)
    };            
    try
    {
      client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
      Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
    }
    catch (ErrorResponseModelException purviewException)
    {
      Console.WriteLine(purviewException.StackTrace);
    }
    Console.WriteLine(
      SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
    while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
           "PendingCreation")
    {
      System.Threading.Thread.Sleep(1000);
    }
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
```

## <a name="run-the-code"></a>运行代码

生成并启动应用程序，然后验证执行。

控制台将打印创建 Purview 帐户的进度。

### <a name="sample-output"></a>示例输出

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>验证输出

转到 [Azure 门户](https://portal.azure.com)的“Purview 帐户”页，并验证使用上述代码创建的帐户。 

## <a name="delete-purview-account"></a>删除 Purview 帐户

若要以编程方式删除 Purview 帐户，请将以下代码行添加到程序： 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>检查 Purview 帐户名称是否可用

若要检查 Purview 帐户的可用性，请使用以下代码： 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

如果名称可用，则上述代码将打印“True”；如果该名称不可用，则为“False”。


## <a name="next-steps"></a>后续步骤

本教程中的代码创建 Purview 帐户、删除 Purview 帐户，并检查 Purview 帐户的名称可用性。 你现在可以下载 .NET SDK，并了解可为 Purview 帐户执行的其他资源提供程序操作。

请继续阅读下一篇文章，了解如何允许用户访问 Azure Purview 帐户。 

> [!div class="nextstepaction"]
> [将用户添加到 Azure Purview 帐户](catalog-permissions.md)
