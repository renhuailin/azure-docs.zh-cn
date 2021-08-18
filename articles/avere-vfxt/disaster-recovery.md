---
title: 适用于 Avere vFXT for Azure 的灾难恢复指南
description: 如何防止 Avere vFXT for Azure 中的数据被意外删除或中断
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: ec58e921940be71b0189bcba96596c18b1b8786b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751494"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>适用于 Avere vFXT for Azure 的灾难恢复指南

本文概述了用于保护 Avere vFXT for Azure 工作流的策略，并提供了备份数据指导，以便你可以从事故或中断中恢复。

Avere vFXT for Azure 将数据临时存储在其缓存中。 数据长期存储在后端存储系统中 - 本地硬件系统、Azure Blob 存储容器，或者两者皆有。

若要防止中断和可能的数据丢失，请考虑以下四个方面：

* 如果 Avere vFXT for Azure 系统不可用，请防范停机
* 保护群集缓存中的数据
* 保护后端 NAS 硬件存储中的数据
* 保护后端 Azure Blob 云存储中的数据

每位 Avere vFXT for Azure 客户都必须创建自己的全面灾难恢复计划，其中包含这些项目的计划。 你还可以将复原能力构建到与 vFXT 群集一起使用的应用程序中。 有关帮助，请参阅[后续步骤](#next-steps)中的链接。

## <a name="protect-against-downtime"></a>防范停机

Avere vFXT for Azure 产品内置了冗余：

* 群集具有高可用性，并且单独的群集节点可以故障转移，使中断时间最少。
* 缓存中更改的数据会定期写入后端核心文件管理器（硬件 NAS 或 Azure Blob）以进行长期存储。

每个 Avere vFXT for Azure 群集都必须位于单个可用性区域中，但你可以使用位于不同区域或不同地区中的冗余群集，在发生区域性服务中断时快速提供访问权限。

如果担心丢失对数据的访问权限，还可以将存储容器放置在多个区域。 但是，请记住，与留在某个区域中的事务相比，多个区域之间的事务具有更高的延迟和更高的费用。

## <a name="protect-data-in-the-cluster-cache"></a>保护群集缓存中的数据

在常规关机之前，缓存数据始终写入核心文件管理器中，但在不受控制的关机中，缓存中的更改数据可能会丢失。

如果仅使用群集来优化文件读取，则不会丢失任何更改。 如果还使用群集来缓存文件更改（写入），请考虑是否要调整核心文件管理器的[缓存策略](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)<!-- link to legacy doc --> 来自定义将数据写入长期存储的频率。

一般而言，恢复计划应侧重于备份后端存储系统，该系统保存更多数据，并且通常对于发生故障后重新建立工作流更加重要。

## <a name="protect-data-in-nas-core-filers"></a>保护 NAS 核心文件管理器中的数据

使用接受的方法来保护存储在本地 NAS 硬件核心文件管理器上的数据，其中包括 NAS 提供商建议的快照和完整备份。 这些核心文件管理器的灾难恢复超出了本文的讨论范围。

## <a name="protect-data-in-azure-blob-storage"></a>保护 Azure Blob 存储中的数据

Avere vFXT for Azure 对 Azure Blob 核心文件管理器使用本地冗余存储 (LRS)。 这意味着，系统会自动复制 Blob 容器中的数据，以防止数据中心内发生暂时性的硬件故障。

本节提供有关如何进一步保护 Blob 存储中的数据免受罕见的区域性中断或意外删除的提示。

Azure Blob 存储中保护数据的最佳做法包括：

* 频繁地（通常由灾难恢复计划确定）将关键数据复制到另一区域中的另一个存储帐户。
* 控制对所有目标系统上的数据访问，以防止意外删除或损坏。 请考虑对数据存储使用[资源锁](../azure-resource-manager/management/lock-resources.md)。
* 为 Blob 核心文件管理器启用 Avere vFXT for Azure [云快照](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>)功能。

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>将 Avere vFXT 核心文件管理器数据复制到备份帐户

按照以下步骤在另一个帐户中建立数据备份。

1. 如果需要，请生成新的加密密钥，并将其安全地存储在受影响的系统之外。

   如果数据通过 Avere vFXT for Azure 群集进行加密，则在将数据复制到另一个存储帐户之前，应生成新的加密密钥。 安全地将该密钥和密码存储在安全且不会受到区域性故障影响的设施中。

   将容器添加到群集时，必须提供此密钥 - 即使你要将其重新添加到其原始群集。

   阅读[云加密设置](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> 了解详细信息。

   如果容器仅使用 Azure 的内置加密，则可以跳过此步骤。

1. 从系统中删除核心文件管理器。 这会强制群集将所有更改的数据写入后端存储。

   尽管你需要在备份后重新添加核心文件管理器，但删除它是确保将所有数据完全写入后端的最佳方式。 （“挂起”选项有时可以将更改的数据保留在缓存中。） <!-- xxx true? or just metadata? -->

   记下核心文件管理器的名称和连接信息（列于控制面板中的“命名空间”页面），以便在备份后重新添加容器时可以复制该容器。

   使用群集控制面板删除核心文件管理器。 [打开群集控制面板](avere-vfxt-cluster-gui.md)，然后选择“核心文件管理器” > “管理核心文件管理器” 。 找到要备份的存储系统，然后使用“删除”按钮将其从群集中删除。

1. 在另一个区域的另一个存储帐户中新建一个空的 Blob 存储容器。

1. 使用任何方便的复制工具将核心文件管理器上的数据复制到新容器。 副本必须复制数据而不做任何更改，而不会中断 Avere vFXT for Azure 使用的专有云文件系统格式。 基于 Azure 的工具包括 [AzCopy](../storage/common/storage-use-azcopy-v10.md)、[Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md) 和 [Azure 数据工厂](../data-factory/connector-azure-data-lake-store.md)。

1. 将数据复制到备份容器后，将原始容器添加回群集，如[配置存储](avere-vfxt-add-storage.md)中所述。

   * 使用相同的核心文件管理器名称和连接信息，以便不需要更改客户端工作流。
   * 将“Bucket 内容”值设置为现有数据选项。
   * 如果容器已由群集进行加密，则必须为其内容输入当前的加密密钥。 （这是步骤 1 中更新的密钥。）

对于第一个备份之后的备份，不需要创建新的存储容器。 不过，在每次执行备份时，请考虑生成新的加密密钥，以确保将当前密钥存储在你记得的位置。

### <a name="access-a-backup-data-source-during-an-outage"></a>在中断期间访问备份数据源

若要从 Avere vFXT for Azure 群集访问备份容器，请遵循以下流程：

1. 如果需要，请在不受影响的区域中创建一个新的 Avere vFXT for Azure 群集。

   > [!TIP]
   > 创建 Avere vFXT for Azure 群集时，可以保存其创建模板和参数的副本。 如果在创建主群集时保存此信息，则可以使用它来创建具有相同属性的替代群集。 在[摘要](avere-vfxt-deploy.md#validation-and-purchase)页上，单击“下载模板和参数”链接。 创建群集之前，请将信息保存到文件中。

1. 添加指向重复 Blob 容器的新云核心文件管理器。

   确保在核心文件管理器创建向导的“Bucket 内容”设置中指定目标容器已经包含数据。 （系统应在意外将此设置为“空”时发出警报。）  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. 如有必要，请更新客户端，使其装载新群集或新的核心文件管理器而不是原始内容。 （如果添加与原始容器具有相同的名称和连接路径的替代核心文件管理器，则不需要更新客户端进程，除非需要在新的 IP 地址上装载新群集。）

## <a name="next-steps"></a>后续步骤

* 有关为 Avere vFXT for Azure 自定义设置的详细信息，请参阅[群集优化](avere-vfxt-tuning.md)。
* 详细了解 Azure 中的灾难恢复，以及如何构建可复原的应用程序：

  * [Azure 复原技术指南](/azure/architecture/reliability/architect)
  * [发生区域范围的服务中断后进行恢复](/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Azure 应用程序的灾难恢复和高可用性](/azure/architecture/framework/resiliency/backup-and-recovery)
  <!-- can't find these in the source tree to use relative links -->
