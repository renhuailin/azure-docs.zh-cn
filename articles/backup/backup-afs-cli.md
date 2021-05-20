---
title: 使用 Azure CLI 备份 Azure 文件共享
description: 了解如何使用 Azure CLI 在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: a5f7472c511a5a50415a6ceb47497dd6f4f1e60b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773614"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>使用 Azure CLI 备份 Azure 文件共享

Azure 命令行接口 (CLI) 为管理 Azure 资源提供了命令行体验。 对于生成自定义自动化来使用 Azure 资源来说，它是绝佳工具。 本文详细介绍了如何使用 Azure CLI 备份 Azure 文件共享。 也可以使用 [Azure PowerShell](./backup-azure-afs-automation.md) 或 [Azure 门户](backup-afs.md)执行这些步骤。

在本教程结束时，你将学会如何使用 Azure CLI 执行以下操作：

* 创建恢复服务保管库
* 为 Azure 文件共享启用备份
* 为文件共享触发按需备份

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0.18 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是一个实体，它为你提供跨所有备份项的合并视图和管理功能。 运行受保护资源的备份作业时，该作业会在恢复服务保管库中创建一个恢复点。 然后，可以使用其中一个恢复点将数据还原到给定的时间点。

若要创建恢复服务保管库，请按以下步骤操作：

1. 保管库放在资源组中。 如果没有现有的资源组，请使用 [az group create](/cli/azure/group#az_group_create) 新建一个。 在本教程中，我们在美国西部区域中新建资源组 azurefiles。

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. 使用 [az backup vault create](/cli/azure/backup/vault#az_backup_vault_create) cmdlet 来创建保管库。 请为保管库指定与资源组相同的位置。

    下面的示例在美国东部区域中创建名为“azurefilesvault”的恢复服务保管库。

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>为 Azure 文件共享启用备份

为了能够更好地理解此部分，你需要已有要为其配置备份的 Azure 文件共享。 如果没有，请使用 [az storage share create](/cli/azure/storage/share#az_storage_share_create) 命令来创建 Azure 文件共享。

若要为文件共享启用备份，则需要创建一个保护策略来定义备份作业何时运行以及恢复点存储多长时间。 可以使用 [az backup policy create](/cli/azure/backup/policy#az_backup_policy_create) cmdlet 来创建备份策略。

下面的示例使用 [az backup protection enable-for-azurefileshare](/cli/azure/backup/protection#az_backup_protection_enable_for_azurefileshare) cmdlet 通过计划 1 备份策略为 afsaccount 存储帐户中的 azurefiles 文件共享启用备份：

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

输出中的“Name”属性对应于备份服务为“启用备份”操作创建的作业的名称。 若要跟踪此作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet。

## <a name="trigger-an-on-demand-backup-for-file-share"></a>为文件共享触发按需备份

若要为文件共享触发按需备份，而不是等待备份策略在计划时间运行作业，请使用 [az backup protection backup-now](/cli/azure/backup/protection#az_backup_protection_backup_now) cmdlet。

需要定义以下参数来触发按需备份：

* --container-name 是托管文件共享的存储帐户的名称。 若要检索容器的名称或易记名称，请使用 [az backup container list](/cli/azure/backup/container#az_backup_container_list) 命令。
* --item-name 是要为其触发按需备份的文件共享的名称。 若要检索已备份项的名称或易记名称，请使用 [az backup item list](/cli/azure/backup/item#az_backup_item_list) 命令。
* --retain-until 指定恢复点保持期截止日期。 应将此值设置为 UTC 时间格式 (dd-mm-yyyy)。

下面的示例为 afsaccount 存储帐户中的 azurefiles 文件共享触发按需备份，保持期截止日期为 2020 年 1 月 20 日。

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

输出中的“Name”属性对应于备份服务为“按需备份”操作创建的作业的名称。 若要跟踪此作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet。

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 CLI 还原 Azure 文件共享](restore-afs-cli.md)
* 了解如何[使用 CLI 管理 Azure 文件共享备份](manage-afs-backup-cli.md)
