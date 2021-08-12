---
title: 使用 Azure CLI 管理 Azure 文件共享备份
description: 了解如何使用 Azure CLI 管理和监视由 Azure 备份所备份的 Azure 文件共享。
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 9ddee7e0e7595d4606d077f33362344fe582d9a8
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111902962"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>使用 Azure CLI 管理 Azure 文件共享备份

Azure CLI 提供了一个命令行体验，用于管理 Azure 资源。 它是构建自定义自动化以使用 Azure 资源的绝佳工具。 本文介绍如何执行管理和监视由 [Azure 备份](./backup-overview.md)所备份的 Azure 文件共享的任务。 还可以使用 [Azure 门户](https://portal.azure.com/)执行这些步骤。

## <a name="prerequisites"></a>先决条件

本文假设你已有通过 [Azure 备份](./backup-overview.md)备份的 Azure 文件共享。 如果你没有文件共享，请参阅[使用 CLI 备份 Azure 文件共享](backup-afs-cli.md)来配置你的文件共享的备份。 本文将使用以下资源：
   -  资源组：azurefiles
   -  RecoveryServicesVault：azurefilesvault
   -  存储帐户：afsaccount
   -  文件共享：azurefiles
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - 本教程需要 Azure CLI 版本 2.0.18 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="monitor-jobs"></a>监视作业

当触发备份或还原操作时，备份服务会创建一个用于跟踪的作业。 若要监视已完成的或当前正在运行的作业，请使用 [az backup job list](/cli/azure/backup/job#az_backup_job_list) cmdlet。 使用 CLI，你还可以[暂停当前正在运行的作业](/cli/azure/backup/job#az_backup_job_stop)或[等待作业完成](/cli/azure/backup/job#az_backup_job_wait)。

以下示例显示了 azurefilesvault  恢复服务保管库的备份作业的状态：

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```
## <a name="create-policy"></a>创建策略

可以创建备份策略，方法是使用以下参数执行 [az backup policy create](/cli/azure/backup/policy?view=azure-cli-latest&preserve-view=true#az_backup_policy_create) 命令：

- --backup-management-type - Azure 存储
- --workload-type - AzureFileShare
- --name - 策略的名称
- --policy - JSON 文件，其中包含有关计划和保留的相应详细信息
- --resource-group - 保管库的资源组
- --vault-name - 保管库的名称

**示例**

```azurecli-interactive
az backup policy create --resource-group azurefiles --vault-name azurefilesvault --name schedule20 --backup-management-type AzureStorage --policy samplepolicy.json --workload-type AzureFileShare

```

示例 JSON (samplepolicy.json)

```json
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule20",
  "location": null,
  "name": "schedule20",
  "properties": {
    "backupManagementType": "AzureStorage",
    "protectedItemsCount": 0,
    "retentionPolicy": {
      "dailySchedule": {
        "retentionDuration": {
          "count": 30,
          "durationType": "Days"
        },
        "retentionTimes": [
          "2020-01-05T08:00:00+00:00"
        ]
      },
      "monthlySchedule": null,
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": null,
      "yearlySchedule": null
    },
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunDays": null,
      "scheduleRunFrequency": "Daily",
      "scheduleRunTimes": [
        "2020-01-05T08:00:00+00:00"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "timeZone": "UTC",
    "workLoadType": “AzureFileShare”
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
}
```

成功创建策略后，命令的输出会显示已在执行命令时作为参数传递的策略 JSON。

可以根据需要修改策略的 schedule 和 retention 节。

**示例**

如果要将每月第一个星期日的备份保留两个月，请更新每月计划，如下所示：

```json
"monthlySchedule": {
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        },
        "retentionScheduleDaily": null,
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Sunday"
          ],
          "weeksOfTheMonth": [
            "First"
          ]
        },
        "retentionTimes": [
          "2020-01-05T08:00:00+00:00"
        ]
      }

