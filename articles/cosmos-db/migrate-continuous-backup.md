---
title: 将 Azure Cosmos DB 帐户从周期备份模式迁移到连续备份模式
description: Azure Cosmos DB 当前支持从周期模式单向迁移到连续模式，此操作不可逆。 从周期模式迁移到连续模式后，可以利用连续模式的优势。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/17/2021
ms.author: sngun
ms.topic: how-to
ms.reviewer: sngun
ms.openlocfilehash: 286caa42fd095a5a273c164d860154e88bc38748
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397389"
---
# <a name="migrate-an-azure-cosmos-db-account-from-periodic-to-continuous-backup-mode"></a>将 Azure Cosmos DB 帐户从周期备份模式迁移到连续备份模式
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

可以使用 [Azure 门户](#portal)、[CLI](#cli)、[PowerShell](#powershell) 或 [资源管理器模板](#ARM-template)，将采用周期模式备份策略的 Azure CosmosDB 帐户迁移到连续模式。 从周期模式迁移到连续模式是单向迁移，此操作不可逆。 从周期模式迁移到连续模式后，可以利用连续模式的优势。

下面是迁移到连续模式的主要原因：

* 能够使用 Azure 门户、CLI 或 PowerShell 执行自助服务还原。
* 能够对过去 30 天的时间窗口以秒时间粒度还原。
* 能够确保备份在一段时间内在分片或分区键范围内保持一致。
* 能够在删除或修改了容器、数据库或整个帐户时将其还原。
* 能够选择容器、数据库或帐户上的事件，并能够决定何时启动还原。

> [!NOTE]
> 迁移功能仅限单向，它是不可逆操作。 这意味着，一旦从周期性模式迁移到连续模式，就无法切换回周期模式。
>
> 仅当满足以下条件时，才能将帐户迁移到连续备份模式：
>
> * 如果帐户的类型为 SQL API 或是 API for MongoDB。
> * 如果帐户有单个写入区域。
> * 如果帐户未启用客户托管密钥 (CMK)。
> * 如果帐户未启用分析存储。

## <a name="permissions"></a>权限

若要执行迁移，用户需要对即将迁移的帐户具有 `Microsoft.DocumentDB/databaseAccounts/write` 权限。  

## <a name="pricing-after-migration"></a>迁移后的定价

将帐户迁移到连续备份模式后，与周期备份模式相比，连续备份模式的成本不同。 连续模式备份成本比周期模式要便宜得多。 要了解更多信息，请参阅[连续备份模式定价](continuous-backup-restore-introduction.md#continuous-backup-pricing)示例。

## <a name="migrate-using-portal"></a><a id="portal"></a> 使用门户进行迁移

使用以下步骤将帐户从周期备份迁移到连续备份模式：

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 导航到你的 Azure Cosmos DB 帐户，打开“功能”窗格。 选择“连续备份”，然后选择“启用”。 

   :::image type="content" source="./media/migrate-continuous-backup/enable-backup-migration.png" alt-text="使用 Azure 门户迁移到连续模式" lightbox="./media/migrate-continuous-backup/enable-backup-migration.png":::

1. 迁移正在进行中时，状态显示为“挂起”。 迁移完成后，状态更改为“开启”。 迁移时间取决于用户帐户中的数据大小。

   :::image type="content" source="./media/migrate-continuous-backup/migration-status.png" alt-text="从 Azure 门户中检查迁移状态" lightbox="./media/migrate-continuous-backup/migration-status.png":::

## <a name="migrate-using-powershell"></a><a id="powershell"></a>使用 PowerShell 迁移

安装[最新版本的 Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) 或高于 6.2.0 的版本。 接下来，执行以下步骤：

1. 连接到 Azure 帐户：

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

1. 将帐户从周期迁移到连续备份模式：

   ```azurepowershell-interactive
   Update-AzCosmosDBAccount ` 
     -ResourceGroupName "myrg" ` 
     -Name "myAccount" `
     -BackupPolicyType Continuous
   ```

## <a name="migrate-using-cli"></a><a id="cli"></a>使用 CLI 进行迁移

1. 安装最新版本的 Azure CLI：

   * 如果没有 CLI，请[安装](/cli/azure/)最新版本的 Azure CLI 或高于 2.26.0 的版本。
   * 如果已安装 Azure CLI，请使用 `az upgrade` 命令升级到最新版本。
   * 另外，用户还可以使用 Azure 门户中的 Cloud Shell。

1. 登录到 Azure 帐户，并运行以下命令以将帐户迁移到连续模式：

   ```azurecli-interactive
   az login

   az cosmosdb update -n <myaccount> -g <myresourcegroup> --backup-policy-type continuous
   ```

1. 迁移成功完成后，输出显示 backupPolicy 对象的 type 属性设置为 Continuous。

   ```console
    {
      "apiProperties": null,
      "backupPolicy": {
        "type": "Continuous"
      },
      "capabilities": [],
      "connectorOffer": null,
      "consistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxIntervalInSeconds": 5,
        "maxStalenessPrefix": 100
      },
    …
    }
   ```

## <a name="migrate-using-resource-manager-template"></a><a id="ARM-template"></a>使用资源管理器模板进行迁移

若要使用 ARM 模板迁移到连续备份模式，请查找模板的 backupPolicy 部分，并更新 `type` 属性。 例如，如果现有模板具有类似于以下 JSON 对象的备份策略：

```json
"backupPolicy": {
   "type": "Periodic",
   "periodicModeProperties": {
   "backupIntervalInMinutes": 240,
   "backupRetentionIntervalInHours": 8
   }
},
```

将其替换为以下 JSON 对象：

```json
"backupPolicy": {
   "type": "Continuous"
},
```

接下来，使用 Azure PowerShell 或 CLI 部署模板。 下面的示例演示如何使用 CLI 命令部署模板：

```azurecli
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="what-to-expect-during-and-after-migration"></a>迁移期间和迁移后会出现什么情况？

从周期模式迁移到连续模式时，无法运行任何执行帐户级别更新或删除的控制平面操作。 例如，迁移正在进行时，无法运行添加或删除区域、帐户故障转移、更新备份策略等操作。 迁移时间取决于帐户中的数据大小和区域数量。 只有迁移成功完成后，才会成功完成迁移的帐户上的还原操作。

可以在迁移完成后还原帐户。 如果迁移在太平洋标准时间下午 1:00 完成，则可以从太平洋标准时间下午 1:00 开始执行时间点还原。

## <a name="frequently-asked-questions"></a>常见问题解答

#### <a name="does-the-migration-only-happen-at-the-account-level"></a>迁移是否只发生在帐户级别？
能。

#### <a name="which-accounts-can-be-targeted-for-backup-migration"></a>哪些帐户可以作为备份迁移的目标？
目前，使用单一写入区域的 SQL API 和 API for MongoDB 帐户，这些帐户已经在整个支持迁移中共享、预配或自动缩放预配。

启用了分析存储、多写入区域和客户管理密钥 (CMK) 的帐户不支持迁移。

#### <a name="does-the-migration-take-time-what-is-the-typical-time"></a>迁移是否需要时间？ 一般需要多长时间？
迁移需要时间，具体取决于帐户中的数据大小和区域数量。 可以使用 Azure CLI 或 PowerShell 命令获取迁移状态。 对于数十 TB 数据的大型帐户，迁移可能需要好几天才能完成。

#### <a name="does-the-migration-cause-any-availability-impactdowntime"></a>迁移是否会导致任何可用性影响/停机时间？
不会，迁移操作会在后台进行，因此客户端请求不受影响。 但是，我们需要在迁移过程中执行一些后端操作，并且如果帐户负载过大，可能需要更多时间。

#### <a name="what-happens-if-the-migration-fails-will-i-still-get-the-periodic-backups-or-get-the-continuous-backups"></a>如果迁移失败会怎样？ 是否仍可以获取周期备份或获取连续备份？
启动迁移过程后，帐户将开始变为连续模式。  如果迁移失败，则必须重新启动迁移，直到成功。

#### <a name="how-do-i-perform-a-restore-to-a-timestamp-beforeduringafter-the-migration"></a>如何还原到迁移之前/期间/之后的时间戳？
假设在 t1 开始迁移并在 t5 完成迁移，则不能使用 t1 和 t5 之间还原时间戳。

若要还原到 t5 后的某个时间，因为帐户现在处于连续模式，可以使用 Azure 门户、CLI 或 PowerShell 执行还原，就像通常使用连续帐户执行的操作一样。 只有在迁移完成后，才能完成这种自助服务还原。

若要还原到 t1 之前的某个时间，可以像往常一样使用周期备份帐户来打开支持票证。 迁移后，最多有 30 天的时间来执行周期还原。  在这 30 天内，可以在迁移前基于帐户的备份保留/间隔进行还原。  例如，如果备份配置在 1 小时间隔内保留 24 个副本，则可以还原到 [t1 –24 小时] 和 [t1] 之间的任何时间。

#### <a name="which-account-level-control-plane-operations-are-blocked-during-migration"></a>迁移期间会阻止哪些帐户级别的控制平面操作？
在迁移期间，会阻止添加/删除区域、故障转移、复制、更改备份策略、导致数据移动的吞吐量更改等操作。

#### <a name="if-the-migration-fails-for-some-underlying-issue-would-it-still-block-the-control-plane-operation-until-it-is-retried-and-completed-successfully"></a>如果迁移由于某种基础问题而失败，那么在重试和成功完成之前，它仍会阻止控制平面操作吗？
失败的迁移不会阻止任何控制平面操作。 如果迁移失败，建议进行重试直至成功，然后再执行任何其他控制平面操作。

#### <a name="is-it-possible-to-cancel-the-migration"></a>能否取消迁移？
迁移是不可逆的操作，因此无法取消。

#### <a name="is-there-a-tool-that-can-help-estimate-migration-time-based-on-the-data-usage-and-number-of-regions"></a>是否有工具可帮助基于数据使用量和区域数来预估迁移事件？
没有用于预估时间的工具。 不过，我们的缩放运行指出，具有 1 TB 数据的单个区域大约耗时一个半小时。

对于多区域帐户，总数据大小的计算公式是 `Number_of_regions * Data_in_single_region`。

#### <a name="since-the-continuous-backup-mode-is-now-ga-would-you-still-recommend-restoring-a-copy-of-your-account-and-try-migration-on-the-copy-before-deciding-to-migrate-the-production-account"></a>由于连续备份模式现已正式发布，是否仍建议先还原帐户副本，并尝试在副本上迁移，然后再决定迁移生产帐户？
建议测试连续备份模式功能，看看它是否按预期工作，然后再迁移生产帐户。 这是因为迁移是单向操作，而且不可逆。

## <a name="next-steps"></a>后续步骤

要了解有关连续备份模式的更多信息，请参阅以下文章：

* [带有时间点还原的连续备份模式简介。](continuous-backup-restore-introduction.md)

* [连续备份模式资源模型。](continuous-backup-restore-resource-model.md)

* 使用 [Azure 门户](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli) 或 [Azure 资源管理器](restore-account-continuous-backup.md#restore-arm-template)还原帐户。
