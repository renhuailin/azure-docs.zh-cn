---
title: 使用 Azure CLI 还原 Azure 文件共享
description: 了解如何使用 Azure CLI 还原恢复服务保管库中的已备份 Azure 文件共享
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: a025de7bfb9db037b2008d69be7782feabb482f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94562315"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>使用 Azure CLI 还原 Azure 文件共享

Azure CLI 提供命令行体验，用于管理 Azure 资源。 它是构建自定义自动化以使用 Azure 资源的绝佳工具。 本文介绍如何使用 Azure CLI 从 [Azure 备份](./backup-overview.md)所创建的还原点来还原整个文件共享或特定文件。 也可以使用 [Azure PowerShell](./backup-azure-afs-automation.md) 或 [Azure 门户](backup-afs.md)执行这些步骤。

完成本文的学习后，你将了解如何使用 Azure CLI 执行以下操作：

* 查看已备份 Azure 文件共享的还原点。
* 还原完整的 Azure 文件共享。
* 还原单个文件或文件夹。

>[!NOTE]
> Azure 备份现在支持使用 Azure CLI 将多个文件或文件夹还原到原始位置或备用位置。 了解详细信息，请参阅本文档的[将多个文件或文件夹还原到原始位置或备用位置](#restore-multiple-files-or-folders-to-original-or-alternate-location)部分。

## <a name="prerequisites"></a>先决条件

本文假设已有 Azure 文件共享由 Azure 备份进行备份。 如果没有，请参阅[使用 CLI 备份 Azure 文件共享](backup-afs-cli.md)，为文件共享配置备份。 本文将使用以下资源：

| 文件共享 | 存储帐户 | 区域 | 详细信息 |
|---|---|---|---|
| “azurefiles” | “afsaccount” | EastUS | 使用 Azure 备份所备份的原始源 |
| “azurefiles1” | “afaccount1” | EastUS | 用于备用位置恢复的目标源 |

可以使用类似的文件共享结构来尝试本文中所述的不同类型的还原。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - 本教程需要 Azure CLI 版本 2.0.18 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>提取 Azure 文件共享的恢复点

使用 [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) cmdlet 列出已备份文件共享的所有恢复点。

以下示例在“afsaccount”存储帐户中提取“azurefiles”文件共享的恢复点列表。 

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

还可以通过提供以下两个附加参数，使用容器和项的易记名称来运行之前的 cmdlet：

* “--backup-management-type”：“azurestorage”
* “--workload-type”：“azurefileshare”

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

结果集是具有每个还原点的时间和一致性详细信息的恢复点列表。

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

输出中的“name”属性对应于可用作恢复操作中的“--rp name”参数值的恢复点名称。 

## <a name="full-share-recovery-by-using-the-azure-cli"></a>使用 Azure CLI 完全共享恢复

可以使用此还原选项在原始或备用位置还原完整的文件共享。

定义以下参数以执行还原操作：

* “--container-name”：托管已备份原始文件共享的存储帐户的名称。 若要检索容器的名称或易记名称，请使用 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 命令。
* “--item-name”：想要用于还原操作的已备份原始文件共享的名称。 若要检索已备份项的名称或易记名称，请使用 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 命令。

### <a name="restore-a-full-share-to-the-original-location"></a>将完全共享还原到原始位置

还原到原始位置时，无需指定与目标相关的参数。 仅“Resolve Conflict”必须提供。

下面的示例使用了 [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) cmdlet，并将还原模式设置为“originallocation”，以将“azurefiles”文件共享还原到原始位置。  使用在 [提取 Azure 文件共享的恢复点](#fetch-recovery-points-for-the-azure-file-share)中获取的恢复点932883129628959823：

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

输出中的“Name”属性对应于备份服务为还原操作创建的作业的名称。 若要跟踪此作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

### <a name="restore-a-full-share-to-an-alternate-location"></a>将完全共享还原到备用位置

可以使用此选项将文件共享还原到备用位置，并使原始文件共享保持原样。 为备用位置恢复指定以下参数：

* “--target-storage-account”：要将备份内容还原到的存储帐户。 目标存储帐户应与保管库位于同一位置。
* “--target-file-share”：目标存储帐户中要将备份内容还原到的文件共享。
* “--target-folder”：文件共享中要将数据还原到的文件夹。 如果要将备份内容还原到根文件夹，请将目标文件夹值指定为空字符串。
* “--resolve-conflict”：与还原的数据发生冲突时提供的说明。 接受“覆盖”或“跳过”。

下面的示例使用 [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare)，并将还原模式设置为“alternatelocation”，以将“afsaccount”存储帐户中的“azurefiles”文件共享还原到“afaccount1”存储帐户中的“azurefiles1”文件共享。    

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

输出中的“Name”属性对应于备份服务为还原操作创建的作业的名称。 若要跟踪此作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

## <a name="item-level-recovery"></a>项目级恢复

可以使用此还原选项来还原原始或备用位置中的单个文件或文件夹。

定义以下参数以执行还原操作：

* “--container-name”：托管已备份原始文件共享的存储帐户的名称。 若要检索容器的名称或易记名称，请使用 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 命令。
* “--item-name”：想要用于还原操作的已备份原始文件共享的名称。 若要检索已备份项的名称或易记名称，请使用 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 命令。

为想要恢复的项指定以下参数：

* “SourceFilePath”：文件共享中要还原的文件的绝对路径，字符串格式。 此路径与 [az storage file download](/cli/azure/storage/file#az-storage-file-download) 或 [az storage file show](/cli/azure/storage/file#az-storage-file-show) CLI 命令中使用的路径相同。
* “SourceFileType”：选择是否已选择目录或文件。 接受“目录”或“文件”。
* “ResolveConflict”：与还原的数据发生冲突时提供的说明。 接受“覆盖”或“跳过”。

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>将单个文件或文件夹还原到原始位置

使用 [az backup restore azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) cmdlet，并将还原模式设置为“originallocation”，以将特定文件或文件夹还原到其原始位置。

下面的示例将“RestoreTest.txt”文件还原到其原始位置：“azurefiles”文件共享。 

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

输出中的“Name”属性对应于备份服务为还原操作创建的作业的名称。 若要跟踪此作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>将单个文件或文件夹还原到备用位置

若要将特定文件或文件夹还原到备用位置，请使用 [az backup restore restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) cmdlet，将还原模式设置为“alternatelocation”，并指定下列与目标相关的参数：

* “--target-storage-account”：要将备份内容还原到的存储帐户。 目标存储帐户应与保管库位于同一位置。
* “--target-file-share”：目标存储帐户中要将备份内容还原到的文件共享。
* “--target-folder”：文件共享中要将数据还原到的文件夹。 如果要将备份内容还原到根文件夹，请将目标文件夹的值指定为空字符串。

下面的示例将“azurefiles”文件共享中原始存在的“RestoreTest.txt”文件还原到备用位置：“afaccount1”存储帐户中托管的“azurefiles1”文件共享中的“restoredata”文件夹。    

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

输出中的“Name”属性对应于备份服务为还原操作创建的作业的名称。 若要跟踪此作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>将多个文件或文件夹还原到原始或备用位置

若要为多个项执行还原，请传递“source-file-path”参数的值作为想要还原的所有文件或文件夹的“space separated”路径。 

下面的示例将“Restore.txt”和“AFS testing Report.docx”文件还原到其原始位置。 

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

输出将如下所示：

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

输出中的“Name”属性对应于备份服务为还原操作创建的作业的名称。 若要跟踪此作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

如果想要将多个项还原到备用位置，请通过指定与目标相关的参数来使用上面的命令，如[将单个文件或文件夹还原到备用位置](#restore-individual-files-or-folders-to-an-alternate-location)部分所述。

## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure CLI 管理 Azure 文件共享备份](manage-afs-backup-cli.md)。
