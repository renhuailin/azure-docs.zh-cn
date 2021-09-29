---
title: 数据保护概述
titleSuffix: Azure Storage
description: 适用于 blob 存储和 Azure Data Lake Storage Gen2 数据的数据保护选项使你能够保护数据不被删除或覆盖。 如果你需要恢复已删除或覆盖的数据，则本指南可帮助你选择最适合你的方案的恢复选项。
services: storage
author: tamram
ms.service: storage
ms.date: 07/30/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: d9beb1ce981826eb7555b1a55e00c7c00fb84810
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766609"
---
# <a name="data-protection-overview"></a>数据保护概述

Azure 存储为 blob 存储和 Azure Data Lake Storage Gen2 提供数据保护，在你需要恢复已删除或覆盖的数据的情况下可以帮助你做好准备。 在发生了可能导致数据泄露的事件后，应考虑如何最充分地保护数据，这一点很重要。 本指南可帮助你提前决定方案所需的数据保护功能以及如何实现这些功能。 如果你需要恢复已删除或覆盖的数据，本概述还根据你的方案提供了有关如何继续操作的指导。

在 Azure 存储文档中，数据保护指的是在删除或修改存储帐户和存储帐户中的数据，或者在删除或修改数据后还原数据的策略。 Azure 存储还提供“灾难恢复”选项，其中包括多个冗余级别，以保护你的数据免受硬件问题或自然灾害导致的服务中断，以及在主要区域中的数据中心不可用的情况下进行客户管理的故障转移。 有关如何保护数据以防止服务中断的详细信息，请参阅[灾难恢复](#disaster-recovery)。

## <a name="recommendations-for-basic-data-protection"></a>基本数据保护方面的建议

如果你正在查找存储帐户的基本数据保护覆盖范围以及它所包含的数据，Microsoft 建议采用以下步骤开始：

- 在存储帐户上配置 Azure 资源管理器锁定，以防止帐户被删除或配置发生变化。 [了解详细信息...](../common/lock-account-resource.md)
- 为存储帐户启用容器软删除，以恢复已删除的容器及其内容。 [了解详细信息...](soft-delete-container-enable.md)
- 按固定间隔保存 blob 的状态：
  - 对于 blob 存储工作负载，每次覆盖 blob 时，启用 blob 版本控制以自动保存数据的状态。 [了解详细信息...](versioning-enable.md)
  - 对于 Azure Data Lake Storage 工作负载，请生成手动快照，以保存特定的时间点的数据状态。 [了解详细信息...](snapshots-overview.md)

下一节将更详细地介绍这些选项以及其他方案的其他数据保护选项。

有关这些功能涉及的成本的概述，请参阅[成本注意事项汇总](#summary-of-cost-considerations)。

## <a name="overview-of-data-protection-options"></a>数据保护选项概述

下表总结了 Azure 存储中可用于常见数据保护方案的选项。 选择适用于你的情况的方案，以详细了解可供你使用的选项。 请注意，并非所有功能都适用于启用了分层命名空间的存储帐户。

| 方案 | 数据保护选项 | 建议 | 保护权益 | 可用于 Data Lake Storage |
|--|--|--|--|--|
| 阻止删除或修改存储帐户。 | Azure 资源管理器锁定<br />[了解详细信息...](../common/lock-account-resource.md) | 使用 Azure 资源管理器锁定锁定所有存储帐户，以防止删除存储帐户。 | 防止存储帐户被删除或配置被更改。<br /><br />无法防止帐户中的容器或 blob 被删除或覆盖。 | 是 |
| 在你控制的时间间隔防止删除 blob 版本。 | blob 版本上的不可变性策略（预览版）<br />[了解详细信息...](immutable-storage-overview.md) | 为了满足法律或法规遵从性要求等目的，在单个 blob 版本上设置一个不可变性策略以保护业务关键文档。 | 防止删除 blob 版本和覆盖其元数据。 每个覆盖操作都会创建一个新版本。<br /><br />如果至少有一个容器启用了版本级不可变性，存储帐户也会受到保护，无法删除。 如果容器中至少存在一个 blob，则容器删除将失败。 | 否 |
| 在你控制的时间间隔防止容器及其 blob 被删除或修改。 | 容器上的不可变性策略<br />[了解详细信息...](immutable-storage-overview.md) | 在容器上设置一个不可变性策略以保护关键业务文档，例如，为了满足法律或法规遵从性要求。 | 防止容器及其 blob 被删除和覆盖。<br /><br />当合法保留或锁定的基于时间的保留策略生效时，存储帐户也会受到保护，无法删除。 未设置任何永久性策略的容器不会受到防删除的保护。 | 是，处于预览状态 |
| 在指定的时间间隔内还原已删除的容器。 | 容器软删除<br />[了解详细信息...](soft-delete-container-overview.md) | 为所有存储帐户启用容器软删除，最短保留间隔为 7 天。<br /><br />与容器软删除一起启用 blob 版本控制和 blob 软删除，以保护容器中的各个 blob。<br /><br />在单独的存储帐户中存储需要不同保留期的容器。 | 删除的容器及其内容在保留期内可以被还原。<br /><br />只能还原容器级别的操作（例如，可以还原 [删除容器](/rest/api/storageservices/delete-container)）。 如果删除了容器中的单个 blob，则无法通过容器软删除来还原该容器中的单个 blob。 | 是 |
| 在覆盖 blob 时，自动将其状态保存在以前的版本中。 | Blob 版本控制<br />[了解详细信息...](versioning-overview.md) | 对于某些存储帐户，如果需要对 blob 数据进行最佳保护，则启用 blob 版本控制，以及容器软删除和 blob 软删除。<br /><br />将不需要版本控制的 blob 数据存储在不同的帐户中以限制成本。 | 每个 blob 写入操作都会创建一个新版本。 如果删除或覆盖当前版本，则可以从以前的版本还原 blob 的当前版本。 | 否 |
| 在指定的时间间隔内还原已删除的 blob 或 blob 版本。 | Blob 软删除<br />[了解详细信息...](soft-delete-blob-overview.md) | 为所有存储帐户启用 blob 软删除，最短保留间隔为 7 天。<br /><br />启用 blob 版本控制和容器软删除以及 blob 软删除功能，以实现 blob 数据的最佳保护。<br /><br />在单独的存储帐户中存储需要不同保留期的 blob。 | 在保留期内可以还原已删除的 blob 或 blob 版本。 | 是，处于预览状态 |
| 将一组块 blob 还原到以前的时间点。 | 时间点还原<br />[了解详细信息...](point-in-time-restore-overview.md) | 要使用时间点还原恢复到以前的状态，请将应用程序设计为删除单个块 blob，而不是删除容器。 | 可以将一组块 blob 还原为过去特定时间点的状态。<br /><br />只能还原在块 blob 上执行的操作。 对容器、页 blob 或追加 blob 执行的任何操作都不会还原。 | 否 |
| 手动保存 blob 在给定时间点的状态。 | Blob 快照<br />[了解详细信息...](snapshots-overview.md) | 如果版本管理不适合你的方案（由于成本或其他考虑因素）或者存储帐户已启用分层命名空间，则建议采用 blob 版本控制的替代方法。 | 如果 blob 被覆盖，则可以从快照还原 blob。 如果删除该 blob，则快照也将被删除。 | 是，处于预览状态 |
| blob 可以被删除或覆盖，但数据会定期复制到另一个存储帐户。 | 使用 Azure 存储对象复制或 AzCopy 或 Azure 数据工厂等工具将数据复制到第二个帐户的自创解决方案。 | 为了让保护更安心，建议采用，以防意外的有意操作或不可预测的情况。<br /><br />在主帐户所在的同一区域中创建第二个存储帐户，以避免产生传出流量费用。 | 如果主要帐户以任何方式泄露，则可以从第二个存储帐户中还原数据。 | 支持 AzCopy 和 Azure 数据工厂。<br /><br />不支持对象复制。 |

## <a name="data-protection-by-resource-type"></a>按资源类型进行的数据保护

下表根据所保护的资源总结了 Azure 存储数据保护选项。

| 数据保护选项 | 保护帐户不被删除 | 保护容器不被删除 | 防止删除对象 | 防止覆盖对象 |
|--|--|--|--|--|
| Azure 资源管理器锁定 | 是 | 否<sup>1</sup> | 否 | 否 |
| blob 版本上的不可变性策略（预览版） | 是<sup>2</sup> | 是<sup>3</sup> | 是 | 是<sup>4</sup> |
| 容器上的不可变性策略 | 是<sup>5</sup> | 是 | 是 | 是 |
| 容器软删除 | 否 | 是 | 否 | 否 |
| Blob 版本管理<sup>6</sup> | 否 | 否 | 是 | 是 |
| Blob 软删除 | 否 | 否 | 是 | 是 |
| 时间点还原<sup>6</sup> | 否 | 否 | 是 | 是 |
| Blob 快照 | 否 | 否 | 否 | 是 |
| 将数据复制到第二个帐户的自创解决方案<sup>7</sup> | 否 | 是 | 是 | 是 |

<sup>1</sup> Azure 资源管理器锁定无法保护容器不被删除。<br />
<sup>2</sup> 如果至少有一个容器启用了版本级不可变存储，存储帐户删除将失败。<br />
<sup>3</sup> 如果容器中至少存在一个 blob，无论策略是已锁定还是未锁定，容器删除都将失败。<br />
<sup>4</sup> 覆盖当前版本 blob 的内容会创建新版本。 不可变性策略可防止覆盖版本的元数据。<br />
<sup>5</sup> 当法定保留或锁定的基于时间的保留策略在容器范围生效时，存储帐户也会受到保护，无法删除。<br />
<sup>6</sup> 目前不支持 Data Lake Storage 工作负载。<br />
<sup>7</sup> AzCopy 和 Azure 数据工厂支持 Blob 存储和 Data Lake Storage 工作负载。 仅 blob 存储工作负载支持对象复制。<br />

## <a name="recover-deleted-or-overwritten-data"></a>恢复删除或覆盖的数据

如果需要恢复已覆盖或已删除的数据，则如何继续取决于已启用的数据保护选项以及受影响的资源。 下表描述了可以执行哪些操作来恢复数据。

| 已删除或覆盖的资源 | 可能的恢复操作 | 恢复要求 |
|--|--|--|
| 存储帐户 | 尝试恢复已删除的存储帐户<br />[了解详细信息...](../common/storage-account-recover.md) | 存储帐户最初是用 Azure 资源管理器部署模型创建的，并且是在过去 14 天内删除。 删除原始帐户后，尚未创建同名的新存储帐户。 |
| 容器 | 恢复软删除容器及其内容<br />[了解详细信息...](soft-delete-container-enable.md) | 已启用容器软删除并且容器软删除保留期尚未过期。 |
| 容器和 Blob | 还原另一个存储帐户中的数据 | 所有容器和 blob 操作都已有效地复制到另一个存储帐户。 |
| Blob（任何类型） | 从以前的版本还原 blob<sup>1</sup><br />[了解详细信息...](versioning-enable.md) | 已启用 blob 版本控制，且 blob 具有一个或多个以前版本。 |
| Blob（任何类型） | 恢复已软删除的 blob<br />[了解详细信息...](soft-delete-blob-enable.md) | 已启用 blob 软删除，且软删除保留间隔尚未过期。 |
| Blob（任何类型） | 从快照还原 blob<br />[了解详细信息...](snapshots-manage-dotnet.md) | Blob 具有一个或多个快照。 |
| 块 blob 集 | 将一组块 blob 恢复到之前某个时间点的状态<sup>1</sup><br />[了解详细信息...](point-in-time-restore-manage.md) | 已启用时间点还原，且还原点在保留间隔内。 存储帐户未泄露且未损坏。 |
| Blob 版本 | 恢复软删除的版本<sup>1</sup><br />[了解详细信息...](soft-delete-blob-enable.md) | 已启用 blob 软删除，且软删除保留间隔尚未过期。 |

<sup>1</sup> 目前不支持 Data Lake Storage 工作负载。

## <a name="summary-of-cost-considerations"></a>成本注意事项摘要

下表总结了本指南中所述的各种数据保护选项的成本注意事项。

| 数据保护选项 | 成本注意事项 |
|-|-|
| 存储帐户的 Azure Resource Manager 锁定 | 在存储帐户上配置锁定是免费的。 |
| blob 版本上的不可变性策略（预览版） | 在容器上启用版本级不可变性是免费的。 在 blob 版本上创建、修改或删除基于时间的保留策略或法定保留会产生写入事务费用。 |
| 容器上的不可变性策略 | 在容器上配置永久性策略是免费的。 |
| 容器软删除 | 为存储帐户启用容器软删除是免费的。 软删除容器中的数据将按与活动数据相同的费率进行计费，直到软删除容器被永久删除。 |
| Blob 版本控制 | 为存储帐户启用 blob 版本控制是免费的。 启用 blob 版本控制后，对该帐户中的 blob 进行的每次写入或删除操作都将创建一个新版本，这可能会导致增加容量成本。<br /><br />Blob 版本根据唯一的块或页进行计费。 因此，随着基础 blob 与特定版本分离，成本也会随之增加。 更改 blob 或 blob 版本的层级可能会对计费产生影响。 有关详细信息，请参阅[定价和计费](versioning-overview.md#pricing-and-billing)。<br /><br />根据需要使用生命周期管理来删除旧版本，以控制成本。 有关详细信息，请参阅[通过自动执行 Azure Blob 存储访问层来优化成本](./lifecycle-management-overview.md)。 |
| Blob 软删除 | 为存储帐户启用 blob 软删除是免费的。 软删除 blob 中的数据将按与活动数据相同的费率进行计费，直到软删除 blob 被永久删除。 |
| 时间点还原 | 为存储帐户启用时间点还原是免费的；但是，启用时间点还原还会启用 blob 版本控制、软删除和更改源，其中每一项都可能产生额外费用。<br /><br />执行还原操作时，会按时间点还原收费。 还原操作的成本取决于要还原的数据量。 有关详细信息，请参阅[定价和计费](point-in-time-restore-overview.md#pricing-and-billing)。 |
| Blob 快照 | 根据唯一的块或页对快照中的数据计费。 因此，随着基础 blob 与快照分离，成本也会随之增加。 更改 blob 或快照的层级可能会对计费产生影响。 有关详细信息，请参阅[定价和计费](snapshots-overview.md#pricing-and-billing)。<br /><br />根据需要使用生命周期管理来删除旧快照，以控制成本。 有关详细信息，请参阅[通过自动执行 Azure Blob 存储访问层来优化成本](./lifecycle-management-overview.md)。 |
| 将数据复制到另一个存储帐户 | 在第二个存储帐户中维护数据将产生容量和事务成本。 如果第二个存储帐户所在的区域不同于源帐户，则将数据复制到第二个帐户将额外产生传出流量费用。 |

## <a name="disaster-recovery"></a>灾难恢复

Azure 存储始终保留数据的多个副本，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 冗余可确保即使遇到故障，存储帐户也能达到其可用性和持久性目标。 有关如何配置存储帐户以实现高可用性的详细信息，请参阅 [Azure 存储冗余](../common/storage-redundancy.md)。

万一数据中心发生故障，如果存储帐户在两个地理区域之间冗余（异地冗余），则可以选择将帐户从主要区域故障转移到次要区域。 有关详细信息，请参阅[灾难恢复和存储帐户故障转移](../common/storage-disaster-recovery-guidance.md)。

对于启用了分层命名空间的存储帐户，目前不支持客户托管的故障转移。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中提供的 Blob 存储功能](./storage-feature-support-in-storage-accounts.md)。

## <a name="next-steps"></a>后续步骤

- [Azure 存储冗余](../common/storage-redundancy.md)
- [灾难恢复和存储帐户故障转移](../common/storage-disaster-recovery-guidance.md)