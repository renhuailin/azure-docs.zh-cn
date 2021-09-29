---
title: 块 blob 的时间点还原
titleSuffix: Azure Storage
description: 块 blob 的时间点还原支持将存储帐户还原到之前某个给定时间点的状态，以防止意外删除或损坏。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cecfd917146cd11399276ca706ddfa52c12b2c86
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596983"
---
# <a name="point-in-time-restore-for-block-blobs"></a>块 blob 的时间点还原

时间点还原支持将块 blob 数据还原到之前的状态，以防止意外删除或损坏。 当用户或应用程序意外删除数据或应用程序错误损坏数据时，时间点还原非常有用。 时间点还原还支持测试方案，这些方案需要在运行其他测试之前将数据集还原到已知状态。

仅标准性能层中的常规用途 v2 存储帐户支持时间点还原。 只有热访问层和冷访问层中的数据才能使用时间点还原进行还原。

若要了解如何对存储帐户启用时间点还原，请参阅[对块 blob 数据执行时间点还原](point-in-time-restore-manage.md)。

## <a name="how-point-in-time-restore-works"></a>时间点还原的工作原理

若要启用时间点还原，请为存储帐户创建管理策略并指定保持期。 在保持期内，可以将块 blob 从当前状态还原到上一个时间点的状态。

若要启动时间点还原，请调用[还原 Blob 范围](/rest/api/storagerp/storageaccounts/restoreblobranges)操作并指定 UTC 时间的还原点。 可以指定字典范围的容器和 blob 名称进行还原，或省略范围以还原存储帐户中的所有容器。 每个还原操作最多支持 10 个字典范围。

Azure 存储会分析在所请求的还原点（UTC 时间）和当前时间段内对指定的 blob 所做的所有更改。 还原操作是原子操作，因此要么完全还原所有更改，要么失败。 如果存在无法还原的 blob，则该操作将失败，并且对受影响的容器的读取和写入操作将继续。

下图介绍了时间点还原的工作原理。 一个或多个容器或 blob 范围已还原到其 n 天前的状态，其中 n 小于或等于为时间点还原定义的保持期。 其作用是还原在保持期内发生的写入和删除操作。

:::image type="content" source="media/point-in-time-restore-overview/point-in-time-restore-diagram.png" alt-text="显示时间点如何将容器还原到以前状态的关系图":::

一个存储帐户一次只能运行一个还原操作。 还原操作在执行过程中无法取消，但可以执行第二个还原操作来撤消第一个操作。

“还原 Blob 范围”操作将返回唯一标识操作的还原 ID。 若要检查时间点还原的状态，请使用从“还原 Blob 范围”操作返回的还原 ID 调用“获取还原状态”操作。

> [!IMPORTANT]
> 执行还原操作时，Azure 存储会阻止对在操作期间还原的范围内的 blob 执行数据操作。 读取、写入和删除操作在主位置中被阻止。 出于此原因，在执行还原操作时，Azure 门户中的操作（如列出容器）可能不会按预期执行。
>
> 如果存储帐户是异地复制的，则在还原操作期间，从辅助位置读取的操作可以继续。

> [!CAUTION]
> 时间点还原仅支持还原对块 blob 执行的操作。 不能还原对容器执行的任何操作。 例如，如果你通过调用[删除容器](/rest/api/storageservices/delete-container)操作从存储帐户中删除了容器，将无法使用时间点还原操作来还原该容器。 如果要在以后还原它们，请删除单个 blob，而不是删除整个容器。

### <a name="prerequisites-for-point-in-time-restore"></a>时间点还原的先决条件

执行时间点还原需要启用以下 Azure 存储功能，然后才能启用时间点还原：

- [软删除](soft-delete-blob-overview.md)
- [更改源](storage-blob-change-feed.md)
- [Blob 版本控制](versioning-overview.md)

启用这些功能可能会产生额外的费用。 在启用时间点还原和先决条件功能之前，请确保你了解计费含义。

### <a name="retention-period-for-point-in-time-restore"></a>时间点还原的保持期

为存储帐户启用时间点还原时，将指定一个保持期。 存储帐户中的块 blob 可以在保持期内还原。

保持期会在启用时间点还原后几分钟开始。 请记住，不能将 blob 还原到保持期开始之前的状态。 例如，如果在 5 月 1 日启用时间点还原，保持期为 30 天，则 5 月 15 日可以还原到最多 15 天。 6 月 1 日可以还原 1 到 30 天内的数据。

时间点还原的保持期必须至少比为软删除指定的保持期少一天。 例如，如果软删除保持期设置为 7 天，则时间点还原保持期可以介于 1 到 6 天。

