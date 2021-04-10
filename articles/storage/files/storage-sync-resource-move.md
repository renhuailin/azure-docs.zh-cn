---
title: Azure 文件同步资源移动和拓扑更改
description: 了解如何跨资源组、订阅和 Azure Active Directory (AAD) 租户移动同步资源。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 3/10/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: acd5f9263a74d3273dc65522e77d83c90a719311
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103554960"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>将 Azure 文件同步资源移到其他资源组、订阅或 AAD 租户

本文介绍如何更改 Azure 文件同步云资源和 Azure 存储帐户的资源组、订阅或 Azure Active Directory (AAD) 租户。

在规划对 Azure 文件同步云资源进行更改时，必须同时考虑到存储资源。 存在以下资源：

**Azure 文件同步资源（按分层顺序排列）**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: 存储同步服务
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: 已注册的服务器
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: 同步组
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: 云终结点
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: 服务器终结点

在 Azure 文件同步中，唯一能够移动的资源是存储同步服务资源。 任何子资源都已绑定到其父级，无法转移到其他存储同步服务。

**Azure 存储资源（按分层顺序排列）**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: 存储帐户
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: 文件共享

在 Azure 存储中，唯一能够移动的资源是存储帐户。 作为子资源，Azure 文件共享无法转移到其他存储帐户。

## <a name="supported-combinations"></a>支持的组合

在规划资源移动时，需要一同考虑到存储帐户和顶级 Azure 文件同步资源（称为“存储同步服务”）。

存储同步服务以及包含同步文件共享的存储帐户应始终驻留在同一个订阅中，这是最佳做法。 支持以下组合：

* 存储同步服务和存储帐户位于 **不同的资源组** 中（但在同一 Azure 租户中）
* 存储同步服务和存储帐户位于 **不同的订阅** 中（但在同一 Azure 租户中）

> [!IMPORTANT]
> 如果混用不同的移动组合形式，最终可能会将存储同步服务和存储帐户放到由不同 AAD 租户控制的不同订阅中。 尽管同步看上去正常，但这并不受支持。 同步将来可能会停止，并且无法恢复正常运行状态。

