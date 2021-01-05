---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: 85cfe3b062d7d9ef3a7bdcf29ef7d2125f8f3ae4
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97812805"
---
模拟器支持单一固定帐户和众所周知的身份验证密钥进行共享密钥身份验证。 此帐户和密钥是允许与模拟器一起使用的唯一共享密钥凭据。 它们分别是：

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> 模拟器支持的身份验证密钥仅用于测试客户端身份验证代码的功能。 它没有任何安全用途。 不能将生产存储帐户和密钥与模拟器一起使用。 不应将开发帐户用于生产数据。
>
> 模拟器仅支持通过 HTTP 进行连接。 但是，若要访问生产性 Azure 存储帐户中的资源，建议使用 HTTPS 协议。
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>使用快捷方式连接到模拟器帐户

从应用程序连接到模拟器的最简单方法是在应用程序的配置文件中配置引用快捷方式的连接字符串 `UseDevelopmentStorage=true` 。 此快捷方式等效于模拟器的完整连接字符串，该字符串指定每个 Azure 存储服务的帐户名称、帐户密钥和模拟器终结点：

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

下面的 .NET 代码段演示如何使用采用连接字符串的方法中的快捷方式。 例如， [BlobContainerClient (string，string) ](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) 构造函数采用一个连接字符串。

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

在调用代码段中的代码之前，请确保模拟器正在运行。
