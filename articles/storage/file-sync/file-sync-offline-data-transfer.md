---
title: 使用 Azure DataBox 将数据迁移到 Azure 文件同步
description: 通过与 Azure 文件同步兼容的方式脱机批量迁移数据。避免文件冲突，并在启用同步后保留文件和文件夹 ACL 和时间戳。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 453a6f9c83b992df62681f366fcf95a77cda9f25
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107795767"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>使用 Azure DataBox 将数据批量迁移到 Azure 文件同步
可以通过两种方式将数据批量迁移到 Azure 文件同步：

* **使用 Azure 文件同步上传文件。** 这是最简单的方法。 将文件本地移动到 Windows Server 2012 R2 或更高版本，然后安装 Azure 文件同步代理。 设置同步后，将从服务器上传文件。 （目前客户的平均上传速度为每 2 天大约 1 TiB。）为了确保服务器不会占用数据中心太多的带宽，你可能需要设置一个[带宽限制计划](file-sync-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)。
* **脱机传输文件。** 如果没有足够的带宽，可能无法在合理的时间内将文件上传到 Azure。 这里要克服的难题是整个文件集的初始同步。 使用脱机批量迁移工具（例如 [Azure Data Box 系列](https://azure.microsoft.com/services/storage/databox)）可解决这一难题。 

本文介绍如何以与 Azure 文件同步兼容的方式脱机迁移文件。请按照这些说明操作，以避免文件冲突，并在启用同步后保留文件和文件夹的访问控制列表 (ACL) 和时间戳。

## <a name="migration-tools"></a>迁移工具
本文介绍的过程不仅适用于 Data Box，还适用于其他脱机迁移工具。 它还适用于 AzCopy、Robocopy 等工具，或直接在 Internet 上使用的合作伙伴工具和服务。 但是，若要解决最初的上传难题，请按照本文中的步骤操作，以与 Azure 文件同步兼容的方式使用这些工具。

在某些情况下，你需要在采用 Azure 文件同步之前从一台 Windows Server 迁移到另一台 Windows Server。[存储迁移服务](/windows-server/storage/storage-migration-service/overview) (SMS) 可为此提供帮助。 无论你是需要迁移到 Azure 文件同步支持的服务器操作系统版本（Windows Server 2012 R2 和更高版本），还是仅仅因为要为 Azure 文件同步购买一个新系统而需要迁移，SMS 都提供了许多功能和优势，可帮助你顺利完成迁移。

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>使用工具脱机传输数据的优点
使用传输工具（例如 Data Box）进行脱机迁移的主要优点如下：

- 无需通过网络上传所有文件。 对于大型命名空间，此工具可以节省大量的网络带宽和时间。
- 当你使用 Azure 文件同步时，无论你使用哪种传输工具（Data Box、Azure 导入/导出服务等等），实时服务器只会上传在你将数据移至 Azure 后更改的文件。
- 即使脱机批量迁移工具不传输 ACL，Azure 文件同步也会同步文件和文件夹 ACL。
- Data Box 和 Azure 文件同步不需要经历停机时间。 当你使用 Data Box 将数据传输到 Azure 中时，可以高效地使用网络带宽并保留文件的保真度。 此外，你还可以通过仅上传在你将数据移动到 Azure 后发生更改的文件，使命名空间保持最新。

## <a name="prerequisites-for-the-offline-data-transfer"></a>脱机数据传输的先决条件
在完成脱机数据传输之前，不应在要迁移的服务器上启用同步。 在开始之前，还要考虑以下事项：

- 如果你计划使用 Data Box 进行批量迁移：请查看 [Data Box 部署先决条件](../../databox/data-box-deploy-ordered.md#prerequisites)。
- 规划最终 Azure 文件同步拓扑：[规划 Azure 文件同步部署](file-sync-planning.md)
- 选择将保存要与其同步的文件共享的 Azure 存储帐户。 确保批量迁移发生在同一存储帐户中的临时暂存共享中。 批量迁移只能通过使用驻留在同一存储帐户中的最终共享和暂存共享启用。
- 仅在创建与服务器位置的新同步关系时，才能使用批量迁移。 无法使用现有同步关系启用批量迁移。


## <a name="process-for-offline-data-transfer"></a>脱机数据传输过程
本部分介绍如何以与 Azure Data Box 等批量迁移工具兼容的方式设置 Azure 文件同步：

![显示如何设置 Azure 文件同步的示意图](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 步骤 | 详细信息 |
|---|---------------------------------------------------------------------------------------|
| ![步骤 1](media/storage-sync-files-offline-data-transfer/bullet-1.png) | [订购 Data Box](../../databox/data-box-deploy-ordered.md)。 Data Box 系列提供[多种产品](https://azure.microsoft.com/services/storage/databox/data)来满足你的需求。 收到 Data Box 后，请按照其[文档，将数据复制](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)到 Data Box 上的此 UNC 路径：\\<DeviceIPAddres\>\<StorageAccountName_AzFile\>\<ShareName\>。 此处，ShareName  是暂存共享的名称。 将 Data Box 发送回 Azure。 |
| ![步骤 2](media/storage-sync-files-offline-data-transfer/bullet-2.png) | 等待文件显示在你选择作为临时暂存共享的 Azure 文件共享中。 请勿启用对这些共享的同步。 |
| ![步骤 3](media/storage-sync-files-offline-data-transfer/bullet-3.png) | <ul><li>为 Data Box 为你创建的每个文件共享创建一个空的新共享。 此新共享应与 Data Box 共享位于同一存储帐户中。 [如何创建新的 Azure 文件共享](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)。</li><li>在存储同步服务中[创建同步组](file-sync-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)。 引用空共享作为云终结点。 对每个 Data Box 文件共享重复此步骤。 [设置 Azure 文件同步](file-sync-deployment-guide.md)。</li></ul> |
| ![步骤 4](media/storage-sync-files-offline-data-transfer/bullet-4.png) | [将实时服务器目录添加为服务器终结点](file-sync-deployment-guide.md#create-a-server-endpoint)。 在此过程中，指定已将文件移动到 Azure，并引用暂存共享。 可以根据需要启用或禁用云分层。 在实时服务器上创建服务器终结点时，需要引用暂存共享。 在“添加服务器终结点”边栏选项卡上的“脱机数据传输”下，选择“已启用”，然后选择与云终结点必须位于同一存储帐户中的暂存共享。 此处可用共享列表按存储帐户和尚未同步的共享进行筛选。 此表后面的屏幕截图显示了如何在 Azure 门户中创建服务器终结点期间引用 DataBox 共享。 |
| ![步骤 5](media/storage-sync-files-offline-data-transfer/bullet-5.png) | 在上一步中添加服务器终结点后，数据开始自动从正确的源流出。 [同步共享](#syncing-the-share)部分介绍了数据何时从 DataBox 共享或从 Windows Server 流出 |
| |

![Azure 门户用户界面的屏幕截图，显示如何在创建新服务器终结点时启用脱机数据传输](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>同步共享
创建服务器终结点后，同步将开始。 同步过程确定服务器上的每个文件是否也存在于 Data Box 存储文件的暂存共享中。 如果文件存在于该位置，同步过程将从暂存共享复制文件，而不是从服务器上传该文件。 如果暂存共享中没有该文件，或者本地服务器上有较新版本，则同步过程将从本地服务器上传该文件。

同步共享时，同步将合并来自本地服务器上的文件变体中的任何缺失的文件属性、权限或时间戳，将它们与 DataBox 共享中的文件对应起来。 这可确保 Azure 文件共享中的每个文件和文件夹具有所有可能的文件保真度。

> [!IMPORTANT]
> 仅当创建服务器终结点时，才可以启用批量迁移模式。 在你建立了一个服务器终结点后，不能将批量迁移的数据从已在同步的服务器整合到命名空间中。

## <a name="acls-and-timestamps-on-files-and-folders"></a>文件和文件夹上的 ACL 和时间戳
Azure 文件同步可确保从实时服务器同步文件和文件夹 ACL，即使所用的批量迁移工具最初未传输 ACL 也是如此。 因此，暂存共享不需要包含文件和文件夹上的任何 ACL。 如果在创建新服务器终结点时启用脱机数据迁移功能，那么所有文件 ACL 都会在服务器上同步。 新创建和修改的时间戳也会同步。

## <a name="shape-of-the-namespace"></a>命名空间的形状
启用同步时，服务器的内容将确定命名空间的形状。 如果在 Data Box 快照和迁移完成之后从本地服务器上删除了文件，则不会将这些文件移动到实时同步命名空间中。 它们保留在暂存共享中，但不会被复制。 这是必需行为，因为同步根据实时服务器保留命名空间。 Data Box 快照只是高效文件复制的过渡方式。 它不是实时命名空间形状的颁发机构。

## <a name="cleaning-up-after-bulk-migration"></a>在批量迁移后进行清理 
当服务器完成命名空间的初始同步时，Data Box 的批量迁移的文件将使用暂存文件共享。 在 Azure 门户的“服务器终结点属性”边栏选项卡中，在“脱机数据传输”部分中，状态从“正在进行”更改为“已完成”。 

![“服务器终结点属性”边栏选项卡的屏幕截图，可在其中找到脱机数据传输的状态和禁用控件](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

现在，你可以清理暂存共享以节省成本：

1. 在“服务器终结点属性”边栏选项卡上，在状态为“已完成”时，选择“禁用脱机数据传输”。
2. 请考虑删除暂存共享以节省成本。 暂存共享可能不包含文件和文件夹 ACL，因此不太可能有用。 若要即时备份，请创建[正在同步的 Azure 文件共享的真实快照](../files/storage-snapshots-files.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)。 你可以[设置 Azure 备份以按计划创建快照]( ../../backup/backup-afs.md)。

仅当状态为“已完成”或由于配置错误而要取消时，才禁用脱机数据传输模式。 如果在部署过程中禁用该模式，即使暂存共享仍然可用，系统也将开始从服务器上传文件。

> [!IMPORTANT]
> 禁用脱机数据传输模式后，将无法再次启用它，即使批量迁移中的暂存共享仍可用也是如此。

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Azure 文件同步和云中预设定为种子的文件

如果通过非 DataBox 方式（例如通过 AzCopy、从云备份执行 RoboCopy 或任何其他方法）在 Azure 文件共享中设定了种子文件，则仍应按照本文中所述的[脱机数据传输过程](#process-for-offline-data-transfer)进行操作。 只需忽略 DataBox 作为将文件移动到云的方法。 但是，最好确保仍遵循在暂存共享（而非最终的 Azure 文件同步连接共享）中设定文件种子的过程进行操作。

> [!WARNING]
> 按照在暂存共享（而非最终的 Azure 文件同步连接共享）中设定文件种子的过程进行操作。 如果你不这样做，就会发生文件冲突（将同时存储两个文件版本），且在实时服务器上删除的文件可能会返回（如果这些文件仍然存在于较旧的、设定了种子的文件集中）。 此外，文件夹更改会相互合并，这使得在发生此类错误后，很难将命名空间分开。

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件同步部署](file-sync-planning.md)
- [部署 Azure 文件同步](file-sync-deployment-guide.md)