> [!IMPORTANT]
> 还原一组数据所用的时间取决于在还原期间执行的写入和删除操作的次数。 例如，如果一个帐户有 1 百万个对象，每天增加 3,000 个对象，每天删除 1,000 个对象，将需要大约两个小时才能还原到过去 30 天的点。 对于具有此变化率的帐户，不建议在过去保留时间和还原超过90天。

### <a name="permissions-for-point-in-time-restore"></a>时间点还原权限

若要启动还原操作，客户端必须对存储帐户中的所有容器具有写入权限。 若要使用 Azure Active Directory (Azure AD) 授予授权还原操作的权限，请将“存储帐户参与者”角色分配给存储帐户、资源组或订阅级别的安全主体。

## <a name="limitations-and-known-issues"></a>限制和已知问题

块 blob 的时间点还原具有以下限制和已知问题：

- 在时间点还原操作过程中，只能还原标准常规用途 v2 存储帐户中的块 blob。 不会还原追加 blob、页 blob 和高级块 blob。
- 如果在保持期内删除了某个容器，则该容器将无法使用时间点还原操作进行还原。 如果尝试还原的 blob 范围包含已删除的容器中的 blob，则时间点还原操作将失败。 若要了解如何防止删除容器，请参阅[容器软删除](soft-delete-container-overview.md)。
- 如果在当前时间与还原点之间的时间段内，blob 已移动到热层和冷层之间，则会将 blob 还原到其以前的层。 不支持还原存档层中的块 blob。 例如，如果热层中的某一 blob 在两天前已移至存档层，而还原操作还原至三天前的某个时间点，则该 blob 不会被还原至热层。 若要还原已存档的 blob，请先将其从存档层移开。 有关详细信息，请参阅[存档层中的 blob 解除冻结概述](archive-rehydrate-overview.md)。
- 如果配置了不可变策略，则可以启动还原操作，但任何受不可变策略保护的 blob 不会被修改。 在这种情况下，还原操作不会导致将一致状态还原到给定的日期和时间。
- 通过 [Put Block](/rest/api/storageservices/put-block) 或 [Put Block from URL](/rest/api/storageservices/put-block-from-url) 上传、但未通过 [Put Block List](/rest/api/storageservices/put-block-list) 提交的块不是 blob 的一部分，因此不会在还原操作过程中还原。
- 无法还原具有活动租约的 blob。 如果要还原的 blob 范围内包含具有活动租约的 blob，则还原操作将自动失败。 在启动还原操作之前，中断任何活动租约。
- 在还原操作过程中，不会创建或删除快照。 仅可将基本 blob 还原到其以前的状态。
- 不支持还原 Azure Data Lake Storage Gen2 平面和分层命名空间。

> [!IMPORTANT]
> 如果将块 blob 还原到早于 2020 年 9 月 22 日的点，则时间点还原的预览版限制将有效。 Microsoft 建议你选择一个等于或晚于 2020 年 9 月 22 日的还原点，以利用正式发布的时间点还原功能。

## <a name="feature-support"></a>功能支持

此表显示了你的帐户如何支持此功能，以及启用某些功能时对支持的影响。

| 存储帐户类型                | Blob 存储（默认支持）   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 标准常规用途 v2 | ![是](../media/icons/yes-icon.png) |![否](../media/icons/no-icon.png)              | ![否](../media/icons/no-icon.png) |
| 高级块 blob          | ![否](../media/icons/no-icon.png)|![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 和网络文件系统 (NFS) 3.0 协议都需要已启用分层命名空间的存储帐户。

## <a name="pricing-and-billing"></a>定价和计费

启用时间点还原不收取任何费用。 但是，启用时间点还原还会启用 blob 版本控制、软删除和更改源，其中每个都可能产生额外的费用。

对时间点还原的计费取决于为执行还原操作而处理的数据量。 处理的数据量取决于还原点和当前时间之间发生的更改数。 例如，假设存储帐户中块 blob 数据的变化率相对恒定，则还原到 1 天前的还原操作所花的费用是还原到 10 天前的还原操作的 1/10。

若要估计还原操作的成本，请查看更改源日志，估计在还原期间修改的数据量。 例如，如果更改源的保持期为 30 天，更改源的大小为 10 MB，则还原到 10 天前的点所花的费用大约是该区域 LRS 帐户列出的价格的 1/3。 如果还原到 27 天前的点，则成本大约是所列价格的 9/10。

有关时间点还原定价的详细信息，请参阅[块 Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="next-steps"></a>后续步骤

- [对块 blob 数据执行时间点还原](point-in-time-restore-manage.md)
- [Azure Blob 存储中的更改源支持](storage-blob-change-feed.md)
- [为 blob 启用软删除](./soft-delete-blob-enable.md)
- [启用和管理 blob 版本控制](versioning-enable.md)
