---
title: 教程：将 MongoDB 脱机迁移到用于 MongoDB 的 Azure Cosmos DB API
titleSuffix: Azure Database Migration Service
description: 通过使用 Azure 数据库迁移服务从本地 MongoDB 脱机迁移到适用于 MongoDB 的 Azure Cosmos DB API。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 09/21/2021
ms.openlocfilehash: 6714446d42a256f6948b8e0f6e69b6bc2be60b6e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664345"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>教程：将 MongoDB 脱机迁移到适用于 MongoDB 的 Azure Cosmos DB API
[!INCLUDE[appliesto-mongodb-api](../cosmos-db/includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> 在执行迁移步骤之前，请完整阅读本指南。
>

本 MongoDB 迁移指南是 MongoDB 迁移系列的一部分。 关键的 MongoDB 迁移步骤包括[迁移前步骤](../cosmos-db/mongodb-pre-migration.md)、迁移步骤和[迁移后步骤](../cosmos-db/mongodb-post-migration.md)，如下所示。

![迁移步骤示意图。](../cosmos-db/mongodb/media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-offline-data-migration-from-mongodb-to-azure-cosmos-db-using-dms"></a>使用 DMS 以脱机方式将数据从 MongoDB 迁移到 Azure Cosmos DB 概述

使用 Azure 数据库迁移服务将数据库从 MongoDB 的本地或云实例脱机（一次性）迁移到适用于 MongoDB 的 Azure Cosmos DB API。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
>
> * 创建 Azure 数据库迁移服务的实例。
> * 使用 Azure 数据库迁移服务创建迁移项目。
> * 运行迁移。
> * 监视迁移。

在本教程中，你将迁移在 Azure 虚拟机中托管的 MongoDB 中的数据集。 通过使用 Azure 数据库迁移服务，你可以将数据集迁移到适用于 MongoDB 的 Azure Cosmos DB API。 如果尚未设置 MongoDB 源，请参阅[在 Azure 中的 Windows VM 上安装并配置 MongoDB](/previous-versions/azure/virtual-machines/windows/install-mongodb)。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* [完成预迁移](../cosmos-db/mongodb-pre-migration.md)步骤，如估计吞吐量和选择分区键。
* [为 MongoDB 的 Azure Cosmos DB API 创建帐户](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)。

  > [!NOTE]
  > 如果要迁移到用无服务器模式预配的 MongoDB 帐户的 API，目前不支持 DMS。

* 使用 Azure 资源管理器部署模型，为 Azure 数据库迁移服务创建 Microsoft Azure 虚拟网络。 此部署模型通过使用 [Azure ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 提供与本地源服务器的站点到站点连接。 有关创建虚拟网络的详细信息，请参阅 [Azure 虚拟网络文档](../virtual-network/index.yml)，尤其是提供分步详细信息的“快速入门”文章。

    > [!NOTE]
    > 在虚拟网络设置期间，如果将 ExpressRoute 与 Microsoft 的网络对等互连一起使用，则请将以下服务[终结点](../virtual-network/virtual-network-service-endpoints-overview.md)添加到要在其中预配该服务的子网：
    >
    > * 目标数据库终结点（例如，SQL 终结点或 Azure Cosmos DB 终结点）
    > * 存储终结点
    > * 服务总线终结点
    >
    > Azure 数据库迁移服务缺少 Internet 连接，因此必须提供此配置。

* 确保虚拟网络的网络安全组 (NSG) 规则不阻止以下通信端口：53、443、445、9354 和 10000-20000。 有关详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/virtual-network-vnet-plan-design-arm.md)。
* 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 MongoDB 服务器（默认情况下为 TCP 端口 27017）。
* 如果在源数据库的前面使用防火墙设备时，则可能需要添加防火墙规则，以允许 Azure 数据库迁移服务访问要迁移的源数据库。

## <a name="configure-the-server-side-retry-feature"></a>配置服务器端重试功能

如果从 MongoDB 迁移到 Azure Cosmos DB，则资源调控功能将非常有用。 利用这些功能，你可以充分利用预配吞吐量的请求单位（RU/秒）。 如果该请求超出了容器预配的请求单位，则 Azure Cosmos DB 可能会在迁移过程中限制该请求。 然后，需要重试该请求。

数据库迁移服务能够执行重试。 数据库迁移服务与 Azure Cosmos DB 之间的网络跃点所涉及的往返时间会影响该请求的整体响应时间，了解这一点很重要。 缩短受限请求的响应时间可缩短迁移所需的总时间。

借助 Azure Cosmos DB 的服务器端重试功能，服务可截获限制错误代码，并以更短的往返时间重试，从而大幅缩短请求响应时间。

若要使用服务器端重试，请在 Azure Cosmos DB 门户中选择“功能” > “服务器端重试”。

![显示服务器端重试功能位置的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

如果该功能被禁用，请选择“启用”。

![显示如何启用服务器重试功能的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]  

## <a name="create-an-instance"></a>创建实例

1. 在 Azure 门户中，选择 **+ 创建资源**，搜索 Azure 数据库迁移服务，然后从下拉列表选择 **Azure 数据库迁移服务**。

    ![显示 Azure 市场的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建”   。

    ![显示如何创建 Azure 数据库迁移服务实例的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. 在“创建迁移服务”上，为服务、订阅以及新的或现有资源组指定名称。

4. 选择要在其中创建 Azure 数据库迁移服务实例的位置。 

5. 选择现有虚拟网络或新建一个。

    虚拟网络为 Azure 数据库迁移服务提供了对源 MongoDB 实例和目标 Azure Cosmos DB 帐户的访问权限。

    如需详细了解如何在 Azure 门户中创建虚拟网络，请参阅[使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)。

6. 选择定价层。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

    ![显示 Azure 数据库迁移服务实例的配置设置的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. 选择“创建”  来创建服务。

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务后，请在 Azure 门户中找到并打开它。 然后创建新的迁移项目。

1. 在 Azure 门户中，选择“所有服务”，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”。

      ![显示如何查找所有 Azure 数据库迁移服务实例的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. 在“Azure 数据库迁移服务”屏幕上，搜索你创建的 Azure 数据库迁移服务实例名称，然后选择该实例。

3. 选择“+ 新建迁移项目”。

4. 在“新建迁移项目”上指定项目的名称，并在“源服务器类型”文本框中选择“MongoDB”  。 在“目标服务器类型”文本框中选择“CosmosDB (MongoDB API)”，然后在“选择活动类型”中选择“脱机数据迁移”。    

    ![显示项目选项的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. 选择“创建并运行活动”，以便创建项目并运行迁移活动。 

## <a name="specify-source-details"></a>指定源详细信息

1. 在“源详细信息”  屏幕上，指定源 MongoDB 服务器的连接详细信息。

   > [!IMPORTANT]
   > Azure 数据库迁移服务不支持将 Azure Cosmos DB 作为源。

    可通过三种模式连接到源：
   * **标准模式**：接受完全限定的域名或 IP 地址、端口号和连接凭据。
   * **连接字符串模式**：接受 [连接字符串 URI 格式](https://docs.mongodb.com/manual/reference/connection-string/)中所述的 MongoDB 连接字符串。
   * **Azure 存储中的数据**：接受 Blob 容器 SAS URL。 如果 Blob 容器包含 MongoDB [bsondump 工具](https://docs.mongodb.com/manual/reference/program/bsondump/)生成的 BSON 转储，请选择“Blob 包含 BSON 转储”。 如果容器包含 JSON 文件，请勿选择该选项。

     如果选择此选项，则请确保存储帐户连接字符串按以下格式显示：

     ```
     https://blobnameurl/container?SASKEY
     ```

     可以在 Azure 存储资源管理器中找到此 blob 容器 SAS 连接字符串。 为相关容器创建 SAS 将为你提供所请求格式的 URL。
     
     此外，根据 Azure 存储中的类型转储信息，记住以下信息：

     * 对于 BSON 转储，blob 容器中的数据必须采用 bsondump 格式。 将数据文件放置到格式为 collection.bson 且以包含数据库命名的文件夹中。 使用 collection.metadata.json 格式命名任何元数据文件。

     * 对于 JSON 转储，blob 容器中的文件必须放置到以包含数据库命名的文件夹中。 在每个数据库文件夹中，数据文件必须放置到名为“data”且使用格式 collection.json 命名的子文件夹中 。 将元数据文件放置到名为“metadata”且使用相同格式 collection.json 命名的子文件夹中 。 元数据文件必须采用由 MongoDB bsondump 工具所生成的相同格式。

    > [!IMPORTANT]
    > 我们不建议在 MongoDB 服务器上使用自签名证书。 如果必须使用自签名证书，请使用连接字符串模式连接到服务器，并确保连接字符串有引号 ("")。
    >
    >```
    >&sslVerifyCertificate=false
    >```

   在无法使用 DNS 名称解析的情况下，还可使用 IP 地址。

   ![显示指定源详细信息的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. 选择“保存” 。

## <a name="specify-target-details"></a>指定目标详细信息

1. 在“迁移目标详细信息”屏幕上，指定目标 Azure Cosmos DB 帐户的连接详细信息。 此帐户是适用于 MongoDB 的 Azure Cosmos DB API 的预配置的帐户（你要将 MongoDB 数据迁移到该帐户）。

    ![显示指定目标详细信息的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. 选择“保存”  。

## <a name="map-to-target-databases"></a>映射到目标数据库

1. 在“映射到目标数据库”  屏幕上，映射源和目标数据库以进行迁移。

    如果目标数据库包含的数据库名称与源数据库的相同，则 Azure 数据库迁移服务默认会选择目标数据库。

    如果“创建”显示在数据库名称旁边，则表明 Azure 数据库迁移服务没有找到目标数据库，因此会为你创建该数据库。

    在迁移的这个时候，可以[预配吞吐量](../cosmos-db/set-throughput.md)。 在 Azure Cosmos DB 中，可在数据库级别预配吞吐量，也可为每个集合单独进行预配。 吞吐量以[请求单位](../cosmos-db/request-units.md)度量。 深入了解 [Azure Cosmos DB 定价](https://azure.microsoft.com/pricing/details/cosmos-db/)。

    ![显示映射到目标数据库的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. 选择“保存”  。
3. 在“集合设置”屏幕上，展开集合列表，然后查看要迁移的集合的列表。 

    Azure 数据库迁移服务会自动选择存在于源 MongoDB 实例上但不存在于目标 Azure Cosmos DB 帐户上的所有集合。 若要重新迁移已含数据的集合，需要在此窗格上明确选择集合。

    可以指定希望集合使用的 RU 数量。 Azure 数据库迁移服务建议使用根据集合大小设置的智能默认值。

    > [!NOTE]
    > 并行执行数据库迁移和收集。 如果有必要，可以使用 Azure 数据库迁移服务的多个实例加快运行速度。

    也可指定分片键来利用 [Azure Cosmos DB 中的分区](../cosmos-db/partitioning-overview.md)，以便优化可伸缩性。 请查看[选择分片/分区键的最佳做法](../cosmos-db/partitioning-overview.md#choose-partitionkey)。

    ![显示选择集合表的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. 选择“保存”  。

5. 在“迁移摘要”屏幕的“活动名称”文本框中指定迁移活动的名称。  

    ![显示 nigration 摘要的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>运行迁移

选择“运行迁移”。 迁移活动窗口随即出现，活动状态为“未启动”。

![显示活动状态的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>监视迁移

在迁移活动屏幕上选择“刷新”，以更新显示，直到迁移的状态显示为“已完成” 。

> [!NOTE]
> 可以选择“活动”，获取数据库级别和集合级别迁移指标的详细信息。

![显示活动状态为“已完成”的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>验证 Azure Cosmos DB 中的数据

完成迁移后，可以检查你的 Azure Cosmos DB 帐户，验证是否已成功迁移所有集合。

![显示在何处检查 Azure Cosmos DB 帐户，以验证所有集合是否已成功迁移的屏幕截图。](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>迁移后优化

将 MongoDB 数据库中存储的数据迁移到 MongoDB的 Azure Cosmos DB API 后，你可以连接到 Azure Cosmos DB 并管理数据。 还可以执行其他迁移后的优化步骤。 其中可能包括优化索引策略、更新默认一致性级别或者为 Azure Cosmos DB 帐户配置全局分发。 有关详细信息，请参阅[迁移后的优化](../cosmos-db/mongodb-post-migration.md)。

## <a name="additional-resources"></a>其他资源

* 尝试为迁移到 Azure Cosmos DB 进行容量规划？
    * 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../cosmos-db/convert-vcore-to-request-unit.md) 
    * 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](../cosmos-db/mongodb/estimate-ru-capacity-planner.md)

## <a name="next-steps"></a>后续步骤

在 [Azure 数据库迁移指南](https://datamigration.microsoft.com/)中查看用于其他场景的迁移指南。



