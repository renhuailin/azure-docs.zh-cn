---
title: 升级 Azure Cosmos DB API for MongoDB 帐户的 Mongo 版本
description: 如何无缝升级现有 Azure Cosmos DB API for MongoDB 帐户的 MongoDB 有线协议版本
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 01ff87419a96683af0c590f568a6cc745c55540d
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113359665"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>升级 Azure Cosmos DB API for MongoDB 帐户的 API 版本
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

本文介绍如何升级 Azure Cosmos DB API for MongoDB 帐户的 API 版本。 完成升级后，可以使用 Azure Cosmos DB API for MongoDB 的最新功能。 升级过程不会中断帐户的可用性，也不会在任何时候消耗 RU/s 或减少数据库的容量。 此过程不会影响现有数据或索引。 

升级到新 API 版本时，在升级生产工作负载之前，先从开发/测试工作负载开始。 在升级 Azure Cosmos DB API for MongoDB 之前，必须将客户端升级到与要升级到的 API 版本兼容的版本。

>[!Note]
> 目前，只有使用服务器版本 3.2 的符合条件的帐户可以升级到版本 3.6 或 4.0。 如果帐户未显示升级选项，请[提交支持工单](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="upgrading-to-40-or-36"></a>升级到 4.0 或 3.6 版

### <a name="benefits-of-upgrading-to-version-40"></a>升级到 4.0 版的优势

以下是版本 4.0 中包含的新功能：
- 在未分片集合内支持多文档事务。
- 新的聚合运算符
- 增强的扫描性能
- 更快更高效的存储

### <a name="benefits-of-upgrading-to-version-36"></a>升级到版本 3.6 的优点

以下是版本 3.6 中包含的新功能：
- 增强性能和稳定性
- 支持新的数据库命令
- 支持聚合管道（默认）和新的聚合阶段
- 支持更改源
- 支持复合索引
- 对以下操作的跨分区支持：更新、删除、计数和排序
- 提高了以下聚合操作的性能：$count、$skip、$limit 和 $group
- 现在支持通配符索引

### <a name="changes-from-version-32"></a>自版本 3.2 以来的更改

- [服务器端重试 (SSR)](prevent-rate-limiting-errors.md) 功能是默认启用的，因此来自客户端应用程序的请求不会返回 16500 错误。 请求会继续，直到它们完成或因达到 60 秒的限制而超时。
- 每个请求超时设置为 60 秒。
- 默认情况下，在新的有线协议版本上创建的 MongoDB 集合将仅索引 `_id` 属性。

### <a name="action-required-when-upgrading-from-32"></a>从 3.2 版升级时必需的操作

从 3.2 版升级时，数据库帐户终结点后缀会更新为以下格式：

```
<your_database_account_name>.mongo.cosmos.azure.com
```

如果是从 3.2 版升级，你需要替换应用程序以及驱动程序中连接此数据库帐户的现有终结点。 只有使用新终结点的连接才能访问新 API 版本中的功能。 上一个 3.2 终结点的后缀应为 `.documents.azure.com`。

>[!Note]
> 如果你的帐户是在主权云、政府云或受限制的 Azure 云中创建的，此终结点可能会略有不同。

## <a name="how-to-upgrade"></a>如何升级

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到你的 Azure Cosmos DB API for MongoDB 帐户。 打开“概述”窗格，验证当前的“服务器版本”是 3.2 还是 3.6。

    :::image type="content" source="./media/mongodb-version-upgrade/check-current-version.png" alt-text="从 Azure 门户查看 MongoDB 帐户的当前版本。" border="true":::

1. 从左侧菜单中打开“`Features`”窗格。 此窗格显示可用于数据库帐户的帐户级功能。

1. 选择 `Upgrade MongoDB server version` 行。 如果没有看到此选项，则表示你的帐户可能没有进行此升级的资格。 如果是这种情况，请提交[支持工单](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

    :::image type="content" source="./media/mongodb-version-upgrade/upgrade-server-version.png" alt-text="打开“功能”边栏选项卡并升级帐户。" border="true":::

1. 查看显示的有关升级的信息。 选择“`Set server version to 4.0`”（或 3.6，具体取决于当前版本）。

    :::image type="content" source="./media/mongodb-version-upgrade/select-upgrade.png" alt-text="查看升级指南并选择“升级”。" border="true":::

1. 开始升级后，“功能”菜单会灰显，并且状态会设置为“挂起”。 升级大约需要 15 分钟才能完成。 此过程不会影响数据库帐户的现有功能或操作。 完成后，“更新 MongoDB 服务器版本”状态会显示升级后的版本。 如果处理请求时出现问题，请[联系支持人员](https://azure.microsoft.com/en-us/support/create-ticket/)。

1. 升级帐户后，需注意以下事项：

    1. 如果已从 3.2 升级，请回到“概述”窗格，复制要在应用程序中使用的新连接字符串。 运行 3.2 版的旧连接字符串不会中断。 为确保获得一致的体验，所有应用程序都必须使用新的终结点。

    1. 如果从 3.6 升级，现有的连接字符串会被升级为指定的版本，并且应继续使用它。

## <a name="how-to-downgrade"></a>如何降级

你还可以通过“如何升级”部分中的相同步骤将帐户从 4.0 降级到 3.6。

如果从 3.2 升级到 4.0 或 3.6，并且希望降级回 3.2，可以直接切换回为主机 `accountname.documents.azure.com` 使用以前的 (3.2) 连接字符串，该字符串在升级后运行 3.2 版时仍然可用。

## <a name="next-steps"></a>后续步骤

- 了解 [MongoDB 4.0 版支持和不支持的功能](mongodb-feature-support-40.md)。
- 了解 [MongoDB 版本 3.6 支持和不支持的功能](mongodb-feature-support-36.md)。
- 有关详细信息，请查看 [Mongo 3.6 版本功能](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
