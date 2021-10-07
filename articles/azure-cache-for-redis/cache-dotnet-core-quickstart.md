---
title: 快速入门：在 .NET Core 中使用 Azure Cache for Redis
description: 在本快速入门中，了解如何在 .NET Core 应用中访问 Azure Redis 缓存
author: curib
ms.author: cauribeg
ms.service: cache
ms.devlang: dotnet
ms.custom: devx-track-csharp, mvc
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 3ca0d730d1c99e985a0b7b96c01fdd1dcef95a18
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538604"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-core"></a>快速入门：在 .NET Core 中使用 Azure Cache for Redis

在本快速入门中，会将 Azure Redis 缓存合并到 .NET Core 应用中，以便能够访问 Azure 中的任何应用程序都可以访问的安全专用缓存。 你专门在 .NET Core 控制台应用中将 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 客户端与 C# 代码配合使用。

## <a name="skip-to-the-code-on-github"></a>跳到 GitHub 上的代码

如果要直接跳到代码，请参阅 GitHub 上的 [.NET Core 快速入门](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/dotnet-core)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-a-cache"></a>创建缓存
[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]


[!INCLUDE [redis-cache-access-keys](includes/redis-cache-access-keys.md)]

记下 **主机名** 和 **主** 访问密钥。 稍后将使用这些值来构造 *CacheConnection* 机密。



## <a name="create-a-console-app"></a>创建控制台应用

打开一个新的命令窗口并执行以下命令来创建新的 .NET Core 控制台应用：

```
dotnet new console -o Redistest
```

在命令窗口中，切换到新的 *Redistest* 项目目录。



## <a name="add-secret-manager-to-the-project"></a>向项目添加机密管理器

在本部分中，将向项目添加[机密管理器工具](/aspnet/core/security/app-secrets)。 机密管理器工具存储敏感数据，以便用于项目树之外的开发工作。 此方法有助于防止意外共享源代码中的应用密码。

打开 *Redistest.csproj* 文件。 添加 `DotNetCliToolReference` 元素以包含 Microsoft.Extensions.SecretManager.Tools  。 同时添加 `UserSecretsId` 元素（如下所示），并保存文件。

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>Redistest</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
</Project>
```

执行以下命令将 *Microsoft.Extensions.Configuration.UserSecrets* 包添加到项目：

```
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

执行以下命令来还原包：

```
dotnet restore
```

在命令窗口中，在替换你的缓存名称和主访问密钥的占位符（包括尖括号）后，执行以下命令来存储名为 *CacheConnection* 的新机密：

```
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<primary-access-key>"
```

将以下 `using` 语句添加到 *Program.cs*：

```csharp
using Microsoft.Extensions.Configuration;
```

将以下成员添加到 *Program.cs* 中的 `Program` 类。 此代码初始化配置以访问 Azure Redis 缓存连接字符串的用户机密。

```csharp
private static IConfigurationRoot Configuration { get; set; }
const string SecretName = "CacheConnection";

private static void InitializeConfiguration()
{
    var builder = new ConfigurationBuilder()
        .AddUserSecrets<Program>();

    Configuration = builder.Build();
}
```

## <a name="configure-the-cache-client"></a>配置缓存客户端

在本部分，请配置控制台应用程序，以便将 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 客户端用于 .NET。

在命令窗口中，在 *Redistest* 项目目录中执行以下命令：

```
dotnet add package StackExchange.Redis
```

完成安装后，*StackExchange.Redis* 缓存客户端可供与项目一起使用。


## <a name="connect-to-the-cache"></a>连接到缓存

将以下 `using` 语句添加到 *Program.cs*：

```csharp
using StackExchange.Redis;
```

与 Azure Redis 缓存的连接由 `ConnectionMultiplexer` 类管理。 应在整个客户端应用程序中共享和重复使用此类。 不要为每个操作创建新连接。 

在 *Program.cs* 中，将以下成员添加到控制台应用程序的 `Program` 类：

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}

