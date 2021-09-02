---
title: 在适用于 Linux 的 Docker 上运行 Azure Cosmos DB 仿真器
description: 了解如何在 Linux 和 macOS 上运行和使用 Azure Cosmos DB Linux 仿真器。 使用此仿真器，可以在本地免费开发和测试应用程序，无需使用 Azure 订阅。
ms.service: cosmos-db
ms.topic: how-to
author: StefArroyo
ms.author: esarroyo
ms.date: 06/04/2021
ms.openlocfilehash: c0b7dff4ac6f19b4abcf27ccf98cd8d57b270e21
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111483"
---
# <a name="run-the-emulator-on-docker-for-linux-preview"></a>在用于 Linux 的 Docker（预览版）上运行仿真器
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

为方便进行开发，Azure Cosmos DB Linux 仿真器提供了一个模拟 Azure Cosmos DB 服务的本地环境。 目前，Linux 仿真器仅支持 SQL API。 使用 Azure Cosmos DB 模拟器可在本地开发和测试应用程序，无需创建 Azure 订阅且不会产生任何费用。 如果对应用程序在 Azure Cosmos DB Linux 仿真器中的工作情况感到满意，可以转为在云中切换到使用 Azure Cosmos DB 帐户。 本文介绍了如何在 macOS 和 Linux 环境中安装和使用仿真器。

> [!NOTE]
> Cosmos DB Linux 仿真器目前处于预览模式，仅支持 SQL API。 与 Windows 版本相比，在仿真器每秒处理的请求数方面，性能可能会略微降低。 物理分区的默认数量为 10，此默认数量直接影响可预配的容器数量。
> 
> 不建议在生产环境中使用仿真器（预览版）。 对于更大的工作负载，请使用 [Windows 仿真器](local-emulator.md)。

## <a name="how-does-the-emulator-work"></a>模拟器如何工作？

Azure Cosmos DB Linux 仿真器提供对 Azure Cosmos DB 服务的高保真仿真。 它支持与 Azure Cosmos DB 等效的功能，包括创建数据、查询数据、预配和缩放容器，以及执行存储过程和触发器。 可以使用 Azure Cosmos DB Linux 仿真器开发和测试应用程序，并更新 Azure Cosmos DB 连接终结点将其部署到全球范围内的 Azure。

使用仿真器时，依赖于 Azure 基础结构的功能（例如全球复制、读/写的个位数毫秒延迟，以及可调整的一致性级别）不适用。

## <a name="differences-between-the-linux-emulator-and-the-cloud-service"></a>Linux 仿真器和云服务之间的差异
由于 Azure Cosmos DB 仿真器提供一个在本地开发人员工作站上运行的模拟环境，因此该仿真器在功能上与 Azure Cosmos 云端帐户之间存在一些差异：

- 目前，仿真器中的“数据资源管理器”窗格仅完全支持 SQL API 客户端。

- 使用 Linux 仿真器时，只能在[预配吞吐量](set-throughput.md)模式下创建 Azure Cosmos 帐户；目前它不支持[无服务器](serverless.md)模式。

