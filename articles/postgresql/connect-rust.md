---
title: 快速入门：使用 Rust - Azure Database for PostgreSQL — 单一服务器进行连接
description: 本快速入门提供了可用于从 Azure Database for PostgreSQL — 单一服务器连接和查询数据的 Rust 代码示例。
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 00adc50ac14e627eb356a3e62ce0faa5a9716aa3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505547"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>快速入门：使用 Rust 进行连接并在 Azure Database for PostgreSQL — 单一服务器中查询数据

在本文中，你将了解如何通过探索示例代码中实现的 CRUD（创建、读取、更新、删除）操作来使用[适用于 Rust 的 PostgreSQL 驱动程序](https://github.com/sfackler/rust-postgres)与 Azure Database for PostgreSQL 进行交互。 最后，你可以在本地运行应用程序，以查看实际运行效果。

## <a name="prerequisites"></a>必备条件
对于本快速入门，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)。
- 已安装 [Rust](https://www.rust-lang.org/tools/install) 最新版本。
- 使用 [Azure 门户](./quickstart-create-server-database-portal.md) <br/> 或使用 [Azure CLI](./quickstart-create-server-database-azure-cli.md) 创建一个 Azure Database for PostgreSQL 单一服务器。
- 请完成以下操作之一以启用连接，具体取决于你使用的是公共访问还是私有访问。

  |操作| 连接方法|操作指南|
  |:--------- |:--------- |:--------- |
  | **配置防火墙规则** | 公用 | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | 配置服务终结点 | 公用 | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | 配置专用链接 | Private | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- 已安装 [Git](https://git-scm.com/downloads)。

## <a name="get-database-connection-information"></a>获取数据库连接信息
连接到 Azure Database for PostgreSQL 数据库需要完全限定的服务器名称和登录凭据。 可以从 Azure 门户获取此信息。

1. 在 [Azure 门户](https://portal.azure.com/)中，搜索 Azure Database for PostgreSQL 服务器名称并选择该名称。
1. 在服务器的“概述”页上，复制完全限定的“服务器名称”和“管理员用户名”  。 完全限定的“服务器名称”始终为“\<my-server-name>.postgres.database.azure.com”的格式，“管理员用户名”始终为“\<my-admin-username>@\<my-server-name>”的格式。

## <a name="review-the-code-optional"></a>查看代码（可选）

如果有意了解代码的工作原理，可以查看以下代码片段。 否则，可以直接跳到[运行应用程序](#run-the-application)。

### <a name="connect"></a>连接

`main` 函数首先连接到 Azure Database for PostgreSQL，具体取决于连接信息的一下环境变量：`POSTGRES_HOST`、`POSTGRES_USER`、`POSTGRES_PASSWORD`、`POSTGRES_DBNAME`。 默认情况下，PostgreSQL 数据库服务配置为需要 `TLS` 连接。 如果客户端应用程序不支持 `TLS` 连接，则可以选择禁用 `TLS`。 有关详细信息，请参阅[在 Azure Database for PostgreSQL（单一服务器）中配置 TLS 连接](./concepts-ssl-connection-security.md)。

本文中的示例应用程序将 TLS 与 [postgres-openssl 包装箱](https://crates.io/crates/postgres-openssl/)一起使用。 [postgres::Client::connect](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) 函数用于启动连接，并在此发生故障时退出程序。

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>放置和创建表格

示例应用程序使用简单的 `inventory` 表来演示 CRUD（创建、读取、更新、删除）操作。

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

在创建新函数之前，`drop_create_table` 函数最初会尝试 `DROP` `inventory` 该表。 由于你始终从已知的（干净）状态开始，就可以更轻松地进行学习/试验。 [execute](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) 方法用于创建和放置操作。 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>插入数据

`insert_data` 向 `inventory` 表中添加项。 它使用 [prepare](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare) 函数创建[预定义语句](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html)。 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

另请注意 [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed) 方法的用法，此方法允许显式指定查询参数的类型。

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

最后，使用 `for` 循环添加 `item-3`、`item-4` 和 `item-5`，并为每一项随机生成数量。

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>查询数据

`query_data` 函数演示如何从 `inventory` 表中检索数据。 [query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) 方法用于按 `id` 获取项目。 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

库存表中的所有行都通过 [query](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query) 方法使用 `select * from` 查询来提取。 循环访问返回的行使用 [get](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get) 提取每个列的值。 

> [!TIP]
> 请注意查看 `get` 如何通过其在行中的数值索引或列名称指定列。

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>更新数据

`update_date` 函数会随机更新所有项的数量。 由于 `insert_data` 函数已添加 `5` 行，因此 `for` 循环 - `for n in 1..=5` 须考虑同样的情形

> [!TIP]
> 请注意，我们使用 `query` 而非 `execute`，因为我们想要找回 `id` 和最近生成的 `quantity`（使用 [RETURNING 子句](https://www.postgresql.org/docs/current/dml-returning.html)）。

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>删除数据

最后，该 `delete` 函数演示如何按照 `id` 从 `inventory` 表中删除项。 `id` 是随机选择的，是 `1` 至 `5` 之间的随机整数（包括 `5`），因为 `insert_data` 函数已添加 `5` 行以开始操作。 

> [!TIP]
> 请注意，我们使用 `query` 而非 `execute`，因为我们想要找回我们刚刚删除的项信息（使用 [RETURNING 子句](https://www.postgresql.org/docs/current/dml-returning.html)）。

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>运行应用程序

1. 若要开始，请运行以下命令来克隆示例存储库：

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. 设置所需的环境变量，包含你之前从 Azure 门户复制的值：

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. 若要运行应用程序，请更改为克隆文件所在的目录，并执行 `cargo run`：

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    应该会看到与下面类似的输出：

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. 若要进行确认，你还可以[使用 psql](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) 连接到 Azure Database for PostgreSQL，并对数据库运行查询，例如：

    ```sql
    select * from inventory;
    ```

[存在问题？请告诉我们](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>清理资源

若要清理本快速入门中使用的所有资源，请使用以下命令删除该资源组：

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用门户管理 Azure Database for PostgreSQL 服务器](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [使用 CLI 管理 Azure Database for PostgreSQL 服务器](./how-to-manage-server-cli.md)<br/>

[找不到要查找的内容？请告诉我们。](https://aka.ms/postgres-doc-feedback)