private static Lazy<ConnectionMultiplexer> CreateConnection()
{
    return new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = Configuration[SecretName];
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
}
```

这种在应用程序中共享 `ConnectionMultiplexer` 实例的方法使用一个返回已连接实例的静态属性。 此代码提供了一种线程安全方法，它仅初始化单个已连接的 `ConnectionMultiplexer` 实例。 `abortConnect` 设置为 false，这意味着即使未建立与 Azure Redis 缓存的连接，调用也会成功。 `ConnectionMultiplexer` 的一个关键功能是，一旦解决网络问题和其他原因，它会自动还原缓存连接。

*CacheConnection* 机密的值是使用机密管理器配置提供程序访问的并且用作密码参数。

## <a name="handle-redisconnectionexception-and-socketexception-by-reconnecting"></a>通过重新连接来处理 RedisConnectionException 和 SocketException

在 `ConnectionMultiplexer` 上调用方法时，建议最佳做法是通过关闭并重新建立连接来尝试自动解析 `RedisConnectionException` 和 `SocketException` 异常。

将以下 `using` 语句添加到 *Program.cs*：

```csharp
using System.Net.Sockets;
using System.Threading;
```

在 *Program.cs* 中，将以下成员添加到 `Program` 类。

```csharp
private static long lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
private static DateTimeOffset firstErrorTime = DateTimeOffset.MinValue;
private static DateTimeOffset previousErrorTime = DateTimeOffset.MinValue;

private static readonly object reconnectLock = new object();

// In general, let StackExchange.Redis handle most reconnects,
// so limit the frequency of how often ForceReconnect() will
// actually reconnect.
public static TimeSpan ReconnectMinFrequency => TimeSpan.FromSeconds(60);

// If errors continue for longer than the below threshold, then the
// multiplexer seems to not be reconnecting, so ForceReconnect() will
// re-create the multiplexer.
public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);

public static int RetryMaxAttempts => 5;

private static void CloseConnection(Lazy<ConnectionMultiplexer> oldConnection)
{
    if (oldConnection == null)
        return;

    try
    {
        oldConnection.Value.Close();
    }
    catch (Exception)
    {
        // Example error condition: if accessing oldConnection.Value causes a connection attempt and that fails.
    }
}

/// <summary>
/// Force a new ConnectionMultiplexer to be created.
/// NOTES:
///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnect().
///     2. Don't call ForceReconnect for Timeouts, just for RedisConnectionExceptions or SocketExceptions.
///     3. Call this method every time you see a connection exception. The code will:
///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
///         b. not reconnect more frequently than configured in "ReconnectMinFrequency"
/// </summary>
public static void ForceReconnect()
{
    var utcNow = DateTimeOffset.UtcNow;
    long previousTicks = Interlocked.Read(ref lastReconnectTicks);
    var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
    TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;

    // If multiple threads call ForceReconnect at the same time, we only want to honor one of them.
    if (elapsedSinceLastReconnect < ReconnectMinFrequency)
        return;

    lock (reconnectLock)
    {
        utcNow = DateTimeOffset.UtcNow;
        elapsedSinceLastReconnect = utcNow - previousReconnectTime;

        if (firstErrorTime == DateTimeOffset.MinValue)
        {
            // We haven't seen an error since last reconnect, so set initial values.
            firstErrorTime = utcNow;
            previousErrorTime = utcNow;
            return;
        }

        if (elapsedSinceLastReconnect < ReconnectMinFrequency)
            return; // Some other thread made it through the check and the lock, so nothing to do.

        TimeSpan elapsedSinceFirstError = utcNow - firstErrorTime;
        TimeSpan elapsedSinceMostRecentError = utcNow - previousErrorTime;

        bool shouldReconnect =
            elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
            && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).

        // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
        previousErrorTime = utcNow;

        if (!shouldReconnect)
            return;

        firstErrorTime = DateTimeOffset.MinValue;
        previousErrorTime = DateTimeOffset.MinValue;

        Lazy<ConnectionMultiplexer> oldConnection = lazyConnection;
        CloseConnection(oldConnection);
        lazyConnection = CreateConnection();
        Interlocked.Exchange(ref lastReconnectTicks, utcNow.UtcTicks);
    }
}

// In real applications, consider using a framework such as
// Polly to make it easier to customize the retry approach.
private static T BasicRetry<T>(Func<T> func)
{
    int reconnectRetry = 0;
    int disposedRetry = 0;

    while (true)
    {
        try
        {
            return func();
        }
        catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
        {
            reconnectRetry++;
            if (reconnectRetry > RetryMaxAttempts)
                throw;
            ForceReconnect();
        }
        catch (ObjectDisposedException)
        {
            disposedRetry++;
            if (disposedRetry > RetryMaxAttempts)
                throw;
        }
    }
}

public static IDatabase GetDatabase()
{
    return BasicRetry(() => Connection.GetDatabase());
}

public static System.Net.EndPoint[] GetEndPoints()
{
    return BasicRetry(() => Connection.GetEndPoints());
}

