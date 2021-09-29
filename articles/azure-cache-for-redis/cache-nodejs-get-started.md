---
title: 快速入门：在 Node.js 中使用 Azure Cache for Redis
description: 本快速入门介绍如何将 Azure Redis 缓存与 Node.js 和 node_redis 配合使用。
author: yegu-ms
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2018
ms.author: yegu
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: a94b6382003d4cd9ee1e3d1bbb4932ee88a15577
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625728"
---
# <a name="quickstart-use-azure-cache-for-redis-in-nodejs"></a>快速入门：在 Node.js 中使用 Azure Cache for Redis

在本快速入门中，会将 Azure Redis 缓存合并到 Node.js 应用中，以便能够访问 Azure 中的任何应用程序都可以访问的安全专用缓存。

## <a name="skip-to-the-code-on-github"></a>跳到 GitHub 上的代码

如果要直接跳到代码，请参阅 GitHub 上的 [Node.js 快速入门](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/nodejs)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- [node_redis](https://github.com/mranney/node_redis)，可以使用命令 `npm install redis` 安装。

有关使用其他 Node.js 客户端的示例，请参阅 [Node.js Redis 客户端](https://redis.io/clients#nodejs)中所列的适用于 Node.js 客户端的各个文档。

## <a name="create-a-cache"></a>创建缓存

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](includes/redis-cache-access-keys.md)]

为 **主机名** 和 **主** 访问密钥添加环境变量。 通过代码使用这些变量，而不是直接在代码中包含敏感信息。

```powershell
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

```powershell
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="connect-to-the-cache"></a>连接到缓存

最新版本的 [node_redis](https://github.com/mranney/node_redis) 支持使用 TLS 连接到 Azure Cache for Redis。 以下示例演示如何使用 TLS 终结点 6380 连接到 Azure Cache for Redis。

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

不要为代码中的每个操作创建新连接， 而应尽可能重用连接。

## <a name="create-a-new-nodejs-app"></a>新建 Node.js 应用

创建名为 *redistest.js* 的新脚本文件。 使用命令 `npm install redis bluebird` 安装所需的包。

将下面的示例 JavaScript 添加到文件。 此代码演示如何使用缓存主机名和密钥环境变量连接到 Azure Redis 缓存实例。 此代码还在缓存中存储和检索字符串值。 还执行了 `PING` 和 `CLIENT LIST` 命令。 有关将 Redis 与 [node_redis](https://github.com/mranney/node_redis) 客户端一起使用的更多示例，请参阅 [https://redis.js.org/](https://redis.js.org/)。

```js
var redis = require("redis");
var bluebird = require("bluebird");

// Convert Redis client API to use promises, to make it usable with async/await syntax
bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the TLS port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demonstrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

使用 Node.js 运行该脚本。

```powershell
node redistest.js
```

在下面的示例中，可以看到 `Message` 键以前有一个缓存值，该值是使用 Azure 门户中的 Redis 控制台设置的。 应用更新了该缓存值。 应用还执行了 `PING` 和 `CLIENT LIST` 命令。

![已完成的 Redis 缓存应用](./media/cache-nodejs-get-started/redis-cache-app-complete.png)

## <a name="clean-up-resources"></a>清理资源

如果想要继续学习下一篇教程，可以保留本快速入门中创建的资源，以便重复使用。

如果已完成快速入门示例应用程序，可以删除本快速入门中创建的 Azure 资源，以免产生费用。

> [!IMPORTANT]
> 删除资源组的操作不可逆，资源组以及其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有资源组（其中包含要保留的资源）中为托管此示例而创建了相关资源，可在左侧逐个删除这些资源，而不是删除资源组。
>

登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。 

在“按名称筛选”文本框中，输入资源组的名称  。 本文的说明使用了名为 *TestResources* 的资源组。 在结果列表中的资源组上，选择“...”，然后选择“删除资源组”   。

![删除 Azure 资源组](./media/cache-nodejs-get-started/redis-cache-delete-resource-group.png)

系统会要求确认是否删除该资源组。 重新键入资源组的名称进行确认，然后选择“删除”  。

片刻之后，将会删除该资源组及其包含的所有资源。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何通过 Node.js 应用程序使用 Azure Redis 缓存。 请继续学习下一个快速入门，将 Azure Redis 缓存与 ASP.NET Web 应用配合使用。

> [!div class="nextstepaction"]
> [创建使用 Azure Redis 缓存的 ASP.NET Web 应用。](./cache-web-app-howto.md)