在规划资源移动时，[在同一个 AAD 租户内部移动](#move-within-the-same-azure-active-directory-tenant)与[移到其他 AAD 租户](#move-to-a-new-azure-active-directory-tenant)的考虑因素是不同的。 移动 AAD 租户时，请始终将同步和存储资源一起移动。

### <a name="move-within-the-same-azure-active-directory-tenant"></a>在同一个 Azure Active Directory 租户内部移动

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="显示 Azure 门户中存储同步服务资源的插图，其中的“移动”命令已展开。该插图显示了资源组移动和订阅移动选项。" lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        移动存储同步服务资源的便捷方式是使用 Azure 门户。 导航到要移动的存储同步服务，然后在命令栏中选择“移动”。 移动存储帐户时也可以使用相同的步骤。 还可以通过这种方式移动资源组中的所有资源。 如果你安装了存储同步服务并且其使用的所有存储帐户都在此资源组中，则我们建议移动整个资源组。
    :::column-end:::
:::row-end:::

> [!WARNING]
> 移动存储帐户资源时，同步将立即停止。 必须手动授权同步服务访问新订阅中的相关存储帐户。 [Azure 文件同步存储访问授权](#azure-file-sync-storage-access-authorization)部分将提供所需的步骤。

### <a name="move-to-a-new-azure-active-directory-tenant"></a>移到新的 Azure Active Directory 租户

单个资源（例如存储同步服务或存储帐户）无法自行转移到其他 AAD 租户。 只有 Azure 订阅才能移动 AAD 租户。 请考虑新 AAD 租户中的订阅结构。 可将一个订阅专门用于 Azure 文件同步。 

1. 创建一个 Azure 订阅（或者在旧租户中确定一个要移动的现有订阅）。
1. 在存储同步服务和所有关联存储帐户所在的[同一个 AAD 租户内部执行订阅移动](#move-within-the-same-azure-active-directory-tenant)。
1. 同步将会停止。 立即完成租户移动，或者[恢复同步服务访问已移动的存储帐户的能力](#azure-file-sync-storage-access-authorization)。 随后便可以将资源移到新的 AAD 租户。

将所有相关 Azure 文件同步资源隔离到其自身的订阅后，便可以将整个订阅移到目标 AAD 租户。 可以参考[转移订阅指南](../../role-based-access-control/transfer-subscription.md)来规划和执行此类转移。

> [!WARNING]
> 将订阅从一个租户转移到另一个租户时，同步会立即停止。 必须手动授权同步服务访问新订阅中的相关存储帐户。 [Azure 文件同步存储访问授权](#azure-file-sync-storage-access-authorization)部分将提供所需的步骤。

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="显示 Azure 门户中订阅“概述”边栏选项卡的插图，其中突出显示了页面中上方的“更改目录”工具栏命令。" lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        完成规划并分配所需的权限后，便可以开始迁移了：
        1. 在 Azure 门户中，导航到你的订阅的“概述”边栏选项卡。
        1. 选择“更改目录”
        1. 遵循向导步骤分配新的 AAD 租户。
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Azure 文件同步存储访问授权

将存储帐户移到新的订阅，或者从订阅中移到新的 Azure Active Directory (AAD) 租户时，同步将会停止。 可以使用基于角色的访问 (RBAC) 来授权 Azure 文件同步访问存储帐户，这些角色分配不会连同资源一起迁移。

### <a name="azure-file-sync-service-principal"></a>Azure 文件同步服务主体

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="显示 Azure 门户、订阅管理、已注册的资源提供程序的插图。" lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        Azure 文件同步服务主体必须存在于你的 AAD 租户中，这样才能授权同步服务访问存储帐户。 </br></br> 如果现在就创建新的 Azure 订阅，Azure 文件同步资源提供程序 *Microsoft.StorageSync* 将自动注册到该订阅。 资源提供程序注册将在控制订阅的 Azure Active Directory 租户中提供一个用于同步的服务主体。 服务主体类似于 AAD 中的用户帐户。 可以通过基于角色的访问控制 (RBAC) 使用 Azure 文件同步服务主体来授权访问资源。 同步服务需要访问的唯一一个资源是你的存储帐户，其中包含应该同步的文件共享。必须将 *Microsoft.StorageSync* 分配到存储帐户中的内置角色“读取者和数据访问”。 </br></br> 将文件共享添加到同步组时（换言之，创建云终结点时），将通过已登录用户的用户上下文自动完成此分配。 将存储帐户移到新的订阅或 AAD 租户时，此角色分配将会丢失，[必须手动重新建立](#establish-sync-access-to-a-storage-account)。
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> 如果当前未创建目标 Azure 订阅，请检查 *Microsoft.StorageSync* 资源提供程序是否已注册到该订阅。 如果未注册，请在同一门户边栏选项卡上手动添加该资源提供程序。

### <a name="establish-sync-access-to-a-storage-account"></a>建立可让同步服务访问存储帐户的分配

必须使用 [Azure 文件同步服务主体](#azure-file-sync-service-principal)通过基于角色的访问控制 (RBAC) 来授权访问存储帐户。 必须将 *Microsoft.StorageSync* 分配到存储帐户中的内置角色“读取者和数据访问”。 

将文件共享添加到同步组时（换言之，创建云终结点时），通常会通过已登录用户的用户上下文自动完成此分配。 但是，将存储帐户移到新的订阅或 AAD 租户时，此角色分配将会丢失，必须手动重新建立。

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="显示已分配到存储帐户中的“读取者和数据访问”角色的 Microsoft.StorageSync 服务主体的插图":::
    :::column-end:::
    :::column:::
        在 Azure 门户中，导航到需要重新授权同步服务访问的存储帐户。 <ol><li>在左侧的目录中选择“访问控制(IAM)”。</li><li>选择“角色分配”选项卡以列出有权访问你的存储帐户的用户和应用程序（服务主体）。</li><li>选择“添加”</li><li>在“角色”字段中，选择“读取者和数据访问”。 </li><li>在“选择”字段中键入 *Microsoft.StorageSync*，然后选择角色并单击“保存”。 </li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>移到其他 Azure 区域

Azure 文件同步资源“存储同步服务”以及包含所要同步的文件共享的存储帐户会部署到某个 Azure 区域。 该区域是创建资源时确定的。 存储同步服务和存储帐户资源的区域必须匹配。 创建这些区域后，无法在上述任一资源类型中更改区域。

向资源分配不同的区域不同于[区域故障转移](#region-fail-over)，后者是否受支持取决于存储帐户冗余设置。

## <a name="region-fail-over"></a>区域故障转移

[Azure 存储为存储帐户提供异地冗余选项](../common/storage-redundancy.md#geo-redundant-storage)。 这些冗余选项可能会对用于 Azure 文件同步的存储帐户造成问题。主要原因在于，在地理上相隔遥远的区域之间的复制不是由 Azure 文件同步执行的，而是由内置于 Azure 存储子系统中的一种存储复制技术执行的。 这种复制无法知道应用程序的状态，而 Azure 文件同步是任意给定时刻都在与 Azure 文件共享相互同步文件的应用程序。 如果选择启用这些地理分散的存储冗余选项，在发生大规模的灾难时不会丢失所有数据。 但是，需要[预见到数据丢失](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss)。

> [!CAUTION]
> 故障转移永远无法完美替代在正确的 Azure 区域中预配资源。 如果资源位于“错误的”区域中，则需要考虑停止同步，然后重新设置为同步到所需区域中部署的新 Azure 文件共享。

在发生了导致 Azure 区域中的数据中心长时间无法正常工作的灾难性事件时，Microsoft 可以启动区域故障转移。 你的业务可以承受的停机时间定义可能短于 Microsoft 已准备好允许在启动区域故障转移之前经过的时间。 对于类似于这样的情况，[客户也可以自行启动故障转移](../common/storage-initiate-account-failover.md)。

> [!IMPORTANT]
> 发生故障转移时，需要针对受影响的存储同步服务提交支持票证，使同步服务重新正常工作。

## <a name="see-also"></a>请参阅

- [Azure 文件共享概述和同步迁移指南](storage-files-migration-overview.md)
- [对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)
