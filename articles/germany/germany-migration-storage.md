---
title: 将 Azure 存储资源从 Azure 德国迁移到全球 Azure
description: 本文介绍如何将 Azure 存储资源从 Azure 德国迁移到全球 Azure。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: ce152dc6948e7d77ace2defede7f1cb0c0b744a8
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "117028995"
---
# <a name="migrate-storage-resources-to-global-azure"></a>将存储资源迁移到全球 Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

本文中的信息可帮助你将 Azure 存储资源从 Azure 德国迁移到全球 Azure。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="blobs"></a>Blob

AzCopy 是一款免费工具，可用于复制 blob、文件和表。 AzCopy 适用于 Azure 到 Azure、本地到 Azure 和 Azure 到本地的迁移。 使用 AzCopy 进行迁移，将 blob 直接从 Azure 德国复制到全球 Azure。

如果不对源 VM 使用托管磁盘，请使用 AzCopy 将 .vhd 文件复制到目标环境。 否则，必须提前完成某些步骤。 有关详细信息，请参阅[对托管磁盘的建议](#managed-disks)。

以下示例演示 AzCopy 的工作原理。 有关完整的引用，请参阅 [AzCopy 文档](../storage/common/storage-use-azcopy-v10.md)。

AzCopy 使用字词 Source 和 Dest 来表示 URI 。 Azure 德国的 URI 始终采用以下格式：

```http
https://<storageaccountname>.blob.core.cloudapi.de/<containername>/<blobname>
```

全球 Azure 的 URI 始终采用以下格式：

```http
https://<storageaccountname>.blob.core.windows.net/<containername>/<blobname>
```

通过使用 PowerShell 或使用 Azure CLI，可以从门户获得 URI 的三个部分 (storageaccountname、containername、blobname)  。 blob 的名称可以是 URI 的一部分，也可以按固定模式提供，例如 vm121314.vhd。

还需要存储帐户密钥来访问 Azure 存储帐户。 在门户中使用 PowerShell 或 CLI 获取它们。 例如：

```powershell
Get-AzStorageAccountKey -Name <saname> -ResourceGroupName <rgname>
```

与平时一样，每个存储帐户只需要两个密钥中的一个。

示例：

URI 部分 | 示例值
-------- | --------------
源 storageAccount | `migratetest`
源容器 | `vhds`
源 blob | `vm-121314.vhd`
目标 storageAccount | `migratetarget`
目标容器 | `targetcontainer`

此命令将虚拟硬盘从 Azure 德国复制到全球 Azure（缩减密钥以提高可读性）：

```cmd
azcopy -v /source:https://migratetest.blob.core.cloudapi.de/vhds /sourcekey:"0LN...w==" /dest:https://migratetarget.blob.core.windows.net/targetcontainer /DestKey:"o//ucDi5TN...w==" /Pattern:vm-121314.vhd
```

若要获取 VHD 的一致副本，请关闭 VM，然后再复制 VHD。 请为复制活动规划停机时间。 复制 VHD 后，[在目标环境中重新生成 VM](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。

参考信息：

- 请查阅 [AzCopy 文档](../storage/common/storage-use-azcopy-v10.md)。
- 了解如何[从还原的磁盘创建 VM](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。

## <a name="managed-disks"></a>托管磁盘

Azure 托管磁盘通过管理与 VM 磁盘关联的存储帐户，简化了 Azure 基础设施即服务 (IaaS) VM 的磁盘管理。 

由于你无法直接访问 .vhd 文件，因此不能直接使用 AzCopy 等工具复制文件（请参与 [Blob](#blobs)）。 解决方法是首先通过获取临时共享访问签名 URI 导出托管磁盘，然后使用此信息下载或者复制托管磁盘。 以下部分举例说明了如何获取共享访问签名 URI 以及如何处理该 URI。

### <a name="step-1-get-the-shared-access-signature-uri"></a>步骤 1：获取共享访问签名 URI

1. 在门户中，搜索托管磁盘。 （它与 VM 位于同一资源组。 资源类型为磁盘。）
1. 在“概述”页面上，选择顶部菜单中的“导出”按钮（必须先关闭并取消分配 VM，或者取消附加 VM） 。
1. 定义 URI 过期的时间（默认值为 3,600 秒）。
1. 生成 URL（此步骤只需几秒钟）。
1. 复制 URL（它只显示一次）。

### <a name="step-2-azcopy"></a>步骤 2：AzCopy

有关如何使用 AzCopy 的示例，请参阅 [Blob](#blobs)。 使用 AzCopy（或类似工具）将磁盘直接从源环境复制到目标环境。 在 AzCopy 中，必须将 URI 拆分为基础 URI 和共享访问签名部分。 URI 的共享访问签名部分以字符“?”开头。 门户为共享访问签名 URI 提供此 URI：

```http
https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D>
```

以下命令显示 AzCopy 的源参数：

```cmd
/source:"https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd"
```

```cmd
/sourceSAS:" ?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D"
```

以下是完整的命令：

```cmd
azcopy -v /source:"https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd" /sourceSAS:"?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D" /dest:"https://migratetarget.blob.core.windows.net/targetcontainer/newdisk.vhd" /DestKey:"o//ucD... Kdpw=="
```

### <a name="step-3-create-a-new-managed-disk-in-the-target-environment"></a>步骤 3：在目标环境中创建新的托管磁盘

有几种方法可用于创建新的托管磁盘。 下面是在 Azure 门户中执行该操作的方法：

1. 在门户中，选择“新建” > “托管磁盘” > “创建”  。
1. 输入新磁盘的名称。
1. 选一个择资源组。
1. 在“源类型”下，选择“存储 blob” 。 然后，从 AzCopy 命令复制目标 URI，或进行浏览以选择目标 URI。
1. 如果复制的是 OS 磁盘，请选择“OS”类型。 对于其他磁盘类型，请选择“创建”。

### <a name="step-4-create-the-vm"></a>步骤 4：创建 VM

如前所述，有多种方法可以使用此新托管磁盘创建 VM。 以下是两种选项：

- 在门户中，选择磁盘，然后选择“创建 VM”。 像往常一样定义 VM 的其他参数。
- 对于 PowerShell，请参阅[从还原的磁盘创建 VM](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。

参考信息：

- 了解如何[通过 API](/rest/api/compute/disks/grantaccess) 获取共享访问签名 URI，以导出到磁盘。 
- 了解如何[通过 API](/rest/api/compute/disks/createorupdate#create-a-managed-disk-by-importing-an-unmanaged-blob-from-a-different-subscription.) 从非托管 blob 创建托管磁盘。

## <a name="tables"></a>表

可以使用存储资源管理器迁移 Azure 中的表。 存储资源管理器是用于管理 Azure 云存储资源的工具。 使用存储资源管理器，可以连接到源德国存储帐户，并将表复制到目标 Azure 全球存储帐户。

首先，请安装 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

### <a name="connect-to-source"></a>连接到源

使用存储资源管理器从源 Azure 存储帐户复制表。 

将存储资源管理器连接到 Microsoft Azure 德国中的源表资源。 你可以[登录以访问订阅中的资源](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure)，也可以[附加到特定的存储资源](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource)。 

### <a name="connect-to-target"></a>连接到目标

可以使用存储资源管理器将表粘贴到目标 Azure 存储帐户。

将存储资源管理器连接到目标 Microsoft Azure 订阅或 Azure 存储。 你可以[登录以访问订阅中的资源](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure)，也可以[附加到特定的存储资源](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource)。 


### <a name="migrate-tables"></a>迁移表

使用存储资源管理器将表从 Azure 德国复制到 Azure 全球。 右键单击你想要复制的表，并从快捷菜单选择“复制表”，即可复制表。 以下示例演示了从“Azure 德国订阅”复制“testmigrationtable” 。

![复制从 Azure 德国订阅中选中的表菜单](./media/germany-migration-storage/copy-table.png)

使用存储资源管理器将表粘贴到目标 Azure 存储帐户。 右键单击目标 Azure 存储帐户中的“表”节点即可粘贴表。 以下示例演示了从已连接的 Azure 存储帐户粘贴“testmigrationtable”。
 
![粘贴从目标 Azure 存储中选中的表菜单](./media/germany-migration-storage/paste-table.png)

对每个要迁移的表重复复制和粘贴步骤。

## <a name="file-shares"></a>文件共享

使用 AzCopy 进行迁移，将文件共享直接从 Azure 德国复制到全球 Azure。 AzCopy 是一款免费工具，可用于复制 blob、文件和表。

首先，请[下载 AzCopy](https://aka.ms/downloadazcopy) 并进行安装。

AzCopy 使用字词 Source 和 Dest 来表示 URI 。 Azure 德国的 URI 始终采用以下格式：

```http
https://<storageaccountname>.blob.core.cloudapi.de/<filesharename>
```

全球 Azure 的 URI 始终采用以下格式：

```http
https://<storageaccountname>.blob.core.windows.net/<filesharename>
```
需要存储帐户 SAS 令牌来访问 Azure 存储帐户。 

以下示例命令将所有文件共享、目录和文件从 Azure 德国存储帐户复制到全球 Azure 存储帐户。 有关完整的引用，请参阅 [AzCopy 文档](../storage/common/storage-use-azcopy-v10.md)。

URI 部分 | 示例值
-------- | --------------
源 storageAccount | `migratetest`
源文件共享 | `sourcefileshare`
目标 storageAccount | `migratetarget`
目标文件共享 | `targetfileshare`

```cmd
azcopy copy "https://migratetest.blob.core.cloudapi.de/sourcefileshare?<SAS-token>" "https://migratetarget.blob.core.windows.net/targetfileshare?<SAS-token>" --recursive=true
```

有关 AzCopy 的详细信息，请参阅 [AzCopy 文档](../storage/common/storage-use-azcopy-v10.md)和[使用 AzCopy 和文件存储传输数据](../storage/common/storage-use-azcopy-files.md#copy-files-between-storage-accounts)。

## <a name="next-steps"></a>后续步骤

了解用于在以下服务类别中迁移资源的工具、方法和建议：

- [计算](./germany-migration-compute.md)
- [联网](./germany-migration-networking.md)
- [Web](./germany-migration-web.md)
- [数据库](./germany-migration-databases.md)
- [分析](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [集成](./germany-migration-integration.md)
- [标识](./germany-migration-identity.md)
- [安全性](./germany-migration-security.md)
- [管理工具](./germany-migration-management-tools.md)
- [介质](./germany-migration-media.md)
