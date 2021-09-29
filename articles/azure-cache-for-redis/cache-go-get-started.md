---
title: 将 Azure Cache for Redis 与 Go 配合使用
description: 本快速入门介绍如何创建使用 Azure Cache for Redis 的 Go 应用。
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: ab7bd55dd7b66d613f99b4d54a8fdf4445542545
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591817"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>快速入门：将 Azure Cache for Redis 与 Go 配合使用

在本文中，你将了解如何在 Go 应用中构建 REST API，该应用将存储和检索由 [Azure Cache for Redis](./cache-overview.md) 中的 [HASH](https://redis.io/topics/data-types-intro#redis-hashes) 数据结构支持的用户信息。

## <a name="skip-to-the-code-on-github"></a>跳到 GitHub 上的代码

如果要直接跳到代码，请参阅 GitHub 上的 [Go 快速入门](https://github.com/Azure-Samples/azure-redis-cache-go-quickstart/)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- [Go](https://golang.org/doc/install)（最好为版本 1.13 或更高版本）
- [Git](https://git-scm.com/downloads)
- HTTP 客户端，如 [curl](https://curl.se/)

## <a name="create-an-azure-cache-for-redis-instance"></a>创建用于 Redis 的 Azure 缓存实例

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>查看代码（可选）

如果有意了解代码的工作原理，可以查看以下代码片段。 否则，可以直接跳到[运行应用程序](#run-the-application)。

开放源代码 [go-redis](https://github.com/go-redis/redis) 库用于与 Azure Cache for Redis 交互。

`main` 函数首先读取 Azure Cache for Redis 实例的主机名和密码（访问密钥）。

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

然后，我们会建立与 Azure Cache for Redis 的连接。 我们使用 [tls.Config](https://golang.org/pkg/crypto/tls/#Config)--Azure Cache for Redis 只接受以 [TLS 1.2 作为所需的最低版本](cache-remove-tls-10-11.md)的安全连接。

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

如果连接成功，[HTTP 处理程序](https://golang.org/pkg/net/http/#HandleFunc)将配置为处理 `POST` 和 `GET` 操作，HTTP 服务器已启动。

> [!NOTE]
> [gorilla mux 库](https://github.com/gorilla/mux)用于路由（尽管这不是必须的，而且我们可以通过使用这个示例应用程序的标准库来避免这种情况）。
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

`userHandler` 结构封装了由 `createUser`、`getUser` 方法使用的 [redis.Client](https://pkg.go.dev/github.com/go-redis/redis/v8#Client) - 为了简洁起见，没有包含这些方法的代码。

- `createUser`：接受 JSON 有效负载（包含用户信息）并将其保存为 Azure Cache for Redis 中的 `HASH`。
- `getUser`：从 `HASH` 提取用户信息，如果找不到，则返回 HTTP `404` 响应。

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
```

## <a name="clone-the-sample-application"></a>克隆示例应用程序

首先从 GitHub 克隆应用程序开始。

1. 打开命令提示符，然后创建一个新的名为 `git-samples` 的文件夹。

    ```bash
    md "C:\git-samples"
    ```

1. 打开 git 终端窗口（例如 git bash）。 使用 `cd` 命令转到新文件夹，你将在其中克隆示例应用。

    ```bash
    cd "C:\git-samples"
    ```

1. 运行下列命令，克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>运行应用程序

应用程序接受环境变量形式的连接和凭据。

1. 在 [Azure 门户](https://portal.azure.com/)中为 Azure Cache for Redis 实例提取“主机名”和“访问密钥”（可通过访问密钥获得） 

1. 将它们设置为相应的环境变量：

    ```console
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. 在“终端”窗口中，切换到正确的文件夹。 例如：

    ```console
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. 在终端中，运行以下命令以启动应用程序。

    ```console
    go run main.go
    ```

HTTP 服务器将在端口 `8080` 上启动。

## <a name="test-the-application"></a>测试应用程序

1. 创建一些用户条目。 下面的示例使用 curl：

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. 使用 `id` 提取现有用户：

    ```bash
    curl -i localhost:8080/users/1
    ```

    应获得 JSON 响应，如下所示：

    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. 如果尝试提取一个不存在的用户，将收到 HTTP `404`。 例如：

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
    ```

## <a name="clean-up-resources"></a>清理资源

如果已完成在此快速入门中创建的 Azure 资源组和资源，则可以删除它们以避免产生费用。

> [!IMPORTANT]
> 删除资源组的操作不可逆，资源组以及其中的所有资源将被永久删除。 如果在要保留的现有资源组中创建了 Azure Redis 缓存实例，可从缓存“概述”页选择“删除”以便仅删除缓存 。

删除资源组及其 Azure Redis 缓存实例：

1. 从 [Azure 门户](https://portal.azure.com)中，搜索并选择“资源组”。
1. 在“按名称筛选”文本框中，输入包含缓存实例的资源组的名称，然后从搜索结果中选择它。
1. 在资源组页上，选择“删除资源组”。
1. 键入资源组名称，然后选择“删除”。

   ![删除 Azure Redis 缓存的资源组](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何通过 GO 开始使用 Azure Cache for Redis。 你已配置并运行了一个简单的基于 REST API 的应用程序，以创建和获取由 Redis `HASH` 数据结构支持的用户信息。

> [!div class="nextstepaction"]
> [创建简单的使用 Azure Redis 缓存的 ASP.NET Web 应用。](./cache-web-app-howto.md)
