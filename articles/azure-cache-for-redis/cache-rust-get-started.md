---
title: 将 Azure Cache for Redis 与 Rust 配合使用
description: 本快速入门介绍如何使用 Rust 与 Azure Cache for Redis 交互。
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 23e1bdc895da7ba9941901832d7fc89cd9c6196a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592615"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>快速入门：将 Azure Cache for Redis 与 Rust 配合使用

本文将介绍如何使用 [Rust 编程语言](https://www.rust-lang.org/)来与 [Azure Cache for Redis](./cache-overview.md) 交互。 你还将了解常用的 Redis 数据结构： 

* [字符串](https://redis.io/topics/data-types-intro#redis-strings) 
* [哈希](https://redis.io/topics/data-types-intro#redis-hashes) 
* [列表](https://redis.io/topics/data-types-intro#redis-lists) 

在本示例中，你将使用 Redis 的 [redis-rs](https://github.com/mitsuhiko/redis-rs) 库。 此客户端公开高级别和低级别 API，你可查看这两种样式的实际应用。

## <a name="skip-to-the-code-on-github"></a>跳到 GitHub 上的代码

如果要直接跳到代码，请参阅 GitHub 上的 [Rust 快速入门](https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart/)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- [Rust](https://www.rust-lang.org/tools/install)（1.39 或更高版本）
- [Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>创建用于 Redis 的 Azure 缓存实例
[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>查看代码（可选）

如果有意了解代码的工作原理，可以查看以下代码片段。 否则，可以直接跳到[运行应用程序](#run-the-application)。

`connect` 函数用于与 Azure Cache for Redis 建立连接。 它需要分别通过环境变量 `REDIS_HOSTNAME` 和 `REDIS_PASSWORD` 传入主机名和密码（访问密钥）。 连接 URL 的格式为 `rediss://<username>:<password>@<hostname>` - Azure Cache for Redis 仅接受以 [TLS 1.2 作为所需的最低版本](cache-remove-tls-10-11.md)的安全连接。

对 [redis::Client::open](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open) 进行调用会执行基本验证，而 [get_connection()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection) 会实际启动连接。 如果连接因任何原因失败，程序将停止。 例如，其中一个原因可能是密码不正确。

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

`basics` 函数涵盖 [SET](https://redis.io/commands/set)、[GET](https://redis.io/commands/get) 和 [INCR](https://redis.io/commands/incr) 命令。 

低级别 API 用于 `SET` 和 `GET`，以设置和检索名为 `foo` 的键的值。 

`INCRBY` 命令是使用高级别 API 执行的，即 [incr](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr) 以 `2` 为增量递增键（名为 `counter`）的值，然后调用 [get](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get) 来检索该值。

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

以下代码片段演示 Redis `HASH` 数据结构的功能。 使用低级别 API 调用 [HSET](https://redis.io/commands/hset)，以存储有关 Redis 驱动程序（客户端）的信息（`name`、`version`、`repo`）。 例如，Rust 驱动程序（在此示例代码中使用的驱动程序！）的详细信息以 [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) 的形式捕获，然后传递到低级别 API。 然后使用 [HGETALL](https://redis.io/commands/hgetall) 进行检索。

还可使用高级别 API 通过接受元组数组的 [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple) 执行 `HSET`。 然后执行 [hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget) 以提取单个属性的值（在本例中为 `repo`）。

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

在下面的函数中，可了解如何使用 `LIST` 数据结构。 通过执行 [LPUSH](https://redis.io/commands/lpush)（使用低级别 API）将条目添加到列表，并使用高级别 [lpop](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) 方法从列表中检索该条目。 然后使用 [rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) 方法将几个条目添加到列表中，再使用低级别 [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) 方法提取这些条目。

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

可在此处查看一些 `SET` 操作。 使用 [sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd)（高级别 API）方法将几个条目添加到名为 `users` 的 `SET`。 然后执行 [SISMEMBER](https://redis.io/commands/hset)（低级别 API）以检查 `user1` 是否存在。 最后使用 [smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers) 以向量 ([Vec\<String\>](https://doc.rust-lang.org/std/vec/struct.Vec.html)) 形式提取所有集合条目并对其进行循环访问。

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

下面的 `sorted_set` 函数演示排序集数据结构。 使用低级别 API 调用 [ZADD](https://redis.io/commands/zadd)，为播放机 (`player-1`) 添加随机整数分数。 接下来使用 [zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) 方法（高级别 API）添加更多播放机（`player-2` 至 `player-5`）及其各自（随机生成）的分数。 排序集内的条目数是使用 [ZCARD](https://redis.io/commands/zcard) 确定的。 它被用作 [ZRANGE](https://redis.io/commands/zrange) 命令（使用低级别 API 调用）的限制，以按分数升序列出播放机。

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
}
```

## <a name="clone-the-sample-application"></a>克隆示例应用程序

首先从 GitHub 克隆应用程序开始。

1. 打开命令提示符，然后创建一个新的名为 `git-samples` 的文件夹。

    ```bash
    md "C:\git-samples"
    ```

1. 打开 git 终端窗口（例如 git bash）。 使用 `cd` 转到新文件夹，你将在这里克隆示例应用。

    ```bash
    cd "C:\git-samples"
    ```

1. 运行下列命令，克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>运行应用程序

应用程序接受环境变量形式的连接和凭据。 

1. 在 [Azure 门户](https://portal.azure.com/)中为 Azure Cache for Redis 实例提取“主机名”和“访问密钥”（可通过访问密钥获得） 。

1. 将它们设置为相应的环境变量：

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. 在“终端”窗口中，切换到正确的文件夹。 例如：

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. 在终端中，运行以下命令以启动应用程序。

    ```shell
    cargo run
    ```
    
    将会看到以下输出：
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    如果要运行特定函数，请在 `main` 函数中注释禁止其他函数：
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
    ```

## <a name="clean-up-resources"></a>清理资源

用完资源组和资源后，可将它们删除。 通过删除在本快速入门中创建的内容，可避免产生费用。

> [!IMPORTANT]
> 删除资源组的操作不可逆，资源组以及其中的所有资源将被永久删除。 如果在要保留的现有资源组中创建了 Azure Redis 缓存实例，可从缓存“概述”页选择“删除”以便仅删除缓存 。 

删除资源组及其 Azure Redis 缓存实例：

1. 从 [Azure 门户](https://portal.azure.com)中，搜索并选择“资源组”。
1. 在“按名称筛选”文本框中，输入包含你的缓存实例的资源组的名称。 然后，从搜索结果中选择它。 
1. 在资源组页上，选择“删除资源组”。
1. 键入资源组名称，然后选择“删除”。
   
   ![删除 Azure Redis 缓存的资源组](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何使用 Redis 的 Rust 驱动程序在 Azure Cache for Redis 中进行连接和执行操作。

> [!div class="nextstepaction"]
> [创建简单的使用 Azure Redis 缓存的 ASP.NET Web 应用。](./cache-web-app-howto.md)
