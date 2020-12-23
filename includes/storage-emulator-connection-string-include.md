---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 37fba0101365e425110c2943264c8c0e8c511329
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582458"
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

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>使用快捷方式连接到模拟器帐户
从应用程序连接到模拟器的最简单方法是在应用程序的配置文件中配置引用快捷方式的连接字符串 `UseDevelopmentStorage=true` 。 下面是 *app.config* 文件中的模拟器的连接字符串示例： 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

等效于完全指定帐户名称、帐户密钥以及要在连接字符串中使用的每个模拟器服务的终结点。 这是必须的，这样连接字符串将引用与生产存储帐户中的终结点不同的模拟器终结点。 例如，连接字符串的值将如下所示：

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```
