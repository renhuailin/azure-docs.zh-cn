---
title: 存档层中的 Blob 解除冻结概述
description: 当 Blob 位于存档访问层时，它被视为处于脱机状态，无法读取或修改。 若要读取或修改存档 Blob 中的数据，须先将 Blob 解除冻结到联机层，即热层或冷层。
services: storage
author: tamram
ms.author: tamram
ms.date: 08/31/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: 2c4eac524ecda8a2b90036748fd2a6f2a389a3cd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823733"
---
# <a name="overview-of-blob-rehydration-from-the-archive-tier"></a>存档层中的 Blob 解除冻结概述

当 Blob 位于存档访问层时，它被视为处于脱机状态，无法读取或修改。 若要读取或修改存档 Blob 中的数据，须先将 Blob 解除冻结到联机层，即热层或冷层。 提供两个选项解除冻结存储在存档层中的 Blob：

- [将存档的 Blob 复制到联机层](#copy-an-archived-blob-to-an-online-tier)：可以使用[复制 Blob](/rest/api/storageservices/copy-blob) 或[从 URL 复制 Blob](/rest/api/storageservices/copy-blob-from-url) 操作将存档的 Blob 复制到热或冷存储层中的新 Blob，从而解除冻结存档的 Blob。 Microsoft 建议在大多数方案中使用此选项。

- [将 Blob 的访问层更改为联机层](#change-a-blobs-access-tier-to-an-online-tier)：可以使用[设置 Blob 层](/rest/api/storageservices/set-blob-tier)操作来更改 Blob 的访问层，从而将存档的 Blob 解除冻结到热或冷存储层。

从存档层中解除冻结 Blob 可能需要几个小时才能完成。 Microsoft 建议解除冻结较大的 Blob，以获得最佳性能。 同时解除冻结多个小型 Blob 可能导致该时间延长。

可配置 [Azure 事件网格](../../event-grid/overview.md)，以在将 Blob 从存档层解除冻结到联机层时引发事件，并将事件发送到事件处理程序。 有关详细信息，请参阅[处理 Blob 解除冻结事件](#handle-an-event-on-blob-rehydration)。

若要详细了解有关 Azure 存储中的访问层，请参阅 [Azure Blob 存储的访问层 - 热、冷以及存档](storage-blob-storage-tiers.md)。

## <a name="rehydration-priority"></a>解除冻结优先级

在解除冻结 Blob 时，可通过[设置 Blob 层](/rest/api/storageservices/set-blob-tier)或[复制 Blob](/rest/api/storageservices/copy-blob)/[从 URL 复制 Blob](/rest/api/storageservices/copy-blob-from-url) 操作上可选的“x-ms-rehydrate-priority 标头”，设置解除冻结操作的优先级。 解除冻结优先级选项包括：

- **标准优先级**：解冻请求将按其接收顺序处理，最长可能需要 15 个小时。
- **高优先级**：解除冻结请求的优先级高于标准优先级的请求，对于大小不到 10 GB 的对象，可在 1 小时内完成。

若要在执行解除冻结期间查看解除冻结优先级，请调用[获取 Blob 属性](/rest/api/storageservices/get-blob-properties)以返回 `x-ms-rehydrate-priority` 标头的值。 解除冻结优先级属性会返回“标准”或“高” 。

标准优先级是默认解除冻结选项。 高优先级解除冻结速度更快，但成本高于标准优先级的解除冻结。 高优先级解除冻结可能需要花费 1 小时以上，具体取决于 Blob 大小和当前需求。 Microsoft 建议保留高优先级解除冻结，以用于需要紧急还原数据的情况。

有关标准优先级与高优先级解除冻结请求的定价差异的详细信息，请参阅 [Azure Blob 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="copy-an-archived-blob-to-an-online-tier"></a>将存档的 Blob 复制到联机层

将 Blob 从存档层移动到联机层的第一个选项是，将存档的 Blob 复制到位于热或冷层中的新目标 Blob。 可使用[复制 Blob](/rest/api/storageservices/copy-blob) 或[从 URL 复制 Blob](/rest/api/storageservices/copy-blob-from-url) 操作来复制 Blob。 将存档的 Blob 复制到联机层上的新 Blob 时，源 Blob 在存档层中会保持未修改状态。

必须将存档的 Blob 复制到名称不同的新 Blob 或另一个容器。 无法通过复制到同一 Blob 来覆盖源 Blob。

对于大多数需要将 Blob 从存档层移动到联机层的方案中，Microsoft 均建议执行复制操作，原因如下：

- 复制操作可避免在要求的 180 天期限到期之前更改存档层中的 Blob 层时评估的提前删除费用。 有关详细信息，请参阅[存档访问层](storage-blob-storage-tiers.md#archive-access-tier)。
- 如果针对存储帐户存在一个有效的生命周期管理策略，则使用[设置 Blob 层](/rest/api/storageservices/set-blob-tier)解除冻结 Blob 后，会导致生命周期策略将 Blob 移回存档层的情况，因为上次的修改时间超出了此策略设置的阈值。 复制操作将源 Blob 保留在存档层中，并使用不同的名称和新的上次修改时间创建一个新的 Blob，这样，生命周期策略就不会将已解除冻结的 Blob 移回存档层。

复制存档层中的 Blob 可能需要数小时才能完成，具体取决于所选的解除冻结优先级。 Blob 复制操作还会秘密读取已存档的源 Blob，从而在所选目标层中创建新的联机 Blob。 在解除冻结操作完成之前，列出父容器中的 Blob 时，可能会显示新的 Blob，但其层将设置为存档。对存档层中源 Blob 的读取操作完成且 Blob 的内容写入联机层中的新目标 Blob 之后，数据才可用。 新 Blob 是独立的副本，因此修改或删除它不会影响存档层中的源 Blob。

若要了解如何通过将 Blob 复制到联机层来解除冻结 Blob，请参阅[使用复制操作解除冻结 Blob](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation)。

> [!IMPORTANT]
> 成功完成解除冻结之前，不要删除源 Blob。 如果删除源 Blob，则目标 Blob 可能无法完成复制。 可处理复制操作完成时所引发的事件，以了解何时可以安全删除源 Blob。 有关详细信息，请参阅[处理 Blob 解除冻结事件](#handle-an-event-on-blob-rehydration)。

仅支持在同一存储帐户中将存档的 Blob 复制到联机目标层。 无法将存档的 Blob 复制到同样位于存档层的目标 Blob。

下表显示了 Blob 复制操作的行为，具体取决于源 Blob 和目标 Blob 的层。

|  | **热层源** | **冷层源** | **存档层源** |
|--|--|--|--|
| **热层目标** | 支持 | 支持 | 在同一帐户中受支持。 需要 Blob 解除冻结。 |
| **冷层目标** | 支持 | 支持 | 在同一帐户中受支持。 需要 Blob 解除冻结。 |
| **存档层目标** | 支持 | 支持 | 不支持 |

## <a name="change-a-blobs-access-tier-to-an-online-tier"></a>将 Blob 的访问层更改到联机层

将 Blob 从存档层解除冻结到联机层的第二个选项是，调用[设置 Blob 层](/rest/api/storageservices/set-blob-tier)来更改 Blob 的层。 通过此操作，可将存档 Blob 的层更改为热或冷层。

一旦发起[设置 Blob 层](/rest/api/storageservices/set-blob-tier)请求后便无法取消。 在解除冻结操作期间，Blob 的访问层设置继续显示为存档，直到解除冻结过程完成为止。 完成解除冻结操作后，Blob 的访问层属性会更新，以反映新的层。

若要了解如何通过将 Blob 层更改为联机层来解除冻结 Blob，请参阅[通过更改 Blob 层解除冻结 Blob](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-by-changing-its-tier)。

> [!CAUTION]
> 更改 Blob 的层不会影响其上次的修改时间。 如果针对存储帐户存在一个有效的[生命周期管理](./lifecycle-management-overview.md)策略，则使用“设置 Blob 层”解除冻结 Blob 后，会导致生命周期策略将 Blob 移回存档层的情况，因为上次的修改时间超出了此策略设置的阈值。
>
> 若要避免这种情况，请按[将存档的 Blob 复制到联机层](#copy-an-archived-blob-to-an-online-tier)部分所述，改为复制存档的 Blob 来将其解除冻结。 执行复制操作会创建该 Blob 的新实例并更新其上次修改时间，因此不会触发生命周期管理策略。

## <a name="check-the-status-of-a-blob-rehydration-operation"></a>检查 Blob 解除冻结操作的状态

在 Blob 解除冻结操作期间，可调用[获取 Blob 属性](/rest/api/storageservices/get-blob-properties)操作来检查其状态。 请参阅[检查解除冻结操作的状态](archive-rehydrate-to-online-tier.md#check-the-status-of-a-rehydration-operation)了解此操作的状态。

## <a name="handle-an-event-on-blob-rehydration"></a>处理 blob 解除冻结事件

解除冻结存档的 Blob 最多可能需要 15 小时，若反复轮询“获取 Blob 属性”来确定解除冻结是否完成，效率很低。 使用 [Azure 事件网格](../../event-grid/overview.md)捕获在解除冻结完成时触发的事件，能够提高性能并优化成本。

Azure 事件网格引发以下两个 Blob 解除冻结事件之一，具体取决于用于解除冻结 Blob 的操作：

- 创建 Blob 时引发“Microsoft.Storage.BlobCreated”事件。 在 Blob 解除冻结上下文中，当[复制 Blob](/rest/api/storageservices/copy-blob) 或[从 URL 复制 Blob](/rest/api/storageservices/copy-blob-from-url) 操作在热或冷层中创建新的目标 Blob，并已从存档层完全解除冻结 Blob 的数据时，将触发此事件。
- 更改 Blob 层时引发“Microsoft.Storage.BlobTierChanged”事件。 在 Blob 解除冻结上下文中，当[设置 Blob 层](/rest/api/storageservices/set-blob-tier)操作成功将存档 Blob 的层更改为热或冷层时，将触发此事件。

若要了解如何捕获有关解除冻结的事件并将其发送到 Azure 函数事件处理程序，请参阅[运行 Azure 函数以响应 Blob 解除冻结事件](archive-rehydrate-handle-event.md)。

有关如何处理 Blob 存储事件的详细信息，请参阅[响应 Azure Blob 存储事件](storage-blob-event-overview.md)和[将 Azure Blob 存储用作事件网格源](../../event-grid/event-schema-blob-storage.md)。

## <a name="pricing-and-billing"></a>定价和计费

使用[设置 Blob 层](/rest/api/storageservices/set-blob-tier)进行解除冻结操作将按数据读取事务数和数据检索大小来计费。 与标准优先级相比，高优先级解除冻结的操作和数据检索成本更高。 高优先级解冻在帐单上单独显示费用细目。 如果一个高优先级请求返回一个几 GB 的归档 Blob 需要超过 5 个小时，则不会向你收取高优先级检索费率的费用。 不过，标准检索费率仍适用。

使用[复制 Blob](/rest/api/storageservices/copy-blob) 或[从 URL 复制 Blob](/rest/api/storageservices/copy-blob-from-url) 将存档 Blob 复制到联机层将按数据读取事务数和数据检索大小来计费。 在联机层中创建目标 Blob 将按数据写入事务数来计费。 复制到联机 Blob 时，不会产生提前删除费，因为源 Blob 在存档层中保持未修改状态。 如果选择此选项，则将支付高优先级检索费用。

存档层中的 Blob 应至少存储 180 天。 在 180 天期限到期之前删除或更改存档 Blob 的层会产生提前删除费。 有关详细信息，请参阅[存档访问层](storage-blob-storage-tiers.md#archive-access-tier)。

有关块 Blob 和数据解冻的定价详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。 有关出站数据传输费的详细信息，请参阅[数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)。

## <a name="see-also"></a>另请参阅

- [Azure Blob 存储：热、冷以及存档访问层](storage-blob-storage-tiers.md)。
- [将存档的 Blob 解冻到联机层](archive-rehydrate-to-online-tier.md)
- [运行 Azure 函数以响应 Blob 解除冻结事件](archive-rehydrate-handle-event.md)
- [响应 Blob 存储事件](storage-blob-event-overview.md)