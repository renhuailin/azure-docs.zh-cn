---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: a9d7f4f77d91abc88ea348e71a3d9c471b26a273
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622077"
---
模拟器支持单一固定的帐户和众所周知的用于共享密钥身份验证的身份验证密钥。 此帐户和密钥是允许用于模拟器的唯一共享密钥凭据。 它们分别是：

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> 模拟器支持的身份验证密钥仅用于测试客户端身份验证代码的功能。 它没有任何安全用途。 不能在模拟器中使用生产存储帐户和密钥。 不应将开发帐户用于生产数据。
>
> 模拟器仅支持通过 HTTP 进行连接。 但是，若要访问生产性 Azure 存储帐户中的资源，建议使用 HTTPS 协议。
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>使用快捷方式连接到模拟器帐户

从应用程序连接到模拟器的最简单方式是在应用程序的配置文件内配置一个引用快捷方式 `UseDevelopmentStorage=true` 的连接字符串。 该快捷方式等效于模拟器的完整连接字符串，该字符串指定每个 Azure 存储服务的帐户名、帐户密钥和模拟器终结点：

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
```

下面的 .NET 代码片段显示了如何通过采用连接字符串的方法使用该快捷方式。 例如，[BlobContainerClient(String, String)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) 构造函数采用连接字符串。

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

在调用代码片段中的代码之前，请确保模拟器正在运行。
