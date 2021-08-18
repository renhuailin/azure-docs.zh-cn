---
title: 使用 Blitzz 将数据从 Oracle 迁移到 Azure Cosmos DB Cassandra API
description: 了解如何使用 Blitzz 将数据从 Oracle 数据库迁移到 Azure Cosmos DB Cassandra API。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 328e472ff4f596b40ef676c02e64fc9aa2ef6d3c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778285"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>使用 Blitzz 将数据从 Oracle 迁移到 Azure Cosmos DB Cassandra API 帐户
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Azure Cosmos DB 中的 Cassandra API 已成为在 Oracle 上运行的企业工作负荷的极佳选择，原因各种各样，例如：

* **更高的可伸缩性和可用性：** 它可以消除单一故障点，并提高应用程序的可伸缩性和可用性。

* **显著节省成本：** 使用 Azure Cosmos DB 可以节省成本，其中包括 VM、带宽以及任何适用 Oracle 许可证的成本。 另外，你无需管理数据中心、服务器、SSD 存储、网络以及电力成本。

* **无管理和监视开销：** 作为一个完全托管式的云服务，Azure Cosmos DB 消除了管理和监视大量设置所带来的开销。

可以通过多种方式将数据库工作负荷从一个平台迁移到另一个平台。 [Blitzz](https://www.blitzz.io) 工具可以安全可靠地将各种数据库中的数据迁移到 Azure Cosmos DB，且不会造成停机。 本文介绍如何执行所需步骤，以便使用 Blitzz 将数据从 Oracle 数据库迁移到 Azure Cosmos DB Cassandra API。

## <a name="benefits-using-blitzz-for-migration"></a>使用 Blitzz 进行迁移的优势

Blitzz 的迁移解决方案按照分步方法迁移复杂的运营工作负荷。 下面是 Blitzz 的零停机迁移计划的一些重要方面：

* 它可以自动将业务逻辑（表、索引、视图）从 Oracle 数据库迁移到 Azure Cosmos DB。 不需手动创建架构。

* Blitzz 提供大规模并行数据库复制。 它可以使用称作“变更数据捕获 (CDC)”的技术，让源平台和目标平台在迁移过程中保持同步状态。 Blitzz 可以通过 CDC 从源数据库 (Oracle) 持续提取更改流，并将其应用到目标数据库 (Azure Cosmos DB)。

* 它具有容错功能，可以保证数据的准确传输，即使系统出现硬件或软件故障。

* 它可以在传输过程中使用各种安全方法（例如 TLS/SSL、加密）对数据进行保护。

* 它提供的服务可将以 PL/SQL 编写的复杂业务逻辑转换为 Azure Cosmos DB 中的等效业务逻辑。

## <a name="steps-to-migrate-data"></a>迁移数据的步骤

本部分介绍如何执行所需步骤，以便设置 Blitzz 并将数据从 Oracle 数据库迁移到 Azure Cosmos DB。

1. 在打算安装 Blitzz 复制器的计算机中，添加安全证书。 该证书是 Blitzz 复制器与指定的 Azure Cosmos DB 帐户建立 TLS 连接所需的。 可以使用以下步骤来添加证书：

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. 可以在 [Blitzz 网站](https://www.blitzz.io)上请求演示版，以获取 Blitzz 安装和二进制文件。 或者，可以向相关团队发送[电子邮件](mailto:success@blitzz.io)。

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Blitzz 复制器工具下载":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Blitzz 复制器文件":::

1. 在 CLI 终端设置源数据库配置。 使用 **`vi conf/conn/oracle.yml`** 命令打开配置文件，添加一个包含 Oracle 节点 IP 地址的逗号分隔的列表，并添加端口号、用户名、密码以及任何其他必需的详细信息。 以下代码显示一个示例配置文件：

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png" alt-text="打开 Oracle 连接编辑器":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png" alt-text="Oracle 连接配置":::

   填充配置详细信息后，保存并关闭该文件。

1. （可选）设置源数据库筛选器文件。 筛选器文件指定要迁移的架构或表。 使用 **`vi filter/oracle_filter.yml`** 命令打开配置文件，输入以下配置详细信息：

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   填充数据库筛选器详细信息以后，保存并关闭该文件。

1. 接下来，设置目标数据库的配置。 在定义配置之前，请[创建一个 Azure Cosmos DB Cassandra API 帐户](manage-data-dotnet.md#create-a-database-account)。 从数据中[选择适当的分区键](../partitioning-overview.md#choose-partitionkey)，然后创建密钥空间，以及用于存储迁移的数据的表。

1. 在迁移数据之前，请将容器吞吐量提高到快速迁移应用程序所需的量。 例如，可将吞吐量提高到 100000 RU。 在开始迁移之前提高吞吐量可以缩短数据迁移时间。 

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="缩放 Azure Cosmos 容器吞吐量":::

   必须在迁移完成后降低吞吐量。 可以根据存储的数据量以及每次操作所需的 RU 数，估算数据迁移后所需的吞吐量。 若要详细了解如何估算所需的 RU，请参阅[预配容器和数据库的吞吐量](../set-throughput.md)和[使用 Azure Cosmos DB Capacity Planner 估算 RU/秒](../estimate-ru-with-capacity-planner.md)这两篇文章。

1. 在“连接字符串”窗格中获取“联系点、端口、用户名”，以及 Azure Cosmos 帐户的“主密码”。    需要在配置文件中使用这些值。

1. 在 CLI 终端设置目标数据库配置。 使用 **`vi conf/conn/cosmosdb.yml`** 命令打开配置文件，添加一个包含主机 URI 的逗号分隔的列表，并添加端口号、用户名、密码以及其他必需的参数。 下面是一个示例，介绍了配置文件中的内容：

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. 接下来，使用 Blitzz 迁移数据。 可以按“完全”或“快照”模式运行 Blizz 复制器：  

   * “完全模式” - 在此模式下，复制器会在迁移后继续运行，并侦听在源 Oracle 系统上发生的任何更改。 如果它检测到任何更改，则会将更改实时复制到目标 Azure Cosmos 帐户。

   * “快照模式“ - 在此模式下，可以执行架构迁移和一次性数据复制。 此选项不支持实时复制。


   可以通过上述两种模式进行迁移，不会造成停机。

1. 若要迁移数据，请在 Blitzz 复制器 CLI 终端运行以下命令：

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   复制器 UI 显示复制进度。 完成架构迁移和快照操作以后，进度会显示 100%。 完成迁移后，可在目标 Azure Cosmos 数据库上验证数据。

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png" alt-text="Oracle 数据迁移输出":::

1. 由于你已使用“完全”模式进行迁移，因此可以执行多项操作，例如在源 Oracle 数据库上插入、更新或删除数据。 稍后可以验证它们是否已在目标 Azure Cosmos 数据库上实时复制。 迁移后，请务必降低为 Azure Cosmos 容器配置的吞吐量。

1. 可以随时停止复制器，并使用 **--resume** 开关重启它。 复制会从停止的点继续，不会损害数据一致性。 以下命令演示如何使用 resume 开关。

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

若要详细了解如何以实时迁移方式将数据迁移到目标，请观看 [Blitzz 复制器演示](https://www.youtube.com/watch?v=y5ZeRK5A-MI)视频。

## <a name="next-steps"></a>后续步骤

* [在容器和数据库上预配吞吐量](../set-throughput.md)
* [分区键最佳做法](../partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB Capacity Planner 估算 RU/秒](../estimate-ru-with-capacity-planner.md)
