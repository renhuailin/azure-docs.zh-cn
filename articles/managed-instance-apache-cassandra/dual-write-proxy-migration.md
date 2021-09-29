---
title: 使用 Apache Spark 和双重写入代理实时迁移到 Azure Managed Instance for Apache Cassandra
description: 了解如何使用 Apache Spark 和双重写入代理实时迁移到 Azure Managed Instance for Apache Cassandra。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: d6fa48fb35d836fc7f08c98e7b1807068c000d84
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124797020"
---
# <a name="live-migration-to-azure-managed-instance-for-apache-cassandra-by-using-a-dual-write-proxy"></a>使用双重写入代理实时迁移到 Azure Managed Instance for Apache Cassandra

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra 目前以公共预览版形式提供。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

我们建议在可能的情况下，使用 Apache Cassandra 原生功能通过配置[混合群集](configure-hybrid-cluster.md)将现有群集中的数据迁移到 Azure Managed Instance for Apache Cassandra。 此功能使用 Apache Cassandra 的 gossip 协议以无缝方式将源数据中心的数据复制到新的托管实例数据中心。 但在某些情况下，源数据库版本不兼容，或者混合群集设置不可行。 

本教程介绍如何使用[双重写入代理](https://github.com/Azure-Samples/cassandra-proxy)和 Apache Spark 将数据实时迁移到 Azure Managed Instance for Apache Cassandra。 此方法的优点包括：

- 尽量减少对应用程序做出更改。 只需进行少量的配置更改甚至不进行任何配置更改，代理就能接受来自应用程序代码的连接。 它会将所有请求路由到源数据库，并以异步方式将写入路由到辅助目标。 
- 客户端线路协议依赖性。 由于此方法不依赖于后端资源或内部协议，因此可对实现 Apache Cassandra 线路协议的任何源或目标 Cassandra 系统使用此方法。

下图演示了该方法。

:::image type="content" source="./media/migration/live-migration.gif" alt-text="演示如何将数据实时迁移到 Azure Managed Instance for Apache Cassandra 的动画。" border="false":::

## <a name="prerequisites"></a>必备条件

* 使用 [Azure 门户](create-cluster-portal.md)或 [Azure CLI](create-cluster-cli.md) 预配一个 Azure Managed Instance for Apache Cassandra 群集。 确保可以[使用 CQLSH 连接到该群集](./create-cluster-portal.md#connecting-to-your-cluster)。

* [在托管的 Cassandra 虚拟网络中预配一个 Azure Databricks 帐户](deploy-cluster-databricks.md)。 确保该帐户可通过网络访问源 Cassandra 群集。 我们将在此帐户中创建一个 Spark 群集用于加载历史数据。

* 确保已将密钥空间/表方案从源 Cassandra 数据库迁移到目标 Cassandra 托管实例数据库。

## <a name="provision-a-spark-cluster"></a>预配 Spark 群集

建议选择 Azure Databricks 运行时版本 7.5，该版本支持 Spark 3.0。

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="显示如何查找 Azure Databricks 运行时版本的屏幕截图。":::

## <a name="add-spark-dependencies"></a>添加 Spark 依赖项

你需要将 Apache Spark Cassandra 连接器库添加到群集，以便连接到原生终结点和 Azure Cosmos DB Cassandra 终结点。 在群集中，选择“库” > “安装新库” > “Maven”，然后在 Maven 坐标中添加 `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0`  。

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="显示如何在 Azure Databricks 中搜索 Maven 包的屏幕截图。":::

选择“安装”，然后在安装完成后重启群集。

> [!NOTE]
> 安装 Cassandra 连接器库后，请务必重启 Azure Databricks 群集。

## <a name="install-the-dual-write-proxy"></a>安装双重写入代理

为了在双重写入期间获得最佳性能，我们建议在源 Cassandra 群集中的所有节点上安装该代理。

```bash
#assuming you do not have git already installed
sudo apt-get install git 

#assuming you do not have maven already installed
sudo apt install maven

#clone repo for dual-write proxy
git clone https://github.com/Azure-Samples/cassandra-proxy.git

#change directory
cd cassandra-proxy

#compile the proxy
mvn package
```

## <a name="start-the-dual-write-proxy"></a>启动双重写入代理

建议在源 Cassandra 群集中的所有节点上安装该代理。 至少，请运行以下命令在每个节点上启动代理。 请将 `<target-server>` 替换为目标群集中某个节点上的 IP 地址或服务器地址。 请将 `<path to JKS file>` 替换为本地 jks 文件的路径，将 `<keystore password>` 替换为相应的密码。  

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password>
```

以这种方式启动代理的前提是满足以下条件：

- 源和目标终结点具有相同的用户名和密码。
- 源和目标终结点实现安全套接字层 (SSL)。

如果源和目标终结点无法满足这些条件，请继续阅读以了解其他配置选项。

### <a name="configure-ssl"></a>配置 SSL

对于 SSL，可以实现现有的密钥存储（例如源群集使用的密钥存储），或者可以使用 `keytool` 创建自签名证书：

```bash
keytool -genkey -keyalg RSA -alias selfsigned -keystore keystore.jks -storepass password -validity 360 -keysize 2048
```
如果源或目标终结点不实现 SSL，则你还可为其禁用 SSL。 使用 `--disable-source-tls` 或 `--disable-target-tls` 标志：

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password> --disable-source-tls true  --disable-target-tls true 
```

> [!NOTE]
> 通过代理与数据库建立 SSL 连接时，请确保客户端应用程序使用的密钥存储和密码与双重写入代理所用的密钥存储和密码相同。

### <a name="configure-the-credentials-and-port"></a>配置凭据和端口

默认情况下将从客户端应用传递源凭据。 代理将使用这些凭据来与源和目标群集建立连接。 如前所述，此过程假设源和目标凭据相同。 如有必要，可以在启动代理时单独为目标 Cassandra 终结点指定不同的用户名和密码：

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

默认的源和目标端口将是 9042（如果未指定）。 如果目标或源 Cassandra 终结点在不同的端口上运行，你可以使用 `--source-port` 或 `--target-port` 指定不同的端口号： 

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

### <a name="deploy-the-proxy-remotely"></a>远程部署代理

在某些情况下，你可能不希望在群集节点本身上安装代理，而是希望将其安装在单独的计算机上。 在这种情况下，需要指定 `<source-server>` 的 IP 地址：

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar <source-server> <destination-server>
```

> [!NOTE]
> 如果你不在原生 Apache Cassandra 群集中的所有节点上安装并运行代理，则会影响应用程序的性能。 客户端驱动程序将无法与群集中的所有节点建立连接。 

### <a name="allow-zero-application-code-changes"></a>实现零应用程序代码更改

默认情况下，代理侦听端口 29042。 必须将应用程序代码更改为指向此端口。 但是，可以更改代理侦听的端口。 如果你想要消除应用程序级别的代码更改，可采取以下做法：

- 让源 Cassandra 服务器在其他端口上运行。
- 让代理在标准 Cassandra 端口 9042 上运行。

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042
```

> [!NOTE]
> 在群集节点上安装代理不需要重启节点。 但是，如果你有许多应用程序客户端，并希望代理在标准 Cassandra 端口 9042 上运行，以便无需进行任何应用程序级别的代码更改，则需要更改 [Apache Cassandra 默认端口](https://cassandra.apache.org/doc/latest/cassandra/faq/#what-ports-does-cassandra-use)。 然后需要重启群集中的节点，并将源端口配置为你已为源 Cassandra 群集定义的新端口。 
>
> 在以下示例中，我们将源 Cassandra 群集更改为在端口 3074 上运行，并在端口 9042 上启动群集：
>
>```bash
>java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042 --source-port 3074
>``` 

### <a name="force-protocols"></a>强制实施协议

代理提供强制实施协议的功能。如果源终结点比目标更高级或者不受支持，可能需要强制实施协议。 在这种情况下，可以指定 `--protocol-version` 和 `--cql-version` 来强制实施协议，以便与目标相符：

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --protocol-version 4 --cql-version 3.11
```

在运行双重写入代理后，需要更改应用程序客户端上的端口并重启。 （或者更改 Cassandra 端口并重启群集，如果已选择此方法的话。）然后，代理会开始将写入转发到目标终结点。 你可以了解代理工具中提供的[监视和指标](https://github.com/Azure-Samples/cassandra-proxy#monitoring)。 

## <a name="run-the-historical-data-load"></a>运行历史数据加载

若要加载数据，请在 Azure Databricks 帐户中创建一个 Scala 笔记本。 将源和目标 Cassandra 配置替换为相应的凭据，并替换源和目标密钥空间和表。 根据需要在以下示例中为每个表添加更多变量，然后运行该示例。 在应用程序开始向双重写入代理发送请求后，你便可以迁移历史数据。 

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val sourceCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val targetCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.connection.remoteConnectionsPerExecutor" -> "1",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//set timestamp to ensure it is before read job starts
val timestamp: Long = System.currentTimeMillis / 1000

//Read from source Cassandra
val DFfromSourceCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(sourceCassandra)
  .load
  
//Write to target Cassandra
DFfromSourceCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(targetCassandra)
  .option("writetime", timestamp)
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> 在以上 Scala 示例中可以看到，在读取源表中的所有数据之前 `timestamp` 设置为当前时间。 然后 `writetime` 设置为此回溯时间戳。 这可以确保通过加载历史数据而写入到目标终结点的记录，无法覆盖在读取历史数据时从双重写入代理传入的具有更迟时间戳的更新内容。
>
> 如果你出于任何原因需要保留确切的时间戳，应采用可保留时间戳的历史数据迁移方法，如[此示例](https://github.com/Azure-Samples/cassandra-migrator)所示。 

## <a name="validate-the-source-and-target"></a>验证源和目标

历史数据加载完成后，数据库应已同步并准备好进行直接转换。 但是，我们建议对源和目标执行验证，以确保在最终进行直接转换之前请求结果匹配。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure CLI 管理 Azure Managed Instance for Apache Cassandra 资源](manage-resources-cli.md)
