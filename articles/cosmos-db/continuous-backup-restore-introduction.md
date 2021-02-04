---
title: 带时间点还原功能的连续备份 Azure Cosmos DB
description: Azure Cosmos DB 的时间点还原功能有助于恢复意外写入、删除操作或将数据还原到任何区域。 了解定价和当前限制。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: 036f086c88267f6a20da51746ca875c48a248712
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538835"
---
# <a name="continuous-backup-with-point-in-time-restore-preview-feature-in-azure-cosmos-db"></a>带有时间点还原的持续备份 (预览) 功能 Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> 时间点还原功能 (持续备份模式) 用于 Azure Cosmos DB 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Cosmos DB 的时间点还原功能 (预览版) 有助于多种方案，例如：

* 从容器内的意外写入或删除操作恢复。
* 还原已删除的帐户、数据库或容器。
* 若要还原到任何区域 (在该位置，将在还原点) 备份。

Azure Cosmos DB 在后台执行数据备份，而不使用任何额外的预配吞吐量 (ru) 或影响数据库的性能和可用性。 在帐户所在的每个区域中进行连续备份。 下图显示了 "美国西部" 区域中的 "写入区域" 容器、"美国东部" 和 "美国东部 2" 中的 "读取区域" 备份到相应区域中的远程 Azure Blob 存储帐户。 默认情况下，每个区域将备份存储在本地冗余存储帐户中。 如果区域启用了 [可用性区域](high-availability.md#availability-zone-support) ，则将备份存储在 Zone-Redundant 的存储帐户中。

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Cosmos DB 数据备份到 Azure Blob 存储。" lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

"还原 (的可用时间窗口" 也称为 "保留期") 是以下两个值中的较小值： "30 天后过期" 或 "直到资源创建时间"。 还原的时间点可以是保持期内的任何时间戳。

在公共预览版中，你可以使用 [Azure 门户](continuous-backup-restore-portal.md)、 [Azure 命令行接口](continuous-backup-restore-command-line.md) (az CLI) 、 [AZURE POWERSHELL](continuous-backup-restore-powershell.md)或 [Azure 资源管理器](continuous-backup-restore-template.md)将 SQL API 或 MongoDB 内容点的 Azure Cosmos DB 帐户还原到另一个帐户。

## <a name="what-is-restored"></a>还原的内容

在稳定状态下，对源 (帐户执行的所有突变（包括数据库、容器和项) ）都将在100秒内异步备份。 如果备份介质 (Azure 存储) 关闭或不可用，则突变将在本地持久保存，直到有可用的介质，然后将其清除，以防止可还原的操作保真损失。

你可以选择还原预配吞吐量容器、共享吞吐量数据库或整个帐户的任意组合。 还原操作将所有数据及其索引属性还原到新帐户中。 还原过程可确保帐户、数据库或容器中还原的所有数据都能与指定的还原时间一致。 还原的持续时间取决于需要还原的数据量。

