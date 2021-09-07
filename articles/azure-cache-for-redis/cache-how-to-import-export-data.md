---
title: 在 Azure Redis 缓存中导入和导出数据
description: 了解如何使用高级 Azure Redis 缓存实例在 blob 存储中导入和导出数据
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 0430217ab8c2f113127c6a2e519d9e394fc3d3e7
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123318674"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>在 Azure Redis 缓存中导入和导出数据

导入/导出是一项 Azure Cache for Redis 数据管理操作。 使用此操作可以通过从高级缓存导入 Azure Cache for Redis 数据库 (RDB) 快照以及将 Azure Cache for Redis 数据库 (RDB) 快照导出到 Azure 存储帐户中的 Blob，来相应地将数据导入到 Azure Cache for Redis 以及从 Azure Cache for Redis 导出数据。

- 导出  - 可以将 Azure Redis 缓存 RDB 快照导出到页 Blob。
- 导入  - 可以从页 Blob 或块 Blob 导入 Azure Redis 缓存 RDB 快照。

通过导入/导出可在不同的 Azure Redis 缓存实例之间进行迁移，或者在使用之前使用数据填充缓存。

本文提供使用 Azure Redis 缓存导入和导出数据的指南，并提供常见问题的解答。

> [!IMPORTANT]
> 导入/导出仅适用于[高级层](cache-overview.md#service-tiers)缓存。
>
>

## <a name="import"></a>Import

使用导入可从任何云或环境中运行的任何 Redis 服务器（包括 Linux、Windows 或者 Amazon Web Services 等任何云平台中运行的 Redis）引入与 Redis 兼容的 RDB 文件。 导入数据是使用预先填充的数据创建缓存的简单方式。 在导入过程中，Azure Redis 缓存从 Azure 存储将 RDB 文件加载到内存中，再将密钥插入到缓存中。

> [!NOTE]
> 在开始导入操作之前，请确保 Redis 数据库 (RDB) 文件已上传到 Azure 存储的页 blob 或块 blob 中，并与 Azure Redis 缓存实例在同一区域和订阅中。 有关详细信息，请参阅 [Azure Blob 存储入门](../storage/blobs/storage-quickstart-blobs-dotnet.md)。 如果已使用 [Azure Redis 缓存导出](#export)功能导出 RDB 文件，则 RDB 文件已存储在页 blob 中并已准备好进行导入。
>
>

1. 若要导入一个或多个导出的缓存 Blob，请在 Azure 门户中[浏览到你的缓存](cache-configure.md#configure-azure-cache-for-redis-settings)，然后在“资源”菜单中选择“导入数据” 。

    ![导入数据](./media/cache-how-to-import-export-data/cache-import-data.png)
2. 选择“选择 Blob”，然后选择包含要导入的数据的存储帐户。

    ![选择存储帐户](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. 选择包含要导入的数据的容器。

    ![选择容器](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. 通过选择 Blob 名称左侧的区域选择要导入的一个或多个 Blob，然后选择“选择”。

    ![选择 blob](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. 选择“导入”开始导入过程。

   > [!IMPORTANT]
   > 在导入过程中，缓存客户端无法访问该缓存，并且将删除该缓存中的任何现有数据。
   >
   >

    ![Import](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    可以通过关注 Azure 门户中的通知或通过查看[审核日志](../azure-resource-manager/management/view-activity-logs.md)中的事件，来监视导入操作的进度。

    ![导入进度](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Export

使用导出可以将 Azure Redis 缓存中存储的数据导出到与 Redis 兼容的 RDB 文件。 可以使用此功能将一个 Azure Redis 缓存实例中的数据移到另一个 Azure Redis 缓存实例或另一个 Redis 服务器。 在导出过程中，会在托管 Azure Cache for Redis 服务器实例的 VM 上创建临时文件。 然后，该文件将上传到所选的存储帐户。 导出操作完成后，无论状态为成功还是失败，都会删除临时文件。

1. 若要将缓存的当前内容导出到存储，请在 Azure 门户中[浏览到你的缓存](cache-configure.md#configure-azure-cache-for-redis-settings)，然后在“资源”菜单中选择“导出数据” 。

    ![在 contoso5premium 的导航窗格中，会突出显示“管理”列表中的“导出数据”选项。](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. 选择“选择存储容器”并选择所需的存储帐户。 存储帐户必须与缓存在同一订阅和区域中。

   > [!IMPORTANT]
   >
   > - 导出操作适用于页 Blob，经典存储帐户和资源管理器存储帐户都支持页 Blob。
   > - Azure Cache for Redis 不支持导出到 ADLS Gen2 存储帐户。
   > - Blob 存储帐户目前不支持导出。
   >
   > 有关详细信息，请参阅 [Azure 存储帐户概述](../storage/common/storage-account-overview.md)。
   >

    ![存储帐户](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. 选择所需的 Blob 容器，然后选择“选择”。 若要使用新容器，请选择“添加容器”以先添加该容器，然后从列表中选择该容器。

    ![在 contoso55 的“容器”中，会突出显示“+ 容器”选项。 列表中有一个容器 (cachesaves)，该容器已选中并突出显示。 选中并突出显示了“选择”选项。](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. 键入一个 Blob 名称前缀，然后选择“导出”开始导出过程 。 blob 名称前缀用作此导出操作生成的文件名的前缀。

    ![Export](./media/cache-how-to-import-export-data/cache-export-data.png)

    可通过关注 Azure 门户中的通知或查看[审核日志](../azure-resource-manager/management/view-activity-logs.md)中的事件，来监视导出操作的进度。

    ![导出数据完成](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    在导出过程中，缓存仍可供使用。

## <a name="importexport-faq"></a>导入/导出常见问题解答

本部分包含有关导入/导出功能的常见问题解答。

- [哪些定价层可以使用导入/导出？](#what-pricing-tiers-can-use-importexport)
- [能否从任何 Redis 服务器导入数据？](#can-i-import-data-from-any-redis-server)
- [可以导入哪些 RDB 版本？](#what-rdb-versions-can-i-import)
- [导入/导出操作期间缓存是否可用？](#is-my-cache-available-during-an-importexport-operation)
- [能否对 Redis 群集使用导入/导出？](#can-i-use-importexport-with-redis-cluster)
- [导入/导出如何与自定义数据库设置结合使用？](#how-does-importexport-work-with-a-custom-databases-setting)
- [导入/导出与 Redis 持久性有何区别？](#how-is-importexport-different-from-redis-persistence)
- [能否使用 PowerShell、CLI 或其他管理客户端自动执行导入/导出？](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
- [我在导入/导出操作期间收到超时错误。它意味着什么？](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
- [在将我的数据导出到 Azure Blob 存储时收到错误。发生了什么情况？](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>哪些定价层可以使用导入/导出？

导入/导出仅在高级定价层中可用。

### <a name="can-i-import-data-from-any-redis-server"></a>能否从任何 Redis 服务器导入数据？

是的，既可以导入从 Azure Cache for Redis 实例导出的数据，也可以从任何云或环境中运行的任何 Redis 服务器导入 RDB 文件。 这些环境包括 Linux、Windows 或 Amazon Web Services 等云平台。 若要导入此数据，请从所需的 Redis 服务器将 RDB 文件上传到 Azure 存储帐户中的页 Blob 或块 Blob。 然后将其导入到高级 Azure Cache for Redis 实例中。 例如，你可能想要从生产缓存导出数据，并将其导入到用作过渡环境的一部分的缓存，用于测试或迁移。

> [!IMPORTANT]
> 使用页 blob 时，要成功导入从非 Azure Redis 缓存的 Redis 服务器导出的数据，页 blob 大小必须在 512 字节边界上对齐。 有关用于执行任何所需的字节填充的示例代码，请参阅[示例页 blob 上传](https://github.com/JimRoberts-MS/SamplePageBlobUpload)。
>
>

### <a name="what-rdb-versions-can-i-import"></a>可以导入哪些 RDB 版本？

对于 RDB 版本 7，Azure Redis 缓存支持 RDB 导入。

### <a name="is-my-cache-available-during-an-importexport-operation"></a>导入/导出操作期间缓存是否可用？

- **导出** - 缓存保持可用，可以在导出操作过程中继续使用缓存。
- **导入** - 在导入操作开始时，缓存即变为不可用，在导入操作完成后，缓存变为可供使用。

### <a name="can-i-use-importexport-with-redis-cluster"></a>能否对 Redis 群集使用导入/导出？

能，并且可以在群集缓存和非群集缓存之间导入/导出。 由于 Redis 群集[仅支持数据库 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)，因此将不会导入 0 以外的数据库中的任何数据。 导入群集缓存数据时，密钥会在群集的分片之间重新分发。

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>导入/导出如何可用于自定义数据库设置？

某些定价层具有不同的[数据库限制](cache-configure.md#databases)，因此，如果在缓存创建过程中为 `databases` 设置配置了自定义值，则在导入时需注意一些注意事项。

- 当导入到的定价层的 `databases` 限制低于导出层的相应限制时：
  - 如果使用的是默认 `databases` 数（对于所有定价层来说为 16），则不会丢失数据。
  - 如果使用的是在要导入到的层的限制内的自定义 `databases` 数，则不会丢失数据。
  - 如果导出的数据包含超出新层限制的数据库中的数据，则将不会导入这些更高版本数据库中的数据。

### <a name="how-is-importexport-different-from-redis-persistence"></a>导入/导出与 Redis 持久性有何区别？

Azure Redis 缓存暂留能够将 Redis 中存储的数据长期保存在 Azure 存储中。 配置持久性以后，Azure Cache for Redis 会按照可配置的备份频率，将缓存数据的快照以 Redis 二进制格式持久保存在磁盘上。 如果发生了灾难性事件，导致主缓存和副本缓存都无法使用，则会使用最新快照自动还原缓存数据。 有关详细信息，请参阅[如何为高级 Azure Redis 缓存配置数据持久性](cache-how-to-premium-persistence.md)。

使用导入/导出，可以在 Azure Redis 缓存中引入或导出数据。 它不使用 Redis 持久性配置备份和还原。

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>能否使用 PowerShell、CLI 或其他管理客户端自动执行导入/导出？

是，有关 PowerShell 说明，请参阅[导入 Azure Redis 缓存](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis)和[导出 Azure Redis 缓存](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis)。

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>在导入/导出操作期间收到超时错误。 它意味着什么？

如果在启动操作之前停留在左侧“导入数据”或“导出数据”的时间超过 15 分钟，则会收到错误，错误消息类似于以下示例 ：

```azcopy
The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.
```

若要解决此错误，请在 15 分钟过去之前启动导入或导出操作。

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>在将数据导出到 Azure Blob 存储时遇到错误。 发生了什么情况？

导出仅适用于以页 blob 形式存储的 RDB 文件。 目前不支持其他 Blob 类型，包括带有热层和冷层的 Blob 存储帐户。 有关详细信息，请参阅 [Azure 存储帐户概述](../storage/common/storage-account-overview.md)。

## <a name="next-steps"></a>后续步骤

了解有关 Azure Cache for Redis 功能的详细信息。

- [Azure Cache for Redis 服务层](cache-overview.md#service-tiers)
