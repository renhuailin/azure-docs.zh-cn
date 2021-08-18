---
title: 使用 Azure Cosmos DB 模拟器在本地进行安装和开发
description: 了解如何在 Windows、Linux、macOS 和 Windows Docker 环境中安装和使用 Azure Cosmos DB 模拟器。 使用此模拟器，可以在本地免费开发和测试应用程序，无需创建 Azure 订阅。
ms.service: cosmos-db
ms.topic: how-to
author: StefArroyo
ms.author: esarroyo
ms.date: 09/22/2020
ms.custom: devx-track-csharp, contperf-fy21q1
ms.openlocfilehash: b2c6c4dd8d0f7c5b30401f7ef586b7d1be8267ce
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749936"
---
# <a name="install-and-use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>安装并使用 Azure Cosmos DB 模拟器进行本地开发和测试
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

为方便进行开发，Azure Cosmos DB 模拟器提供了一个模拟 Azure Cosmos DB 服务的本地环境。 使用 Azure Cosmos DB 模拟器可在本地开发和测试应用程序，无需创建 Azure 订阅且不会产生任何费用。 如果对应用程序在 Azure Cosmos DB 模拟器中的运行情况感到满意，则可以通过切换在云中使用 Azure Cosmos 帐户。 本文介绍了如何在 Windows、Linux、macOS 和 Windows Docker 环境中安装并使用模拟器。

## <a name="download-the-emulator"></a>下载模拟器

若要开始，请在本地计算机上下载并安装最新版本的 Azure Cosmos DB 模拟器。 [模拟器发行说明](local-emulator-release-notes.md)一文列出了所有可用版本以及每个版本中所做的功能更新。