> [!NOTE]
> 使用连续备份模式时，将在 Azure Cosmos DB 帐户可用的每个区域中创建备份。 默认情况下，为每个区域帐户执行的备份都是本地冗余的，如果你的帐户已为该区域启用了 [可用性区域](high-availability.md#availability-zone-support) 功能，则为区域冗余。 还原操作始终将数据还原到新帐户。

## <a name="what-is-not-restored"></a>不还原哪些内容？

执行时点恢复后，不会还原以下配置：

* 防火墙、VNET、专用终结点设置。
* 一致性设置。 默认情况下，使用会话一致性还原帐户。  
* 区域.
* 存储过程、触发器和 Udf。

还原完成后，可以将这些配置添加到还原的帐户。

## <a name="restore-scenarios"></a>还原方案

下面是由时间点还原功能解决的一些关键方案。 方案 [a] 到 [c] 演示如果事先知道还原时间戳，如何触发还原。 但是，在某些情况下，你不知道意外删除或损坏的确切时间。 方案 [d] 和 [e] 演示如何使用可还原的数据库或容器上的新事件源 Api 来 _发现_ 还原时间戳。

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="可还原帐户的生命周期事件和时间戳。" lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **还原已删除的帐户** -可以从 " **还原** " 窗格查看所有可还原的已删除帐户。 例如，如果在时间戳 T3 上删除 "帐户 A"。 在这种情况下，刚好在 T3、位置、目标帐户名称、资源组和目标帐户名称之前的时间戳足以从 [Azure 门户](continuous-backup-restore-portal.md#restore-deleted-account)、 [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)或 [CLI](continuous-backup-restore-command-line.md#trigger-restore)进行还原。  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="可还原数据库和容器具有时间戳的生命周期事件。" lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. **还原特定区域中某个帐户的数据** -例如，如果 "帐户 a" 存在于 "美国东部" 和 "美国西部" 两个区域的时间戳 T3。 如果在 "美国西部" 中需要帐户 A 的副本，则可以使用 "美国西部" 作为目标位置，从 [Azure 门户](continuous-backup-restore-portal.md)、 [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)或 [CLI](continuous-backup-restore-command-line.md#trigger-restore) 执行时间点还原。

c. **使用已知的还原时间戳从容器内的意外写入或删除操作中恢复** （例如，如果 **知道** "数据库 1" 中的 "容器 1" 的内容在时间戳 T3 上被意外修改。 可以从 [Azure 门户](continuous-backup-restore-portal.md#restore-live-account)、 [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)或 [CLI](continuous-backup-restore-command-line.md#trigger-restore) 执行时间点还原，并将其转换为时间戳 T3 上的其他帐户，以恢复容器的所需状态。

d. 在 **意外删除数据库之前将帐户还原到以前的某个时间点**-在 [Azure 门户](continuous-backup-restore-portal.md#restore-live-account)中，可以使用 "事件源" 窗格确定数据库的删除时间，并找到还原时间。 同样，使用 [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) 和 [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)，可以通过枚举数据库事件源来发现数据库删除事件，然后使用所需的参数触发 restore 命令。

e. **在意外删除或修改容器属性之前，将帐户还原到以前的时间点。** -在 [Azure 门户](continuous-backup-restore-portal.md#restore-live-account)中，可以使用 "事件源" 窗格确定创建、修改或删除容器以查找还原时间。 同样，使用 [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) 和 [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)，可以通过枚举容器事件源来发现所有容器事件，然后使用所需的参数触发 restore 命令。

## <a name="permissions"></a>权限

Azure Cosmos DB 允许你隔离和限制对特定角色或主体的连续备份帐户的还原权限。 帐户所有者可以触发还原，并将角色分配给其他主体，以执行还原操作。 若要了解详细信息，请参阅 [权限](continuous-backup-restore-permissions.md) 一文。

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>定价

Azure Cosmos DB 启用了连续备份的帐户将每月额外的费用添加到 "存储备份" 和 "还原数据"。 每次启动还原操作时，都会添加还原成本。 如果使用连续备份配置帐户，但不还原数据，则只会在帐单中包含备份存储开销。

下面的示例基于在美国非政府地区部署的 Azure Cosmos 帐户的价格。 定价和计算可能因所使用的区域而异，有关最新定价信息，请参阅 [Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/) 。

* 使用连续备份策略启用的所有帐户都将对按以下方式计算的备份存储额外收取每月费用：

  $ 0.20/GB * 以 GB 为限的数据大小 * 区域数量

* 每个还原 API 调用都将产生一次费用。 费用是数据还原量的一个函数，计算方式如下：

  $ 0.15/GB * 数据大小（GB）。

例如，如果两个区域包含 1 TB 的数据，则：

* 备份存储成本按每月 (1000 * 0.20 * 2) = $400 计算

* 还原成本计算为 (1000 * 0.15) = $150 每个还原

## <a name="current-limitations-public-preview"></a> (公开预览版的当前限制) 

目前，时间点还原功能处于公共预览阶段，但有以下限制：

* 对于连续备份，只支持 SQL 和 MongoDB Azure Cosmos DB Api。 尚不支持 Cassandra、Table 和 Gremlin Api。

* 不能将具有默认定期备份策略的现有帐户转换为使用连续备份模式。

* Azure 主权和 Azure 政府版云区域尚不受支持。

* 不支持使用客户托管密钥的帐户使用连续备份。

* 不支持多区域写入帐户。

* 不支持已启用 Synapse 链接的帐户。

* 在源帐户所在的同一区域中创建已还原的帐户。 如果某个帐户不存在，则无法将该帐户还原到该区域。

* "还原" 窗口只是30天，无法更改。

* 备份不会自动抵御异地灾难。 必须显式添加另一个区域，以使帐户和备份具有复原能力。

* 当还原正在进行时，请勿修改或删除授予帐户权限或更改任何 VNET、防火墙配置的标识和访问管理 (IAM) 策略。

* 用于在创建容器后创建唯一索引的 SQL 或 MongoDB 帐户 Azure Cosmos DB API 不支持连续备份。 仅支持在初始容器创建过程中创建唯一索引的容器。 对于 MongoDB 帐户，可以使用 [扩展命令](mongodb-custom-commands.md)创建唯一索引。

* 时间点还原功能始终会还原到新的 Azure Cosmos 帐户。 当前不支持还原到现有帐户。 如果你有兴趣提供有关就地还原的反馈，请通过你的客户代表或 [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db)联系 Azure Cosmos DB 团队。

* `RestorableDatabaseAccount` `RestorableSqlDatabases` `RestorableSqlContainer` `RestorableMongodbDatabase` 当功能处于预览阶段时，为列出、、、而公开的所有新 api `RestorableMongodbCollection` 将受到更改。

* 还原后，对于某些集合，可能会重新生成一致的索引。 可以通过 [IndexTransformationProgress](how-to-manage-indexing-policy.md) 属性检查重新生成操作的状态。

* 还原过程将还原容器的所有属性，包括其 TTL 配置。 因此，如果你以这种方式配置，则可能会立即删除还原的数据。 为了防止出现这种情况，必须在将 TTL 属性添加到容器之前还原时间戳。

## <a name="next-steps"></a>后续步骤

* 使用 [Azure 门户](continuous-backup-restore-portal.md)、 [PowerShell](continuous-backup-restore-powershell.md)、 [CLI](continuous-backup-restore-command-line.md)或 [Azure 资源管理器](continuous-backup-restore-template.md)配置和管理连续备份。
* [管理](continuous-backup-restore-permissions.md) 以连续备份模式还原数据所需的权限。
* [连续备份模式的资源模型](continuous-backup-restore-resource-model.md)
