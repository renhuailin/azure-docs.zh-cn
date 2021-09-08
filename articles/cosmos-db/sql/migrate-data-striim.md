---
title: 使用 Striim 将数据迁移到 Azure Cosmos DB SQL API 帐户
description: 了解如何使用 Striim 将数据从 Oracle 数据库迁移到 Azure Cosmos DB SQL API 帐户。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 1826aea977798de29de6f3968b50b9a4578eff69
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113412"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>使用 Striim 将数据迁移到 Azure Cosmos DB SQL API 帐户
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]
 
通过 Azure 商城中的 Striim 映像，可以从数据仓库和数据库向 Azure 持续实时移动数据。 在移动数据时，可以执行内联的非规范化、数据转换，启用实时分析，以及数据报告方案。 可以轻松地开始使用 Striim 将企业数据持续移动到 Azure Cosmos DB SQL API。 Azure 提供一种商城产品，可用于轻松部署 Striim 并将数据迁移到 Azure Cosmos DB。 

本文介绍如何使用 Striim 将数据从 Oracle 数据库迁移到 Azure Cosmos DB SQL API 帐户。

## <a name="prerequisites"></a>先决条件

* 如果还没有 [Azure 订阅](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 在本地运行的 Oracle 数据库，其中包含一些数据。

## <a name="deploy-the-striim-marketplace-solution"></a>部署 Striim 商城解决方案

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择“创建资源”，并在 Azure 商城中搜索“Striim” 。 选择第一个选项，然后选择“创建”。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="查找 Striim 商城项":::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在“基本信息”窗格中，输入“VM 用户名”、“VM 密码”（此密码用于通过 SSH 连接到 VM）  。 选择要在其中部署 Striim 的“订阅”、“资源组”和“位置详细信息”  。 在完成后，选择“确定”。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="配置 Striim 的基本设置":::

1. 在“Striim 群集设置”窗格中，选择 Striim 部署的类型和虚拟机大小。

   |设置 | 值 | 说明 |
   | ---| ---| ---|
   |Striim 部署类型 |独立 | Striim 可以在“独立”或“群集”部署类型中运行 。 独立模式将在一个虚拟机上部署 Striim 服务器，并且可以根据数据量来选择 VM 的大小。 群集模式将在具有所选大小的两个或多个 VM 上部署 Striim 服务器。 具有 2 个以上节点的群集环境提供自动高可用性和故障转移。</br></br> 在本教程中，可以选择“独立”选项。 使用默认的“Standard_F4s”大小 VM。  | 
   | Striim 群集的名称|    <Striim_cluster_Name>|  Striim 群集的名称。|
   | Striim 群集密码|   <Striim_cluster_password>|  群集的密码。|

   在填写表单后，请选择“确定”以继续。

1. 在“Striim 访问设置”窗格中，配置要用于登录到 Striim UI 的“公共 IP 地址”（选择默认值）、“Striim 的域名”、“管理员密码”   。 配置 VNET 和子网（选择默认值）。 在填写详细信息后，选择“确定”以继续。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Striim 访问设置":::

1. Azure 将会验证部署并确保一切正常；完成验证需要几分钟时间。 在完成验证后，选择“确定”。
  
1. 最后，查看使用条款并选择“创建”来创建 Striim 实例。 

## <a name="configure-the-source-database"></a>配置源数据库 

在本部分中，将配置 Oracle 数据库作为源来进行数据移动。  需要使用 [Oracle JDBC 驱动程序](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html)来连接到 Oracle。 要从源 Oracle 数据库读取更改，可以使用 [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) 或 [XStream API](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)。 要在 Oracle 数据库中读取、写入或持续保存数据，Striim 的 Java 类路径中必须存在 Oracle JDBC 驱动程序。

将 [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) 驱动程序下载到本地计算机。 稍后会将它安装在 Striim 群集中。

## <a name="configure-the-target-database"></a>配置目标数据库

在本部分中，将配置 Azure Cosmos DB SQL API 帐户作为目标来进行数据移动。

1. 使用 Azure 门户创建一个 [Azure Cosmos DB SQL API 帐户](create-cosmosdb-resources-portal.md)。

1. 在 Azure Cosmos 帐户中导航到“数据资源管理器”窗格。 选择“新建容器”来新建一个容器。 假定要将“产品”和“订单”数据从 Oracle 数据库迁移到 Azure Cosmos DB 。 使用名为“订单”的容器新建一个名为“StriimDemo”的数据库 。 将该容器预配为使用 1000 个 RU（此示例使用 1000 个 RU，但你应该使用针对自己工作负载估算的吞吐量），并预配 /ORDER_ID 作为分区键 。 这些值将会因源数据而异。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png" alt-text="创建 SQL API 帐户":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>配置 Oracle 到 Azure Cosmos DB 的数据流

1. 现在，我们回到 Striim。 在与 Striim 交互之前，请先安装前面下载的 Oracle JDBC 驱动程序。

1. 导航到在 Azure 门户中部署的 Striim 实例。 选择上部菜单栏中的“连接”按钮，然后从“SSH”选项卡中，复制“使用 VM 本地帐户登录”字段中的 URL  。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="获取 SSH URL":::

1. 打开一个新终端窗口，并运行从 Azure 门户复制的 SSH 命令。 本文使用 MacOS 中的终端，你可以在 Windows 计算机上使用 PuTTY 或其他 SSH 客户端，遵循类似的说明操作。 在出现提示时，键入“yes”以继续，并输入在上一步中为虚拟机设置的密码 。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="连接到 Striim VM":::

1. 现在，请打开一个新终端选项卡，复制先前下载的 ojdbc8.jar 文件。 使用以下 SCP 命令将该 jar 文件从本地计算机复制到 Azure 中运行的 Striim 实例的 tmp 文件夹：

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="将 Jar 文件从本地计算机复制到 Striim":::

1. 接下来，返回到已在其中通过 SSH 连接到 Striim 实例的窗口，并以 sudo 身份登录。 使用以下命令将 ojdbc8.jar 文件从 /tmp 目录移到 Striim 实例的 lib 目录  ：

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="将 Jar 文件移动到 lib 文件夹":::


1. 从同一终端窗口中，通过执行以下命令来重启 Striim 服务器：

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim 将用一分钟时间来启动。 如果想查看状态，请运行以下命令： 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. 现在，返回到 Azure 并复制 Striim VM 的公共 IP 地址。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="复制 Striim VM IP 地址":::

1. 若要导航到 Striim 的 Web UI，请在浏览器中打开新标签页，然后复制公共 IP，后面加上：9080。 使用用户名“admin”和在 Azure 门户中指定的管理员密码进行登录。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="登录到 Striim":::

1. 现在，将打开 Striim 的主页。 主页有三个不同的窗格-“仪表板”、“应用”和“SourcePreview”  。 “仪表板”窗格用于实时移动数据并将数据可视化。 “应用”窗格包含流式处理数据的管道，也叫数据流。 页面右侧是“SourcePreview”，用于在移动数据之前先预览数据。

1. 请选择“应用”窗格，目前我们将重点介绍此窗格。 这里有多种示例应用，可用于了解 Striim，但在本文中将创建自己的应用。 选择右上角的“添加应用”按钮。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="添加 Striim 应用":::

1. 创建 Striim 应用程序有几种不同方法。 选择“从模板开始”将使用现有模板开始创建。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png" alt-text="使用模板开始创建应用":::

1. 在“搜索模板”字段中，键入“Cosmos”并选择“目标：Azure Cosmos DB”，然后选择“Oracle CDC 到 Azure Cosmos DB”。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png" alt-text="选择“Oracle CDC 到 Cosmos DB”":::

1. 在下一页中，为应用程序命名。 可以提供 oraToCosmosDB 等名称，然后选择“保存”。

1. 接下来，输入源 Oracle 实例的源配置。 为“源名称”输入一个值。 源名称只是 Striim 应用程序的命名约定，可以使用 src_onPremOracle 等名称。 为其余源参数（包括“URL”、“用户名”、“密码”）输入值  ，然后选择“LogMiner”作为读取器从 Oracle 读取数据。 选择“下一步”继续操作。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png" alt-text="配置源参数":::

1. Striim 将检查环境，确保可以连接源 Oracle 实例、拥有正确的权限，并且该 CDC 配置正确。 验证所有值后，选择“下一步”。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png" alt-text="验证源参数":::

1. 从要迁移的 Oracle 数据库中选择表。 例如，选择 Orders 表，然后选择“下一步”。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png" alt-text="选择源表":::

1. 选择源表后，可以执行映射和筛选等更复杂的操作。 在这种情况下，只需在 Azure Cosmos DB 中创建源表的副本。 因此，选择“下一步”以配置目标

1. 现在，让我们来配置目标：

   * 目标名称 - 为目标提供一个友好名称。 
   * 输入源 - 从下拉列表中，选择在源 Oracle 配置中创建的源输入流。 
   * 集合 - 输入目标 Azure Cosmos DB 配置属性。 集合语法为 SourceSchema.SourceTable, TargetDatabase.TargetContainer。 在本例中，值为“SYSTEM.ORDERS, StriimDemo.Orders”。 
   * AccessKey - Azure Cosmos 帐户的 PrimaryKey。
   * ServiceEndpoint – Azure Cosmos 帐户的 URI，位于 Azure 门户的“关键值”部分。 

   依次选择“保存”和“下一步” 。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png" alt-text="配置目标参数":::


1. 接下来，将打开流设计器，从中可以直接拖放连接器来创建流式处理应用程序。 此时无需对流进行任何修改。 接下来，通过选择“部署应用”按钮来部署应用程序。
 
   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png" alt-text="部署应用":::

1. 在部署窗口中，可以指定是否要在部署拓扑的特定部分上运行应用程序的某些部分。 由于我们通过 Azure 在简单部署拓扑中运行，因此我们将使用默认选项。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png" alt-text="使用默认选项":::

1. 在部署后，可以预览该流，以查看流过的数据。 选择波形图标和它旁边的眼球图标。 选择顶部菜单栏中的“已部署”按钮，然后选择“启动应用” 。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-app.png" alt-text="启动应用":::

1. 通过使用 CDC（变更数据捕获）读取器，Striim 将只会选取数据库的新更改。 如果有数据流过源表，就会看到这些数据。 但由于这是演示表，源没有连接到任何应用程序。 如果使用示例数据生成器，可将事件链插入 Oracle 数据库。

1. 你将会看到数据流过 Striim 平台。 Striim 还会选取所有与该表关联的元数据，它们对于监视数据和确保数据登陆正确目标非常有用。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png" alt-text="配置 CDC 管道":::

1. 最后，让我们登录 Azure 并导航到你的 Azure Cosmos 帐户。 刷新数据资源管理器，就可以看到数据已经到达。  

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png" alt-text="在 Azure 中验证迁移的数据":::

通过使用 Azure 中的 Striim 解决方案，可以持续从各种不同的源（例如 Oracle、Cassandra、MongoDB 等）向 Azure Cosmos DB 迁移数据。 有关详细信息，请访问 [Striim 网站](https://www.striim.com/)，[下载 Striim 30 天免费试用版](https://go2.striim.com/download-free-trial)；如果在使用 Striim 设置迁移路径时遇到任何问题，请提交[支持请求](https://go2.striim.com/request-support-striim)。

## <a name="next-steps"></a>后续步骤

* 尝试为迁移到 Azure Cosmos DB 进行容量计划？
    * 如果只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
    * 如果知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)

* 如果要将数据迁移到 Azure Cosmos DB SQL API，请参阅[如何使用 Striim 将数据迁移到 Cassandra API 帐户](../cassandra/migrate-data-striim.md)

* [使用 Azure Cosmos DB 指标监视和调试数据](../use-metrics.md)