:::image type="icon" source="media/local-emulator/download-icon.png" border="false"::: **[下载 Azure Cosmos DB 模拟器](https://aka.ms/cosmosdb-emulator)**

你可以使用 Azure Cosmos DB 模拟器结合 [SQL](local-emulator.md#sql-api)、[Cassandra](local-emulator.md#cassandra-api)、[MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb)、[Gremlin](local-emulator.md#gremlin-api) 和[表](local-emulator.md#table-api) API 帐户来开发应用程序。 目前，模拟器中的数据资源管理器仅完全支持查看 SQL 数据；目前无法查看使用 MongoDB、Gremlin/Graph 和 Cassandra 客户端应用程序创建的数据。 若要了解详细信息，请参阅[如何从不同的 API 连接到模拟器终结点](#connect-with-emulator-apis)。

## <a name="how-does-the-emulator-work"></a>模拟器如何工作？

Azure Cosmos DB 模拟器提供对 Azure Cosmos DB 服务的高保真模拟。 它支持与 Azure Cosmos DB 等效的功能，包括创建数据、查询数据、预配和缩放容器，以及执行存储过程和触发器。 可以使用 Azure Cosmos DB 仿真器开发和测试应用程序，并通过更新 Azure Cosmos DB 连接终结点将其部署到全球范围内的 Azure。

虽然模拟 Azure Cosmos DB 服务很逼真，但模拟器的实现不同于服务。 例如，模拟器使用标准 OS 组件，例如用于暂留的本地文件系统和用于连接的 HTTPS 协议堆栈。 使用仿真器时，依赖于 Azure 基础结构的功能（例如全球复制、读/写的个位数毫秒延迟，以及可调整的一致性级别）不适用。

可通过 [Azure Cosmos DB 数据迁移工具](https://github.com/azure/azure-documentdb-datamigrationtool)在 Azure Cosmos DB 模拟器与 Azure Cosmos DB 服务之间迁移数据。

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>模拟器和云服务之间的差异

由于 Azure Cosmos DB 模拟器提供一个在本地开发人员工作站上运行的模拟环境，因此该模拟器在功能上与 Azure Cosmos 云端帐户之间存在一些差异：

* 目前，模拟器中的“数据资源管理器”窗格仅完全支持 SQL API 客户端。 不完全支持在“数据资源管理器”中查看和操作 Azure Cosmos DB API，例如 MongoDB API、表 API、Graph API 和 Cassandra API。

* 模拟器仅支持一个固定的帐户和一个公开的主密钥。 使用 Azure Cosmos DB 模拟器时，无法重新生成密钥，但是可以使用[命令行](emulator-command-line-parameters.md)选项更改默认密钥。

* 使用模拟器时，只能在[预配吞吐量](set-throughput.md)模式下创建 Azure Cosmos 帐户；目前它不支持[无服务器](serverless.md)模式。

* 此模拟器不是一项可缩放的服务，它不支持大量容器。 使用 Azure Cosmos DB 模拟器时，默认情况下，最多可创建 25 个 400 RU/s 的固定大小容器（仅支持使用 Azure Cosmos DB SDK 进行创建），或 5 个不受限容器。 有关如何更改此值的详细信息，请参阅[设置 PartitionCount 值](emulator-command-line-parameters.md#set-partitioncount)一文。

* 此模拟器没有像云服务一样提供各种 [Azure Cosmos DB 一致性级别](consistency-levels.md)。

* 此模拟器未提供[多区域复制](distribute-data-globally.md)。

* 因为 Azure Cosmos DB 模拟器副本并不总是能反映出 Azure Cosmos DB 服务中的最新更改，因此应始终使用 [Azure Cosmos DB 容量规划器](estimate-ru-with-capacity-planner.md)来准确估计应用程序的吞吐量 (RU) 需求。

* 模拟器支持的最大 ID 属性大小为 254 个字符。

## <a name="install-the-emulator"></a>安装模拟器

安装模拟器之前，请确保满足以下硬件和软件要求：

* 所需软件：
  * 当前支持 Windows Server 2016、2019 或 Windows 10 主机操作系统。 当前不支持启用了 Active Directory 的主机操作系统。
  * 64 位操作系统

* 最低硬件要求：
  * 2-GB RAM
  * 10-GB 可用硬盘空间

* 若要安装、配置和运行 Azure Cosmos DB 模拟器，必须在计算机上具有管理特权。 模拟器将添加一个证书，并设置防火墙规则，以便运行其服务。 因此，模拟器需要管理员权限才能执行此类操作。

若要开始，请在本地计算机上下载并安装最新版本的 [Azure Cosmos DB 模拟器](https://aka.ms/cosmosdb-emulator)。 如果在安装模拟器时遇到任何问题，请参阅[模拟器故障排除](troubleshoot-local-emulator.md)一文来进行调试。

根据你的系统要求，你可以在 [Windows](#run-on-windows)、[用于 Windows 的 Docker](local-emulator-on-docker-windows.md)、[Linux 或 macOS](#run-on-linux-macos) 上运行模拟器，如本文后续部分所述。

## <a name="check-for-emulator-updates"></a>检查模拟器更新

模拟器的每个版本都附带了一组功能更新或 bug 修复。 若要查看可用版本，请阅读[模拟器发行说明](local-emulator-release-notes.md)一文。

安装后，如果已使用默认设置，则与模拟器对应的数据将保存在 %LOCALAPPDATA%\CosmosDBEmulator 位置。 你可以使用可选的数据路径设置来配置一个不同的位置，即作为[命令行参数](emulator-command-line-parameters.md)的 `/DataPath=PREFERRED_LOCATION`。 在 Azure Cosmos DB 模拟器的一个版本中创建的数据不保证在使用不同版本时可以访问。 如果需要长期保存数据，建议将该数据存储在 Azure Cosmos 帐户中而不是 Azure Cosmos DB 模拟器中。

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>在 Windows 上使用模拟器

默认情况下，Azure Cosmos DB 模拟器将安装在 `C:\Program Files\Azure Cosmos DB Emulator` 位置。 若要在 Windows 上启动 Azure Cosmos DB 模拟器，请选择“开始”按钮或按 Windows 键。 开始键入“Azure Cosmos DB 模拟器”，并从应用程序列表中选择该模拟器。

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="选择“启动”按钮或按 Windows 键，开始键入“Azure Cosmos DB 模拟器”，然后从应用程序列表中选择该模拟器":::

在模拟器启动后，Windows 任务栏通知区域中会显示一个图标。 它会在浏览器中自动打开 Azure Cosmos 数据资源管理器，URL 为 `https://localhost:8081/_explorer/index.html`。

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Azure Cosmos DB 本地模拟器任务栏通知":::

还可以通过命令行或 PowerShell 命令启动和停止模拟器。 有关详细信息，请参阅[命令行工具参考](emulator-command-line-parameters.md)一文。

默认情况下，Azure Cosmos DB 模拟器在本地计算机（“localhost”）上运行，侦听端口 8081。 地址显示为 `https://localhost:8081/_explorer/index.html`。 如果关闭数据资源管理器后要重新打开它，可以在浏览器中打开该 URL 或通过 Windows 任务栏图标中的 Azure Cosmos DB 模拟器启动，如下所示。

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Azure Cosmos 本地模拟器数据资源管理器启动器":::

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>在 Linux 或 macOS 上使用模拟器

目前 Azure Cosmos DB 模拟器只能在 Windows 上运行。 如果你使用的是 Linux 或 macOS，我们建议你使用 [Linux Emulator（预览版）](linux-emulator.md)，或在某个托管在虚拟机监控程序中的 Windows 虚拟机（例如 Parallels 或 VirtualBox）中运行此模拟器。
> [!NOTE]
> 每次重启虚拟机监控程序中托管的 Windows 虚拟机时，都必须重新导入证书，因为虚拟机的 IP 地址会发生更改。 如果已将虚拟机配置为保留 IP 地址，则不需要导入证书。

通过以下步骤在 Linux 或 macOS 环境中使用模拟器：

1. 在 Windows 虚拟机中运行以下命令，并记下 IPv4 地址：

   ```bash
   ipconfig.exe
   ```

1. 在应用程序中更改终结点 URL，以便使用由 `ipconfig.exe` 返回的 IPv4 地址，而不是使用 `localhost`。

1. 在 Windows VM 中，使用以下选项从命令行启动 Azure Cosmos DB 模拟器。 有关命令行支持的参数的详细信息，请参阅[模拟器命令行工具参考](emulator-command-line-parameters.md)：

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. 最后，你需要解决在 Linux 或 Mac 环境中运行的应用程序与模拟器之间的证书信任过程问题。 可以使用以下两个选项之一来解决证书问题：

   1. [将模拟器 TLS/SSL 证书导入到 Linux 或 Mac 环境中](#import-certificate)，或者
   2. [在应用程序中禁用 TLS/SSL 验证](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>选项 1：导入模拟器 TLS/SSL 证书

以下部分介绍了如何将模拟器 TLS/SSL 证书导入到 Linux 和 macOS 环境中。

#### <a name="linux-environment"></a>Linux 环境

如果在 Linux 中工作，则 .NET 依赖于 OpenSSL 来执行验证：

1. [以 PFX 格式导出证书](local-emulator-export-ssl-certificates.md#export-emulator-certificate)。 选择导出私钥时，PFX 选项可用。

1. 将该 PFX 文件复制到 Linux 环境。

1. 将 PFX 文件转换为 CRT 文件

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. 将 CRT 文件复制到你的 Linux 分发版中包含自定义证书的文件夹。 在 Debian 分发版中，它通常位于 `/usr/local/share/ca-certificates/`。

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. 更新 TLS/SSL 证书，这将更新 `/etc/ssl/certs/` 文件夹。

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>macOS 环境

如果在 Mac 中工作，请使用以下步骤：

1. [以 PFX 格式导出证书](local-emulator-export-ssl-certificates.md#export-emulator-certificate)。 选择导出私钥时，PFX 选项可用。

1. 将该 PFX 文件复制到 Mac 环境。

1. 打开 *Keychain Access* 应用程序并导入 PFX 文件。

1. 打开证书列表，并找到名为 `localhost` 的证书。

1. 打开该特定项的上下文菜单，选择“获取项”，然后在“信任” > “使用此证书时”选项下选择“始终信任”。   

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="打开该特定项的上下文菜单，选择“获取项”，然后在“信任 - 使用此证书时”选项下选择“始终信任”":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>选项 2：在应用程序中禁用 SSL 验证

建议仅出于开发目的禁用 SSL 验证，并且在生产环境中运行时不应这样做。 下面的示例演示了如何对 .NET 和 Node.js 应用程序禁用 SSL 验证。

# <a name="net-standard-21"></a>[.NET Standard 2.1+](#tab/ssl-netstd21)

对于在与 .NET Standard 2.1 或更高版本兼容的框架中运行的任何应用程序，我们可以利用 `CosmosClientOptions.HttpClientFactory`：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

对于在与 .NET Standard 2.0 兼容的框架中运行的任何应用程序，我们可以利用 `CosmosClientOptions.HttpClientFactory`：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

对于 Node.js 应用程序，可以在启动应用程序时修改 `package.json` 文件以设置 `NODE_TLS_REJECT_UNAUTHORIZED`：

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>启用对本地网络上的模拟器的访问权限

如果你有多台计算机使用单个网络，在一台计算机上设置了模拟器，而你想要从其他计算机访问它， 那么，在这种情况下，你需要启用对本地网络上的模拟器的访问权限。

可在本地网络中运行仿真器。 要启用网络访问，请在[命令行](emulator-command-line-parameters.md)中指定 `/AllowNetworkAccess` 选项，同时还需指定 `/Key=key_string` 或 `/KeyFile=file_name`。 可使用 `/GenKeyFile=file_name` 提前生成具有随机密钥的文件。 然后，可将其传递到 `/KeyFile=file_name` 或 `/Key=contents_of_file`。

首次启用网络访问时，用户应关闭模拟器，并删除模拟器的数据目录 %LOCALAPPDATA%\CosmosDBEmulator。

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>使用模拟器时对连接进行身份验证

与云中的 Azure Cosmos DB 一样，针对 Azure Cosmos DB 模拟器发出的每个请求都必须进行身份验证。 Azure Cosmos DB 模拟器仅支持通过 TLS 进行安全通信。 Azure Cosmos DB 模拟器支持单一固定帐户和用于主密钥身份验证的公开的身份验证密钥。 此帐户和密钥是允许用于 Azure Cosmos DB 模拟器的唯一凭据。 它们是：

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Azure Cosmos DB 模拟器支持的主密钥仅可用于该模拟器。 不能在 Azure Cosmos DB 模拟器中使用生产 Azure Cosmos DB 帐户和密钥。

> [!NOTE]
> 如果是使用 /Key 选项启动的模拟器，请使用所生成的密钥而不是默认密钥 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`。 有关 /Key 选项的详细信息，请参阅[命令行工具参考](emulator-command-line-parameters.md)。

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>使用模拟器连接到不同的 API

### <a name="sql-api"></a>SQL API

在桌面上运行 Azure Cosmos DB 模拟器以后，可以使用任何支持的 [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) 或 [Azure Cosmos DB REST API](/rest/api/cosmos-db/) 与模拟器进行交互。 Azure Cosmos DB 模拟器还包括内置数据资源管理器，它允许你为 SQL API 或 Azure Cosmos DB for Mongo DB API 创建容器。 使用数据资源管理器，你可以查看和编辑各个项，而无需编写任何代码。

```csharp
// Connect to the Azure Cosmos DB Emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB 的用于 MongoDB 的 API

在桌面上运行 Azure Cosmos DB 模拟器后，可以使用 [Azure Cosmos DB API for MongoDB](mongodb/mongodb-introduction.md) 与该模拟器进行交互。 在[命令提示符](emulator-command-line-parameters.md)下，以管理员身份使用“/EnableMongoDbEndpoint”启动模拟器。 然后，使用以下连接字符串来连接到 MongoDB API 帐户：

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>表 API

在桌面上运行 Azure Cosmos DB 模拟器后，可使用 [Azure Cosmos DB 表 API SDK](./table/tutorial-develop-table-dotnet.md) 与该模拟器进行交互。 在[命令提示符](emulator-command-line-parameters.md)处，以管理员身份使用“/EnableTableEndpoint”启动模拟器。 接下来，运行下列代码以连接到表 API 帐户：

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra API

使用“/EnableCassandraEndpoint”在管理员[命令提示符](emulator-command-line-parameters.md)处启动模拟器。 或者，也可设置环境变量 `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`。

1. [安装 Python 2.7](https://www.python.org/downloads/release/python-2716/)

1. [安装 Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

1. 在常规命令提示符窗口中运行以下命令：

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. 在 CQLSH shell 中，运行下列命令以连接到 Cassandra 终结点：

   ```bash
   CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
   DESCRIBE keyspaces;
   USE mykeyspace;
   CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
   INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
   SELECT * from table1;
   EXIT
   ```

### <a name="gremlin-api"></a>Gremlin API

使用“/EnableGremlinEndpoint”在管理员[命令提示符](emulator-command-line-parameters.md)处启动模拟器。 或者，也可设置环境变量 `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

1. [安装 apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4)。

1. 从模拟器的数据资源管理器中，创建数据库“db1”和集合“coll1”，并选择“/name”作为分区键

1. 在常规命令提示符窗口中运行以下命令：

   ```bash
   cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
   copy /y conf\remote.yaml conf\remote-localcompute.yaml
   notepad.exe conf\remote-localcompute.yaml
     hosts: [localhost]
     port: 8901
     username: /dbs/db1/colls/coll1
     password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
     connectionPool: {
     enableSsl: false}
     serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
     config: { serializeResultToString: true  }}

   bin\gremlin.bat
   ```

1. 在 Gremlin shell 中，运行下列命令以连接到 Gremlin 终结点：

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>卸载本地模拟器

使用以下步骤卸载模拟器：

1. 退出所有打开的本地模拟器实例，方法是：在系统任务栏上右键单击“Azure Cosmos DB 模拟器”图标，然后选择“退出”。  退出所有实例可能需要一分钟。

1. 在 Windows 搜索框中，键入“应用和功能”，然后选择“应用和功能(系统设置)”结果 。

1. 在应用列表中，滚动到“Azure Cosmos DB 模拟器”并将其选中，单击“卸载”，然后确认并再次选择“卸载”  。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何使用本地模拟器进行免费的本地开发。 你现在可以继续学习下面的文章：

* [导出要用于 Java、Python 和 Node.js 应用的 Azure Cosmos DB 模拟器证书](local-emulator-export-ssl-certificates.md)
* [使用命令行参数和 PowerShell 命令控制模拟器](emulator-command-line-parameters.md)
* [使用模拟器调试问题](troubleshoot-local-emulator.md)
