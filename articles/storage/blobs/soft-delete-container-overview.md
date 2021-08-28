---
title: 容器软删除
titleSuffix: Azure Storage
description: 容器软删除可以保护数据，以便在应用程序或其他存储帐户用户错误地修改或删除数据后可以更轻松地恢复数据。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: d3684d3fcb79fca31f403294a640887fa8e33d92
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113504470"
---
# <a name="soft-delete-for-containers"></a>容器软删除

容器软删除通过将已删除的数据在系统中保留指定的一段时间来防止数据被意外删除。 在保留期内，你可以将软删除的容器及其内容恢复到该容器被删除时的状态。 保留期过后，该容器及其内容会被永久删除。

## <a name="recommended-data-protection-configuration"></a>推荐的数据保护配置

Blob 软删除是针对 Blob 数据的综合性数据保护策略的一部分。 为实现对 Blob 数据的最佳保护，Microsoft 建议启用以下所有数据保护功能：

- 容器软删除，用于还原已删除的容器。 若要了解如何启用容器软删除，请参阅[启用和管理容器的软删除](soft-delete-container-enable.md)。
- Blob 版本控制，用于自动维护 blob 的先前版本。 启用 blob 版本控制后，如果错误地修改或删除了数据，则可以还原 blob 的先前版本以恢复数据。 若要了解如何启用 blob 版本控制，请参阅[启用和管理 blob 版本控制](versioning-enable.md)。
- Blob 软删除，用于还原已删除的 blob、快照或版本。 若要了解如何启用 blob 软删除，请参阅[启用和管理 blob 的软删除](soft-delete-blob-enable.md)。

若要详细了解 Microsoft 的数据保护建议，请参阅[数据保护概述](data-protection-overview.md)。

## <a name="how-container-soft-delete-works"></a>容器软删除的工作原理

启用容器软删除后，可以为已删除的容器指定保持期，介于 1 到 365 天之间。 默认保持期为 7 天。 在保持期内，可以通过调用还原容器操作来恢复已删除的容器。

恢复容器时，该容器的 blob 以及任何 blob 版本和快照也会恢复。 但是，如果容器本身已被删除，则只能使用容器软删除来还原 blob。 要在尚未删除其父容器时还原已删除的 blob，必须使用 blob 软删除或 blob 版本控制。

> [!WARNING]
> 容器软删除只能还原整个容器及其在删除时的内容。 无法使用容器软删除还原容器中已删除的 blob。 Microsoft 建议同时启用 blob 软删除和 blob 版本控制，以保护容器中的各个 blob。
>
> 恢复容器时，必须将其恢复为原始名称。 如果原始名称已用于创建新容器，则无法恢复软删除的容器。

下图显示了在启用容器软删除后，如何还原已删除的容器：

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="显示如何还原软删除容器的示意图":::

保持期到期后，容器将从 Azure 存储永久删除，并且无法恢复。 保持期自删除容器的那一刻开始计算。 你可以随时更改保持期，但要记住，更新的保持期仅适用于新删除的容器。 先前删除的容器将以它被删除时生效的保持期为准永久删除。

禁用容器软删除后，并不会永久删除先前软删除的容器。 任何软删除的容器将在它被删除时生效的保持期到期后被永久删除。

容器软删除可用于以下类型的存储帐户：

- 常规用途 v2 和 v1 存储帐户
- 块 Blob 存储帐户
- Blob 存储帐户

还支持启用了分层命名空间的可以与 Azure Data Lake Storage Gen2 一起使用的存储帐户。

Azure 存储 REST API 2019-12-12 版或更高版本支持容器软删除。

> [!IMPORTANT]
> 容器软删除不能防止删除存储账户，仅可防止删除帐户中的容器。 若要防止存储帐户被删除，请在存储帐户资源上配置锁。 若要详细了解如何锁定 Azure 资源管理器资源，请参阅[锁定资源以防止意外更改](../../azure-resource-manager/management/lock-resources.md)。

## <a name="pricing-and-billing"></a>定价和计费

启用容器软删除无需额外付费。 软删除容器中的数据按与活动数据相同的费率计费。

## <a name="next-steps"></a>后续步骤

- [配置容器软删除](soft-delete-container-enable.md)
- [blob 的软删除](soft-delete-blob-overview.md)
- [Blob 版本控制](versioning-overview.md)
