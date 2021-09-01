---
title: 自动重新加载机密和证书
titleSuffix: Azure App Configuration
description: 了解如何设置应用程序，以自动从 Key Vault 中重新加载机密和证书。
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 05/25/2021
ms.author: avgupta
ms.openlocfilehash: f133c3184186ef46f7d4579a64d22733b773df7f
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111414939"
---
# <a name="reload-secrets-and-certificates-from-key-vault-automatically"></a>自动从 Key Vault 重新加载机密和证书

应用程序配置和 Key Vault 是互补性的服务，大多数应用程序中会同时使用两者。 应用程序配置可在你的应用程序配置存储中创建密钥（引用存储在 Key Vault 中的机密或证书），以帮助你结合使用这两个服务。 由于 Key Vault 将证书的公钥和私钥对存储为机密，因此你的应用程序可以从 Key Vault 中检索任何证书作为机密。

应定期轮换[机密](../key-vault/secrets/tutorial-rotation.md)和[证书](../key-vault/certificates/tutorial-rotate-certificates.md)，这是一种良好的安全做法。 在 Key Vault 中轮换机密和证书后，需要让应用程序选取最新的机密和证书值。 在不重启应用程序的情况下，有两种方法可以实现此目的：
- 更新 sentinel 键值以触发整个配置的刷新，从而重新加载所有 Key Vault 机密和证书。 有关详细信息，请参阅如何[在 ASP.NET Core 应用中使用动态配置](./enable-dynamic-configuration-aspnet-core.md)。
- 定期从 Key Vault 重新加载部分或全部机密和证书。

第一个选项中，每当在 Key Vault 中轮换机密和证书时，必须更新应用程序配置中的 sentinel 键值。 若要在应用程序中强制立即重新加载机密和证书，此方法非常有效。 但是，当机密和证书在 Key Vault 中自动轮换时，如果未及时更新 sentinel 键值，则应用程序可能会遇到错误。 第二个选项允许你完全自动执行此过程。 可以将应用程序配置为，在自轮换时起可接受的延迟内从 Key Vault 重新加载机密和证书。 本教程将逐步介绍第二个选项。


## <a name="prerequisites"></a>先决条件

- 本教程介绍了如何设置应用程序，以从 Key Vault 自动重新加载机密和证书。 它基于有关在代码中实现 Key Vault 引用的教程。 在继续之前，请首先完成[教程：在 ASP.NET Core 应用中使用 Key Vault 引用](./use-key-vault-references-dotnet-core.md)。

- [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) 包 v4.4.0 或更高版本。


## <a name="add-an-auto-rotating-certificate-to-key-vault"></a>向 Key Vault 中添加自动轮换证书

 按照[教程：在 Key Vault 中配置证书自动轮换](../key-vault/certificates/tutorial-rotate-certificates.md)，将名为 ExampleCertificate 的自动轮换证书添加到之前教程中创建的 Key Vault。


## <a name="add-a-reference-to-the-key-vault-certificate-in-app-configuration"></a>在应用程序配置中添加 Key Vault 证书的引用

1. 在 Azure 门户中，选择“所有资源”，然后选择之前教程中创建的应用程序配置存储实例。

1. 选择“配置资源管理器”。 

1. 选择“+ 创建” > “Key Vault 引用”，然后指定以下值：  
    - 密钥：选择 TestApp:Settings:KeyVaultCertificate。 
    - **标签**：将此值保留空白。
    - 订阅、资源组和 Key Vault：输入与之前教程中创建的 Key Vault 相对应的值。  
    - 机密：选择之前部分中创建的名为 ExampleCertificate 的机密。 
    - 机密版本：最新版本。 

> [!Note]
> 如果引用特定版本，则从 Key Vault 重新加载机密或证书将始终返回相同的值。


## <a name="update-code-to-reload-key-vault-secrets-and-certificates"></a>更新代码以重新加载 Key Vault 机密和证书

在 Program.cs 文件中，更新 `AddAzureAppConfiguration` 方法，以使用 `SetSecretRefreshInterval` 方法为 Key Vault 证书设置刷新间隔。 进行此更改后，应用程序将每隔 12 小时重新加载一次 ExampleCertificate 的公钥-私钥对。

```csharp
config.AddAzureAppConfiguration(options =>
{
    options.Connect(settings["ConnectionStrings:AppConfig"])
            .ConfigureKeyVault(kv =>
            {
                kv.SetCredential(new DefaultAzureCredential());
                kv.SetSecretRefreshInterval("TestApp:Settings:KeyVaultCertificate", TimeSpan.FromHours(12));
            });
});
```

`SetSecretRefreshInterval` 方法中的第一个参数是应用程序配置 Key Vault 引用的密钥。 该参数可选。 如果省略密钥参数，刷新间隔将应用于没有单独刷新间隔的所有机密和证书。

刷新间隔定义机密和证书从 Key Vault 重新加载的频率，而不考虑在 Key Vault 或应用程序配置中对它们值的任何更改。 如果要在应用程序配置中机密和证书的值发生更改时重新加载机密和证书，可以使用 `ConfigureRefresh` 方法监视它们。 有关详细信息，请参阅如何[在 ASP.NET Core 应用中使用动态配置](./enable-dynamic-configuration-aspnet-core.md)。

根据机密和证书在 Key Vault 中更新后可接受的延迟选择刷新间隔。 还必须考虑 [Key Vault 服务限制](../key-vault/general/service-limits.md)以避免受到限制。


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]


## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何设置应用程序，以自动从 Key Vault 重新加载机密和证书。 若要了解如何使用托管标识来简化对应用程序配置和 Key Vault 的访问，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [托管标识集成](./howto-integrate-azure-managed-service-identity.md)
