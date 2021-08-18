---
title: 创建 Azure 文件同步服务器终结点
description: 了解在创建服务器终结点的过程中可用的选项，以及如何根据自己的情况最恰当地应用这些选项。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 06/01/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: b981ae7394e070d4da793fddccdd52e4350bde86
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798894"
---
# <a name="create-an-azure-file-sync-server-endpoint"></a>创建 Azure 文件同步服务器终结点

服务器终结点代表已注册服务器上的特定位置，例如服务器卷中的文件夹。 服务器终结点必须满足以下条件：

- 服务器终结点必须是已注册的服务器（而不是装载式共享）上的路径。 不支持网络附加存储 (NAS)。
- 尽管服务器终结点可以位于系统卷上，但系统卷上的服务器终结点可能不会使用云分层。
- 不支持在卷上创建服务器终结点后更改路径或驱动器号。 在创建服务器终结点之前，请确保使用合适的路径。
- 已注册的服务器可支持多个服务器终结点，但在任何给定时间，一个同步组中每个已注册的服务器只能有一个服务器终结点。 同步组中的其他服务器终结点必须位于不同的已注册的服务器上。
- 如果所有终结点的命名空间不重叠（例如 F:\sync1 和 F:\sync2）并且每个终结点同步到唯一的同步组，则多个服务器终结点可存在于同一个卷。 

本文将帮助你了解创建新服务器终结点和开始同步所需的选项和决策。为此，必须已完成[规划 Azure 文件同步部署](file-sync-planning.md)，并在执行创建服务器终结点的步骤之前已部署[所需的资源](file-sync-deployment-guide.md)。

## <a name="prerequisites"></a>先决条件

若要创建服务器终结点，必须首先确保满足以下条件： 
- 服务器安装了 Azure 文件同步代理并已注册。 有关安装 Azure 文件同步代理的详细信息，请参阅[在 Azure 文件同步中注册/取消注册服务器](file-sync-server-registration.md)。 
- 确保已部署存储同步服务。 有关如何部署存储同步服务的详细信息，请参阅[如何部署 Azure 文件同步](file-sync-deployment-guide.md)。 
- 确保已部署同步组。 了解如何[创建同步组](file-sync-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)。
- 确保服务器已连接到 Internet，并且 Azure 可以访问。 Azure 文件同步使用端口 443 来进行服务器与云服务之间的所有通信。

## <a name="create-a-server-endpoint"></a>创建服务器终结点

[!INCLUDE [storage-files-sync-create-server-endpoint](../../../includes/storage-files-sync-create-server-endpoint.md)]

### <a name="cloud-tiering-section"></a>“云分层”部分

创建新的服务器终结点时，可以选择启用 Azure 文件同步的云分层功能。以后可以更改“云分层”部分中的选项。 但是，以下部分中的不同选项是根据是否为新服务器终结点启用了云分层提供的。

请参阅详细介绍了基础知识、[策略](file-sync-cloud-tiering-policy.md)和最佳做法的[云分层文章](file-sync-cloud-tiering-overview.md)。 

### <a name="initial-sync-section"></a>“初始同步”部分

