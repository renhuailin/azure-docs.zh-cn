---
title: 从快照还原应用
description: 了解如何从快照还原应用。 使用自动卷影副本从高级层中的意外数据丢失状况中恢复。
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 09/02/2021
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: 48b3d859a73a2d63644e1d5881c3505cee93f9d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679496"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>在 Azure 中从快照还原应用
本文介绍如何在 [Azure 应用服务](../app-service/overview.md)中从快照还原应用。 可以根据应用的某个快照将应用还原到以前的状态。 为实现数据恢复目的，平台会自动保存所有应用的快照，因而无需启用快照。

快照是应用服务应用的增量卷影副本。 当应用位于高级层或更高层时，应用服务会定期拍摄应用内容及其配置的快照。 相比[标准备份](manage-backup.md)，它们具有多个优点：

- 不会因文件锁定而造成文件复制错误。
- 最大快照大小更大 (30GB)。
- 支持的定价层无需任何配置。
- 快照可以还原到任何 Azure 区域的新应用服务应用。

从快照还原适用于在 **高级** 层或更高层中运行的应用。 有关向上缩放应用的信息，请参阅[在 Azure 中向上缩放应用](manage-scale-up.md)。

## <a name="limitations"></a>限制

- 快照还原支持的最大大小为 30GB。 如果存储大小大于 30GB，快照还原会失败。 例如，若要减小存储大小，请考虑将日志、图像、音频和视频等文件移动到 [Azure 存储](../storage/index.yml)。
- 快照中不包含[标准备份](manage-backup.md#what-gets-backed-up)支持的任何已连接数据库或[装载的 Azure 存储](configure-connect-to-azure-storage.md?pivots=container-windows)。 请考虑使用连接的 Azure 服务（例如 [SQL 数据库](../azure-sql/database/automated-backups-overview.md)和 [Azure 文件存储](../storage/files/storage-snapshots-files.md)）的本机备份功能。
- 应用服务在还原快照时会停止目标应用或目标槽。 若要最大限度减少生产应用的停机时间，请首先将快照还原到[过渡槽](deploy-staging-slots.md)，然后交换到生产槽。
- 最近 30 天的快照可用。 保留期和快照频率不可配置。
- 应用服务环境上运行的应用服务不支持快照。

## <a name="restore-an-app-from-a-snapshot"></a>从快照还原应用

1. 在 [Azure 门户](https://portal.azure.com)中应用的“设置”页上，单击“备份”以显示“备份”页  。 然后，在“快照(预览)”部分下单击“还原”。  
   
    ![屏幕截图：显示如何从快照还原应用。](./media/app-service-web-restore-snapshots/1.png)

2. 在“还原”页中选择要还原的快照。
   
    ![显示如何选择要还原的快照的屏幕截图。 ](./media/app-service-web-restore-snapshots/2.png)
   
3. 在“还原目标”中指定应用还原的目标。
   
    ![显示如何指定还原目标的屏幕截图。](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > 最佳做法是，我们建议还原到新槽，然后执行交换。 如果选择“覆盖”，则会清除并覆盖应用的当前文件系统中的所有现有数据。 在单击“确定”之前，请确保该操作是想要执行的操作。
   > 
   > 
      
   > [!Note]
   > 由于目前的技术限制，只能还原到同一缩放单元的应用。 将来的版本会取消此限制。
   > 
   > 
   
    可以选择要还原到槽的 **现有应用**。 在使用此选项之前，应该已在应用中创建一个槽。

4. 可以选择还原站点配置。
   
    ![显示如何还原站点配置的屏幕截图。](./media/app-service-web-restore-snapshots/4.png)

5. 单击“确定”。