- Linux 仿真器不是一项可缩放的服务，它不支持大量容器。 使用 Azure Cosmos DB 仿真器时，默认情况下，最多可创建 10 个 400 RU/s 的固定大小容器（仅支持使用 Azure Cosmos DB SDK 进行创建），或 5 个不受限容器。 有关如何更改此值的详细信息，请参阅[设置 PartitionCount 值](emulator-command-line-parameters.md#set-partitioncount)一文。

- 虽然[一致性级别](consistency-levels.md)可以仅针对测试场景使用命令行参数进行调整（默认设置为“会话”），但用户可能不希望看到与云服务中的行为相同的行为。 例如，除了向 Cosmos DB SDK 发送帐户的默认一致性信号外，强和有限过期的一致性不会对仿真器产生任何影响。

- Linux 仿真器未提供[多区域复制](distribute-data-globally.md)功能。

- 因为 Azure Cosmos DB Linux 仿真器副本并不总是能反映出 Azure Cosmos DB 服务中的最新更改，因此应始终使用 [Azure Cosmos DB 容量规划器](estimate-ru-with-capacity-planner.md)来准确估计应用程序的吞吐量 (RU) 需求。

- Linux 仿真器支持的最大 ID 属性大小为 254 个字符。

## <a name="run-the-linux-emulator-on-macos"></a><a id="run-on-macos"></a>在 macOS 上运行 Linux 仿真器

> [!NOTE]
> 仿真器仅支持 Macbook 与 Intel 处理器。

若要开始操作，请访问 Docker Hub 并安装 [适用于 macOS 的 Docker Desktop](https://hub.docker.com/editions/community/docker-ce-desktop-mac/)。 使用以下步骤在 macOS 上运行仿真器：

[!INCLUDE[linux-emulator-instructions](includes/linux-emulator-instructions.md)]

## <a name="install-the-certificate"></a><a id="install-certificate"></a>安装证书

1. 运行仿真器后，使用其他终端将本地计算机的 IP 地址加载到变量中。

    ```bash
    ipaddr="`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' | head -n 1`"
    ```

1. 接下来，下载仿真器的证书。

    ```bash
    curl -k https://$ipaddr:8081/_explorer/emulator.pem > emulatorcert.crt
    ```


## <a name="consume-the-endpoint-via-ui"></a><a id="consume-endpoint-ui"></a>通过 UI 使用终结点

仿真器使用自签名证书来确保到其终结点的连接安全，并且需要手动信任。 使用以下步骤通过 UI 使用所需的 Web 浏览器来使用终结点：

1. 请确保已下载仿真器自签名证书

   ```bash
   curl -k https://$ipaddr:8081/_explorer/emulator.pem > emulatorcert.crt
   ```

1. 在 Mac 上打开 Keychain Access 应用以导入仿真器证书。

1. 选择“文件”和“导入项”，然后导入 emulatorcert.crt  。

1. 将 emulatorcert.crt 加载到 KeyChain 后，双击 localhost 名称，将信任设置更改为“始终信任” 。

1. 现可浏览到 `https://localhost:8081/_explorer/index.html` 或 `https://{your_local_ip}:8081/_explorer/index.html`，并检索仿真器的连接字符串。

还可以选择对应用程序禁用 SSL 验证。 建议仅用于开发目的，并且在生产环境中运行时不应这样做。

## <a name="run-the-linux-emulator-on-linux-os"></a><a id="run-on-linux"></a>在 Linux OS 上运行 Linux 仿真器

若要开始操作，请使用 `apt` 包并安装最新版本的 Docker。

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

如果使用适用于 Linux 的 Windows 子系统 (WSL)，请运行以下命令获取 `ifconfig`：

```bash
sudo apt-get install net-tools
```

使用以下步骤在 Linux 上运行仿真器：

[!INCLUDE[linux-emulator-instructions](includes/linux-emulator-instructions.md)]

4. 运行仿真器后，使用其他终端将本地计算机的 IP 地址加载到变量中。

    ```bash
    ipaddr="`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' | head -n 1`"
    ```

5. 接下来，下载仿真器的证书。 或者，在仿真器终结点准备好接收来自其他应用程序的请求时，还可以使用下面下载自签名仿真器证书的终结点来发送信号。

    ```bash
    curl -k https://$ipaddr:8081/_explorer/emulator.pem > ~/emulatorcert.crt
    ```

6. 将 CRT 文件复制到你的 Linux 分发版中包含自定义证书的文件夹。 在 Debian 分发版中，它通常位于 `/usr/local/share/ca-certificates/`。

   ```bash
   cp ~/emulatorcert.crt /usr/local/share/ca-certificates/
   ```

7. 更新 TLS/SSL 证书，这将更新 `/etc/ssl/certs/` 文件夹。

   ```bash
   update-ca-certificates
   ```

    对于基于 Java 的应用程序，必须将证书导入到 [Java 受信任存储](local-emulator-export-ssl-certificates.md)。

    ```bash
    keytool -keystore ~/cacerts -importcert -alias  emulator_cert -file ~/emulatorcert.crt
    java -ea -Djavax.net.ssl.trustStore=~/cacerts -Djavax.net.ssl.trustStorePassword="changeit" $APPLICATION_ARGUMENTS
    ```

## <a name="configuration-options"></a><a id="config-options"></a>配置选项

|名称  |默认  |说明  |
|---------|---------|---------|
|  端口：`-p`   |         |   目前，仿真器终结点仅需要端口 8081 和 10251-10255。     |
| `AZURE_COSMOS_EMULATOR_PARTITION_COUNT`    |    10     |    控制物理分区的总数，而物理分区又控制在给定时间点可以创建和存在的容器数。 建议从小规模开始，以改进仿真器的启动时间，即 3.     |
|  内存：`-m`   |         | 对于内存，需要 3 GB 或更多。     |
| 核心：`--cpus`  |         |   请确保预配足够的内存和 CPU 核心；虽然仿真器运行的核心数可能最少为 0.5（虽然非常慢），但推荐至少 2 个核心。      |
|`AZURE_COSMOS_EMULATOR_ENABLE_DATA_PERSISTENCE`  | false  | 此设置本身有助于在容器重启之间保存数据。  |

## <a name="troubleshoot-issues"></a><a id="troubleshoot-issues"></a>排查问题

本部分提供有关使用 Linux 仿真器时排查错误的提示。

### <a name="connectivity-issues"></a>连接问题

#### <a name="my-app-cant-connect-to-emulator-endpoint-the-ssl-connection-could-not-be-established-or-i-cant-start-the-data-explorer"></a>我的应用无法连接到仿真器终结点，（“无法建立 SSL 连接”）或无法启动数据资源管理器

- 请确保仿真器正在运行以下命令：

    ```bash
    docker ps --all
    ```

- 验证具体的仿真器容器是否处于运行状态。

- 验证是否有其他应用程序正在使用仿真器端口：8081 和 10250-10255。

- 验证容器端口 8081 是否已正确映射，并且是否可从容器之外的环境中进行访问。  

   ```bash
   netstat -lt
   ```

- 尝试使用 Docker 容器的 IP 地址而不非“localhost”访问仿真器的终结点和端口。

- 请确保已将仿真器自签名证书正确添加到 [KeyChain](#consume-endpoint-ui)。

- 对于 Java 应用程序，请确保已将证书导入到 [Java 证书存储部分](#run-on-linux)。

- 对于 .NET 应用程序，可禁用 SSL 验证：

# <a name="net-standard-21"></a>[.NET Standard 2.1+](#tab/ssl-netstd21)

对于在与 .NET Standard 2.1 或更高版本兼容的框架中运行的任何应用程序，我们可以利用 `CosmosClientOptions.HttpClientFactory`：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

对于在与 .NET Standard 2.0 兼容的框架中运行的任何应用程序，我们可以利用 `CosmosClientOptions.HttpClientFactory`：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

---

#### <a name="my-nodejs-app-is-reporting-a-self-signed-certificate-error"></a>我的 Node.js 应用正在报告自签名证书错误

如果尝试通过 `localhost` 以外的地址（例如容器 IP 地址）连接到仿真器，Node.js 将引发有关证书自签名的错误，即使已安装了证书也是如此。

可通过将环境变量 `NODE_TLS_REJECT_UNAUTHORIZED` 设置为 `0` 来禁用 TLS 验证：

```bash
NODE_TLS_REJECT_UNAUTHORIZED=0
```

建议仅将此标志用于本地开发，因为它禁用 Node.js 的 TLS。 有关详细信息，请参阅 [Node.js 文档](https://nodejs.org/api/cli.html#cli_node_tls_reject_unauthorized_value)和 [Cosmos DB 仿真器证书文档](local-emulator-export-ssl-certificates.md#how-to-use-the-certificate-in-nodejs)。

#### <a name="the-docker-container-failed-to-start"></a>Docker 容器无法启动

仿真器出错，并出现以下消息：

```bash
/palrun: ERROR: Invalid mapping of address 0x40037d9000 in reserved address space below 0x400000000000. Possible causes:
1. The process (itself, or via a wrapper) starts up its own running environment sets the stack size limit to unlimited via syscall setrlimit(2);
2. The process (itself, or via a wrapper) adjusts its own execution domain and flag the system its legacy personality via syscall personality(2);
3. Sysadmin deliberately sets the system to run on legacy VA layout mode by adjusting a sysctl knob vm.legacy_va_layout.
```

此错误很可能是因为当前 Docker 主机处理器类型与我们的 Docker 映像不兼容；也就是说，此计算机是使用 M1 芯片组的 MacBook。

#### <a name="my-app-received-too-many-connectivity-related-timeouts"></a>我的应用收到太多与连接相关的超时信息

- 没有为 Docker 容器预配充足资源[（核心或内存）](#config-options)。 建议增加核心数，或者减少启动时预配的物理分区数。

- 确保 TCP 连接数不超过当前的操作系统设置。

- 尝试降低应用程序中文档的大小。
    
#### <a name="my-app-could-not-provision-databasescontainers"></a>我的应用无法预配数据库/容器

仿真器上预配的物理分区数太低。 删除未使用的数据库/集合，或者使用[大量物理分区](#config-options)启动仿真器。

### <a name="reliability-and-crashes"></a>可靠性和故障

- 仿真程序启动失败：

  - 请确保正在[运行适用于 Linux 的 Cosmos DB 仿真器的最新映像](#refresh-linux-container)。 否则，请参阅上述与连接相关的问题部分。

  - 如果 Cosmos DB 仿真器数据文件夹为“卷装载”，请确保卷具有足够的空间，并且可读/写。

  - 确认使用建议的设置创建的容器是否有效。 如果有效，则失败的原因很可能是启动容器时通过相应的 Docker 命令传递的其他设置。

  - 如果仿真器启动失败并出现以下错误：
  
    ```bash
    "Failed loading Emulator secrets certificate. Error: 0x8009000f or similar, a new policy might have been added to your host that prevents an application such as Azure Cosmos DB emulator from creating and adding self signed certificate files into your certificate store."
    ```

    即使在管理员上下文中运行也可能出现这种情况，因为通常由 IT 部门添加的特定策略优先于本地管理员。 在这种情况下，为仿真器使用 Docker 映像可能会有所帮助，只要仍有权将自签名仿真器 SSL 证书添加到主机上下文（这是 Java 和 .NET Cosmos SDK 客户端应用程序所必需的）。

- 仿真器出现故障：

  - 确认使用[建议的设置](#run-on-linux)创建的容器是否有效。 如果有效，则失败的原因很可能是启动容器时通过相应的 Docker 命令传递的其他设置。

  - 在附加模式下启动仿真器的 Docker 容器（请参阅 `docker start -it`）。

  - 收集与故障相关的转储/数据，并按照[所述步骤](#report-an-emulator-issue)报告问题。  

### <a name="data-explorer-errors"></a>数据资源管理器错误

- 我无法查看我的数据：

  - 请参阅上述与连接相关的问题部分。

  - 请确保正确导入并手动信任自签名仿真器证书，以便浏览器能够访问数据资源管理器页。

  - 尝试创建数据库/容器并使用数据资源管理器插入项。 如果成功，则问题的原因很可能就在应用程序中。 如果失败，[请联系 Cosmos DB 团队](#report-an-emulator-issue)。

### <a name="performance-issues"></a>性能问题

每秒的请求数较低，请求的延迟较高：

- 没有为 Docker 容器预配充足资源[（核心或内存）](#config-options)。 建议增加核心数，或者减少启动时预配的物理分区数。

## <a name="refresh-linux-container"></a><a id="refresh-linux-container"></a>刷新 Linux 容器

使用以下步骤刷新 Linux 容器：

1. 运行以下命令查看所有 Docker 容器。

   ```bash
   docker ps --all
   ```

1. 使用从上面的命令中检索到的 ID 删除容器。

   ```bash
   docker rm ID_OF_CONTAINER_FROM_ABOVE
   ```

1. 接下来列出所有 Docker 映像。

   ```bash
   docker images
   ```

1. 使用从上一步中检索到的 ID 删除映像。

   ```bash
   docker rmi ID_OF_IMAGE_FROM_ABOVE
   ```

1. 提取 Cosmos DB Linux 仿真器的最新映像。

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/linux/azure-cosmos-emulator
   ```

1. 若要启动已停止的容器，请运行以下内容：

   ```bash
   docker start -ai ID_OF_CONTAINER
   ```

## <a name="report-an-emulator-issue"></a>报告仿真器的问题

报告 Linux 仿真器的问题时，请提供尽可能多的有关问题的信息。 这些详细信息包括：

- 遇到的错误/问题的说明
- 环境（OS、主机配置）
- 计算机和处理器类型
- 用于创建和启动仿真器的命令（如果使用 Docker Compose，则使用 YML 文件）
- 工作负载的说明
- 使用的数据库/集合和项的示例
- 包括在附加模式下启动仿真器的 Docker 容器的控制台输出
- 将以上全部内容发送到 [Azure Cosmos DB 团队](mailto:cdbportalfeedback@microsoft.com)。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何使用 Azure Cosmos DB Linux 仿真器进行免费的本地开发。 你现在可以继续学习下面的文章：

- [导出要用于 Java、Python 和 Node.js 应用的 Azure Cosmos DB 模拟器证书](local-emulator-export-ssl-certificates.md)
- [使用模拟器调试问题](troubleshoot-local-emulator.md)