“初始同步”部分仅适用于同步组中的第一个服务器终结点。 对于任何其他服务器终结点，请参阅[“初始下载”部分](#initial-download-section)。

有两种在根本上不同的初始同步行为：

:::row:::
    :::column:::
        **合并**
    :::column-end:::
    :::column:::
        权威上传
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        “合并”是标准选项，默认已选中。 除非迁移方案比较特殊，否则应保持选中“合并”。 
* 加入某个服务器位置时，在大多数情况下，该服务器位置或云共享是空的。 对于这种情况，“合并”是适当的行为，可以产生预期的结果。 
* 当这两个位置都包含文件和文件夹时，命名空间将会合并。 如果服务器上的文件或文件夹名称也存在于云共享中，则会发生同步冲突。 [冲突将自动得到解决](../files/storage-files-faq.md#afs-conflict-resolution)。 </br> </br> 在“合并”选项中，需要做出一项选择来确定 Azure 文件共享中的内容最初如何到达服务器。 如果 Azure 文件共享是空的，则此选择不会产生任何影响。 可在接下来的段落中找到更多详细信息：[初始下载](#initial-download-section)
    :::column-end:::
    :::column:::
        “权威上传”是为特定迁移方案保留的初始同步选项。 同步同一个服务器路径，该路径也用来（例如，通过 Azure Data Box）为云共享播种。 在这种情况下，云和服务器位置包含基本相同的数据，不过服务器中的数据略新一些。 在 Data Box 进行传输的过程中，用户不断地进行更改。 然后，此迁移方案要求使用服务器上的更改（较新数据）无缝更新云，且不产生任何冲突。 因此，服务器是命名空间形状的权威来源，而 Data Box 用于避免从服务器进行大规模的初始上传。 即使使用了脱机数据传输机制来为云存储播种，服务器权威上传也能实现在不发生停机的情况下采用云。
    :::column-end:::
:::row-end:::

仅当服务器位置包含数据时，才能使用权威上传选项成功进行服务器终结点的预配。 此块用于防范意外的错误配置。 权威上传的工作方式类似于 RoboCopy /MIR。 此模式将源镜像到目标。 源是 AFS 服务器，目标是云共享。 权威上传将在源的映像中为目标塑形。 

* 将从服务器上传新的或已更新的文件和文件夹。
* 将从云共享中删除不（再）存在于服务器上的文件和文件夹。
* 对服务器上的文件和文件夹做出的仅限元数据的更改将作为仅限元数据的更新有效转移到云共享。
* 文件和文件夹可能存在于服务器和云共享上。 但自从为 Azure 文件共享播种以来，某些文件或文件夹可能在服务器上更改了其父目录。 这些文件和文件夹将从云共享中清除，然后再次上传。 因此，在迁移过程中，最好避免以较大规模重新构建命名空间。

### <a name="initial-download-section"></a>“初始下载”部分

“初始下载”部分适用于同步组中的第二个和后续的任何服务器终结点。 [同步组中的第一个服务器终结点具有额外的选项](#initial-sync-section)，这些选项与使用 Azure Data Box 进行迁移相关。 如果这不是同步组中的第一个服务器终结点，则这些选项不适用。

> [!NOTE]
> 如果 Azure 文件共享是空的，则选择初始下载选项不会有任何影响。

在此部分，可以选择 Azure 文件共享中的内容最初到达服务器的方式：

:::row:::
    :::column:::
        :::image type="content" source="media/file-sync-server-endpoint-create/initial-download-options.png" alt-text="描述“创建服务器终结点”Azure 门户向导中的选项的插图。" lightbox="media/file-sync-server-endpoint-create/initial-download-options-expanded.png":::
    :::column-end:::
    :::column:::
* 仅命名空间 </br> 只将 Azure 文件共享中的文件和文件夹结构引入到本地服务器。 不会下载任何文件内容。 如果先前为此新服务器终结点启用了云分层，则此选项是默认选项。
* 先关闭命名空间，然后撤回内容 </br> 为了更快地提供数据，不管云分层设置如何，都会先关闭命名空间。 命名空间在服务器上可用后，云中的文件内容将撤回到服务器。 撤回是根据每个文件中的上次修改时间戳进行的。 如果服务器卷上没有足够的空间，则剩余的文件将保持为分层文件。 如果未为此服务器终结点启用云分层，则此选项是默认选项。
* 避免分层文件 </br> 此选项将完整地下载每个文件，然后该文件将显示在服务器上的文件夹中。 此选项可以完全避免服务器上存在分层文件。 命名空间项和文件内容始终同时显示。 如果你创建服务器终结点的原因是为了从云快速进行灾难恢复，请避免使用此选项。 如果你的应用程序需要显示完整文件并且无法容忍其命名空间中存在分层文件，则这是理想选择。 如果为新服务器终结点使用云分层，则此选项不可用。
    :::column-end:::
:::row-end:::

一旦选择了初始下载选项，那么你在确认创建服务器终结点后便无法更改此选项。 初始下载完成后，文件在服务器上的显示方式取决于如何使用云分层功能，以及是否选择了[主动撤回云中的更改](file-sync-cloud-tiering-overview.md#proactive-recalling)。 后一种功能适用于在不同地理位置具有多个服务器终结点的同步组。

* 已启用云分层 </br> 来自其他服务器终结点的新文件和已更改的文件将作为分层文件显示在此服务器终结点上。 仅当已选择[主动撤回](file-sync-cloud-tiering-overview.md#proactive-recalling) Azure 文件共享中由其他服务器终结点所做的更改时，这些更改才会作为完整文件出现。
*  已禁用云分层 </br> 来自其他服务器终结点的新文件和已更改的文件将作为完整文件显示在此服务器终结点上。 它们不会先作为分层文件显示，然后被撤回。 已禁用云分层的分层文件是一种快速灾难恢复功能，仅在初始预配期间显示。


## <a name="next-steps"></a>后续步骤

对于 Azure 文件共享和 Azure 文件同步，有更多的知识有待探索。以下文章可帮助你了解高级选项和最佳做法。 其中还提供了故障排除方面的帮助。 在适当的情况下，这些文章会包含 [Azure 文件共享文档](../files/storage-files-introduction.md)的链接。

* [迁移概述](../files/storage-files-migration-overview.md)
* [规划 Azure 文件同步部署](../file-sync/file-sync-planning.md)
* [创建文件共享](../files/storage-how-to-create-file-share.md)
* [Azure 文件同步排除故障](../file-sync/file-sync-troubleshoot.md)
