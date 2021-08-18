---
title: include 文件
description: include 文件
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 07/02/2021
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 8f9bf2d231e09c5d2aa636ab8cf03cef42bc58d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733246"
---
1. 在 Azure 门户菜单或主页中，选择“创建资源” 。

1. 在“新建”页面中搜索“Azure Cosmos DB”，然后选择它。 

1. 在“Azure Cosmos DB”页上，选择“创建”。

1. 在“创建 Azure Cosmos DB 帐户”页中，输入新 Azure Cosmos 帐户的基本设置。

   |设置|值|说明 |
   |---|---|---|
   | 订阅|你的订阅|选择要用于此 Azure Cosmos DB 帐户的 Azure 订阅。 |
   | 资源组|新建<br><br>然后，输入与帐户名称相同的名称。|选择“新建”。  然后输入帐户的新资源组名称。 为简单起见，请使用与 Azure Cosmos 帐户名称相同的名称。 |
   | 帐户名|输入唯一的名称|输入标识此 Azure Cosmos DB 帐户的唯一名称。 帐户 URI 将是追加到唯一帐户名称的“cassandra.cosmos.azure.com”  。<br><br>帐户名称只能使用小写字母、数字及连字符 (-)，必须为 3 到 31 个字符长。|
   | API|Cassandra|API 确定要创建的帐户的类型。 Azure Cosmos DB 提供五种 API：Core(SQL)（适用于文档数据库）、Gremlin（适用于图数据库）、MongoDB（适用于文档数据库）、Azure 表和 Cassandra。 必须为每种 API 创建单独的帐户。 <br><br>选择“Cassandra”  ，因为本快速入门将创建使用 Cassandra API 的表。 <br><br>[详细了解 Cassandra API](../cassandra/cassandra-introduction.md)。|
   |位置|离用户最近的区域|选择用于托管 Azure Cosmos DB 帐户的地理位置。 使用离用户最近的位置，使他们能够以最快的速度访问数据。|
   |容量模式|预配吞吐量或无服务器|选择“预配吞吐量”以在[预配吞吐量](../set-throughput.md)模式下创建帐户。 选择“无服务器”以在[无服务器](../serverless.md)模式下创建帐户。|
   |应用 Azure Cosmos DB 免费层折扣|“应用”或“不应用” |使用 Azure Cosmos DB 免费层，你将在帐户中获得每秒前 1000 RU 的免费吞吐量和 25 GB 的免费存储。 了解[免费层](https://azure.microsoft.com/pricing/details/cosmos-db/)的详细信息。|

   > [!NOTE]
   > 每个 Azure 订阅最多可以有一个免费层 Azure Cosmos DB 帐户，并且你必须在创建帐户时选择加入使用。 如果看不到用于应用免费层折扣的选项，这意味着订阅中的另一个帐户已启用免费层。

   :::image type="content" source="./media/cosmos-db-create-dbaccount-cassandra/azure-cosmos-db-create-new-account.png" alt-text="Azure Cosmos DB Cassandra API 的“新建帐户”页面":::

1. 在“全局分发”选项卡中，配置以下详细信息。 对于本快速入门，可以保留默认值：

   |设置|值|说明 |
   |---|---|---|
   |异地冗余|禁用|通过将你的区域与另一区域进行配对来启用或禁用帐户的全局分发。 稍后可以将更多区域添加到帐户。|
   |多区域写入|禁用|借助多区域写入功能，可以利用全球数据库和容器的预配吞吐量。|

   > [!NOTE]
   > 如果选择“无服务器”作为“容量模式”，则以下选项不可用 ：
   > - 应用免费层折扣
   > - 异地冗余
   > - 多区域写入

1. （可选）可以在以下选项卡中配置其他详细信息：

   * **网络** - 配置 [来自虚拟网络的访问](../how-to-configure-vnet-service-endpoint.md)。
   * **备份策略**- 配置 [定期](../configure-periodic-backup-restore.md)或 [连续](../provision-account-continuous-backup.md)备份策略。
   * **加密** - 使用服务管理的密钥或 [客户管理的密钥](../how-to-setup-cmk.md#create-a-new-azure-cosmos-account)。
   * **标记** - 标记是名称/值对，通过将相同的标记应用到多个资源和资源组，可以对资源进行分类并查看合并的账单。

1. 选择“查看 + 创建”。

1. 检查帐户设置，然后选择“创建”。 创建帐户需要几分钟时间。 等待门户页显示“你的部署已完成”消息。

   :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png" alt-text="Azure 门户“通知”窗格":::

1. 选择“转到资源”，转到 Azure Cosmos DB 帐户页。 