```

## <a name="modify-policy"></a>修改策略

你可以使用 [az backup item set-policy](/cli/azure/backup/item#az_backup_item_set_policy) 修改备份策略来更改备份频率或保持期。

若要更改策略，请定义以下参数：

* --container-name：承载着文件共享的存储帐户的名称。 若要检索你的容器的名称或易记名称，请使用 [az backup container list](/cli/azure/backup/container#az_backup_container_list) 命令。 
* --name：要更改其策略的文件共享的名称。 若要检索已备份项的名称或易记名称，请使用 [az backup item list](/cli/azure/backup/item#az_backup_item_list) 命令。 
* --policy-name：要为文件共享设置的备份策略的名称。 你可以使用 [az backup policy list](/cli/azure/backup/policy#az_backup_policy_list) 查看保管库的所有策略。

以下示例为  afsaccount 存储帐户中的 azurefiles 文件共享设置 schedule2 备份策略。

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

你还可以通过提供以下两个附加参数，使用容器和项的易记名称来运行上一个命令：

* --backup-management-type：azurestorage
* --workload-type：azurefileshare

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

输出中的“Name”属性对应于备份服务为更改策略操作创建的作业的名称。 若要跟踪此作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet。

## <a name="stop-protection-on-a-file-share"></a>停止对文件共享的保护

可以通过两种方法来停止保护 Azure 文件共享：

* 停止所有将来的备份作业，并删除所有恢复点。
* 停止所有将来的备份作业，但保留恢复点。

在存储中保留恢复点可能会产生费用，因为 Azure 备份创建的基础快照将会保留。 保留恢复点的好处是，以后可以根据需要还原文件共享。 如需了解保留恢复点的成本，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/storage/files)。 如果选择删除所有恢复点，则无法还原文件共享。

若要停止对文件共享的保护，请定义以下参数：

* --container-name：承载着文件共享的存储帐户的名称。 若要检索你的容器的名称或易记名称，请使用 [az backup container list](/cli/azure/backup/container#az_backup_container_list) 命令。 
* --item-name：要停止保护的文件共享的名称。 若要检索已备份项的名称或易记名称，请使用 [az backup item list](/cli/azure/backup/item#az_backup_item_list) 命令。 

### <a name="stop-protection-and-retain-recovery-points"></a>停止保护但保留恢复点

若要停止保护但保留数据，请使用 [az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable) cmdlet。

下面的示例将停止对 azurefiles 文件共享的保护，但会保留所有恢复点。

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

你还可以通过提供以下两个附加参数，使用容器和项的易记名称来运行上一个命令：

* --backup-management-type：azurestorage
* --workload-type：azurefileshare

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

输出中的“Name”属性对应于备份服务为停止保护操作创建的作业的名称。 若要跟踪此作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet。

### <a name="stop-protection-without-retaining-recovery-points"></a>停止保护而不保留恢复点

若要停止保护且不保留恢复点，请在 delete-backup-data 选项设置为 true 的情况下使用 [az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable) cmdlet。

下面的示例会停止对 azurefiles 文件共享的保护，且不保留恢复点。

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

你还可以通过提供以下两个附加参数，使用容器和项的易记名称来运行上一个命令：

* --backup-management-type：azurestorage
* --workload-type：azurefileshare

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>恢复对文件共享的保护

如果你停止了对 Azure 文件共享的保护，但保留了恢复点，则以后可以恢复保护。 如果不保留恢复点，则无法恢复保护。

若要恢复对文件共享的保护，请定义以下参数：

* --container-name：承载着文件共享的存储帐户的名称。 若要检索你的容器的名称或易记名称，请使用 [az backup container list](/cli/azure/backup/container#az_backup_container_list) 命令。 
* --item-name：要恢复保护的文件共享的名称。 若要检索已备份项的名称或易记名称，请使用 [az backup item list](/cli/azure/backup/item#az_backup_item_list) 命令。 
* --policy-name：要为其恢复文件共享保护的备份策略的名称。

下面的示例使用 [az backup protection resume](/cli/azure/backup/protection#az_backup_protection_resume) cmdlet 来恢复使用 schedule1 备份策略对 azurefiles 文件共享进行的保护。

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

你还可以通过提供以下两个附加参数，使用容器和项的易记名称来运行上一个命令：

* --backup-management-type：azurestorage
* --workload-type：azurefileshare

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

输出中的“Name”属性对应于备份服务为恢复保护操作创建的作业的名称。 若要跟踪此作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet。

## <a name="unregister-a-storage-account"></a>注销存储帐户

如果要使用一个不同的恢复服务保管库来保护特定存储帐户中的文件共享，请先在该存储帐户中[停止对所有文件共享的保护](#stop-protection-on-a-file-share)。 然后，从当前用于保护的恢复服务保管库中注销该帐户。

你需要提供容器名称来注销存储帐户。 若要检索容器的名称或易记名称，请使用 [az backup container list](/cli/azure/backup/container#az_backup_container_list) 命令。 

下面的示例使用 [az backup container unregister](/cli/azure/backup/container#az_backup_container_unregister) cmdlet 从 azurefilesvault 中注销 afsaccount 存储帐户。

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

还可以通过提供以下附加参数，使用容器的易记名称来运行上一 cmdlet：

* --backup-management-type：azurestorage

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[对 Azure 文件共享备份进行故障排除](troubleshoot-azure-files.md)。
