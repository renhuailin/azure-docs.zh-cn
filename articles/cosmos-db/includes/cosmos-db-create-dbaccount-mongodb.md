---
title: 包含文件
description: include 文件
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: 06bbeb2cfe39cdb29bc6182d849d000734c01346
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113279765"
---
1. 在新浏览器窗口中，登录到 [Azure 门户](https://portal.azure.com/)。

2. 在左侧菜单中，选择“创建资源”。
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png" alt-text="在 Azure 门户中创建资源":::
   
3. 在“新建”页上，选择“数据库” > “Azure Cosmos DB”。  
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png" alt-text="Azure 门户“数据库”窗格":::
   
3. 在“创建 Azure Cosmos DB 帐户”页中，输入新 Azure Cosmos DB 帐户的设置。 

   |设置|值|说明 |
   |---|---|---|
   |订阅|订阅名称|选择要用于此 Azure Cosmos 帐户的 Azure 订阅。 |
   |资源组|资源组名称|选择一个资源组，或者选择“新建”，然后输入新资源组的唯一名称。 |
   |帐户名|输入唯一的名称|输入标识此 Azure Cosmos DB 帐户的唯一名称。 帐户 URI 将是追加到唯一帐户名称的 *mongo.cosmos.azure.com*。<br><br>帐户名称只能使用小写字母、数字及连字符 (-)，必须为 3 到 44 个字符长。|
   API|Azure Cosmos DB for Mongo DB API|API 确定要创建的帐户的类型。 Azure Cosmos DB 提供五种 API：适用于文档数据库的 Core (SQL)、适用于图形数据库的 Gremlin、适用于文档数据库的 Azure Cosmos DB for Mongo DB API、Azure 表和 Cassandra。 目前，你必须为每种 API 创建单独的帐户。 <br><br>选择“Azure Cosmos DB for Mongo DB API”，因为本快速入门将创建使用 MongoDB 的集合  。<br><br>[了解有关 Azure Cosmos DB for MongoDB API 的详细信息](../mongodb-introduction.md)。|
   |位置|离用户最近的区域|选择用于托管 Azure Cosmos DB 帐户的地理位置。 使用离用户最近的位置，使他们能够以最快的速度访问数据。|
   |容量模式|预配吞吐量或无服务器|选择“预配吞吐量”以在[预配吞吐量](../set-throughput.md)模式下创建帐户。 选择“无服务器”以在[无服务器](../serverless.md)模式下创建帐户。 .<br><br>**注意**：无服务器帐户仅支持 MongoDB API 版本 3.6。 选择版本 3.2 将强制帐户处于配置的吞吐量模式。|
   |应用 Azure Cosmos DB 免费层折扣|“应用”或“不应用” |使用 Azure Cosmos DB 免费层，你将在帐户中获得每秒前 1000 RU 的免费吞吐量和 25 GB 的免费存储。 了解[免费层](https://azure.microsoft.com/pricing/details/cosmos-db/)的详细信息。|
   | 版本 | 选择所需的服务器版本 | Azure Cosmos DB API for MongoDB 与 Server 版本 4.0、3.6 和 3.2 兼容。 创建帐户后，可以对其进行[升级或降级](../mongodb-version-upgrade.md)。 |

   > [!NOTE]
   > 每个 Azure 订阅最多可以有一个免费层 Azure Cosmos DB 帐户，并且你必须在创建帐户时选择加入使用。 如果看不到用于应用免费层折扣的选项，这意味着订阅中的另一个帐户已启用免费层。

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png" alt-text="Azure Cosmos DB 的“新建帐户”页面"::: 

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
   * **备份策略**- 配置 [定期](../configure-periodic-backup-restore.md)或 [连续](../continuous-backup-restore-portal.md)备份策略。
   * **加密** - 使用服务管理的密钥或 [客户管理的密钥](../how-to-setup-cmk.md#create-a-new-azure-cosmos-account)。
   * **标记** - 标记是名称/值对，通过将相同的标记应用到多个资源和资源组，可以对资源进行分类并查看合并的账单。

1. 选择“查看 + 创建”。

4. 创建帐户需要几分钟时间。 等待门户中显示“祝贺你!  Azure Cosmos DB for MongoDB 帐户已准备就绪”页。

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png" alt-text="Azure 门户“通知”窗格"::: 