public static IServer GetServer(string host, int port)
{
    return BasicRetry(() => Connection.GetServer(host, port));
}
```

## <a name="executing-cache-commands"></a>执行缓存命令

在 *Program.cs* 中，为控制台应用程序的 `Program` 类的 `Main` 过程添加以下代码：

```csharp
static void Main(string[] args)
{
    InitializeConfiguration();

    IDatabase cache = GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET Core console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET Core console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires allowAdmin=true in the connection string
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
    IServer server = GetServer(endpoint.Host, endpoint.Port);
    ClientInfo[] clients = server.ClientList();

    Console.WriteLine("Cache response :");
    foreach (ClientInfo client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    CloseConnection(lazyConnection);
}
```

保存 Program.cs。

Azure Redis 缓存具有可配置的数据库数量（默认为 16 个），因此可以通过逻辑方式隔离 Azure Redis 缓存中的数据。 该代码连接到默认数据库 DB 0。 有关详细信息，请参阅[什么是 Redis 数据库？](cache-development-faq.yml#what-are-redis-databases-)和[默认 Redis 服务器配置](cache-configure.md#default-redis-server-configuration)。

可以使用 `StringSet` 和 `StringGet` 方法来存储和检索缓存项。

Redis 将大多数数据存储为 Redis 字符串，但这些字符串可能包含许多类型的数据，包括序列化的二进制数据，可在缓存中存储 .NET 对象时使用。

在命令窗口中执行以下命令来生成应用：

```
dotnet build
```

然后，使用以下命令来运行应用：

```
dotnet run
```

在下面的示例中，可以看到 `Message` 键以前有一个缓存值，该值是使用 Azure 门户中的 Redis 控制台设置的。 应用更新了该缓存值。 应用还执行了 `PING` 和 `CLIENT LIST` 命令。

![控制台应用的一部分](./media/cache-dotnet-core-quickstart/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>处理缓存中的 .NET 对象

Azure Redis 缓存可以缓存 .NET 对象以及基元数据类型，但在缓存 .NET 对象之前，必须将其序列化。 此 .NET 对象序列化是应用程序开发人员的责任，同时赋与开发人员选择序列化程序的弹性。

将对象序列化的一种简单方式是使用 [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) 中的 `JsonConvert` 序列化方法，并与 JSON 相互序列化。 在本部分中，将向缓存中添加一个 .NET 对象。

执行以下命令将 *Newtonsoft.json* 包添加到应用：

```
dotnet add package Newtonsoft.json
```

将以下 `using` 语句添加到 *Program.cs* 的顶部：

```csharp
using Newtonsoft.Json;
```

将以下 `Employee` 类定义添加到 *Program.cs*：

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string employeeId, string name, int age)
    {
        Id = employeeId;
        Name = name;
        Age = age;
    }
}
```

在 *Program.cs* 中的 `Main()` 过程的底部，在对 `CloseConnection()` 的调用之前，添加以下代码行来缓存和检索已序列化的 .NET 对象：

```csharp
    // Store .NET object to cache
    Employee e007 = new Employee("007", "Davide Columbo", 100);
    Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

    // Retrieve .NET object from cache
    Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
    Console.WriteLine("Deserialized Employee .NET object :\n");
    Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
    Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
    Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

保存 *Program.cs* 并使用以下命令重新生成应用：

```
dotnet build
```

使用以下命令运行应用来测试 .NET 对象的序列化：

```
dotnet run
```

![已完成的控制台应用](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>清理资源

如果想要继续学习下一篇教程，可以保留本快速入门中创建的资源，以便重复使用。

如果已完成快速入门示例应用程序，可以删除本快速入门中创建的 Azure 资源，以免产生费用。 

> [!IMPORTANT]
> 删除资源组的操作不可逆，资源组以及其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有资源组（其中包含要保留的资源）中为托管此示例而创建了相关资源，可在左侧逐个删除这些资源，而不是删除资源组。
>

登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。 

在“按名称筛选...”文本框中键入资源组的名称。 本文的说明使用了名为 *TestResources* 的资源组。 在结果列表中的资源组上，选择“...”，然后选择“删除资源组”   。

![删除](./media/cache-dotnet-core-quickstart/cache-delete-resource-group.png)

系统会要求确认是否删除资源组。 键入资源组的名称进行确认，然后选择“删除”。

片刻之后，将会删除该资源组及其包含的所有资源。



<a name="next-steps"></a>

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何通过 .NET Core 应用程序使用 Azure Redis 缓存。 请继续学习下一个快速入门，将 Azure Redis 缓存与 ASP.NET Web 应用配合使用。

> [!div class="nextstepaction"]
> [创建使用 Azure Redis 缓存的 ASP.NET Web 应用。](./cache-web-app-howto.md)

希望优化并节省云支出？

> [!div class="nextstepaction"]
> [使用成本管理开始分析成本](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)