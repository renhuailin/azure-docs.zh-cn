---
title: 存储帐户概述
titleSuffix: Azure Storage
description: 了解 Azure 存储中各种类型的存储帐户。 查看帐户命名、性能层、访问层、冗余、加密、终结点等。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: cb19372e5b97a9f508367366faa9ae49d0d11261
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596954"
---
# <a name="storage-account-overview"></a>存储帐户概述

Azure 存储帐户包含所有 Azure 存储数据对象：Blob、文件共享、队列、表和磁盘。 存储帐户为 Azure 存储数据提供一个唯一的命名空间，可通过 HTTP 或 HTTPS 从世界上的任何位置访问该数据。 Azure 存储帐户中的数据持久，高度可用、安全且可大规模缩放。

若要了解如何创建 Azure 存储帐户，请参阅[创建存储帐户](storage-account-create.md)。

## <a name="types-of-storage-accounts"></a>存储帐户的类型

Azure 存储提供多种类型的存储帐户。 每种类型支持不同的功能，并且具有自己的定价模型。 在创建存储帐户之前，请考虑到这些差异，以确定最适合应用程序的帐户类型。

以下是 Microsoft 在大多数场景下推荐的存储帐户类型。 它们全部使用 [Azure 资源管理器](../../azure-resource-manager/management/overview.md) 部署模型。

| 存储帐户的类型 | 支持的存储服务 | 冗余选项 | 使用情况 |
|--|--|--|--|
| 标准常规用途 v2 | Blob（包括 Data Lake Storage<sup>1</sup>）、队列和表存储、Azure 文件存储  | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Blob、文件共享、队列和表的标准存储帐户类型。 建议在大多数情况下使用 Azure 存储。 请注意，如果想要支持 Azure 文件存储 中的 NFS 文件共享，请使用高级文件共享帐户类型。 |
| 高级块 blob <sup>3</sup> | Blob 存储（包括 Data Lake Storage <sup>1</sup>） | LRS<br /><br />ZRS<sup>2</sup> | 块 Blob 和追加 Blob 的高级存储帐户类型。 建议用于事务率较高的方案，或者用于使用较小对象或需要存储延迟始终较低的方案。 [了解有关示例工作负载的详细信息。](../blobs/storage-blob-performance-tiers.md#premium-performance) |
| 高级文件共享 <sup>3</sup> | Azure 文件存储 | LRS<br /><br />ZRS<sup>2</sup> | 仅适用于文件共享的高级存储帐户类型。 建议用于企业级应用程序或高性能级应用程序。 如果想要同时支持 SMB 和 NFS 文件共享的存储帐户，请使用此帐户类型。 |
| 高级页 Blob <sup>3</sup> | 仅页 Blob | LRS | 仅适用于页 blob 的高级存储帐户类型。 [了解有关页 Blob 和示例用例的详细信息。](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Data Lake Storage 是一组专用于大数据分析的功能，基于 Azure Blob 存储而构建。 有关详细信息，请参阅 [Data Lake Storage Gen2 简介](../blobs/data-lake-storage-introduction.md) 和 [创建用于 Data Lake Storage Gen2 的存储帐户](../blobs/create-data-lake-storage-account.md)。

<sup>2</sup> 区域冗余存储 (ZRS) 和异地区域冗余存储 (GZRS/RA-GZRS) 在某些区域中仅可用于标准常规用途 v2 帐户、高级块 blob 帐户和高级文件共享帐户。 有关详细信息，请参阅 [Azure 存储冗余](storage-redundancy.md)。

<sup>3</sup> 高级性能层中的存储帐户使用固态硬盘 (SSD)，以实现低延迟和高吞吐量。

旧存储帐户也受支持。 有关详细信息，请参阅[旧存储帐户类型](#legacy-storage-account-types)。

## <a name="storage-account-endpoints"></a>存储帐户终结点

存储帐户在 Azure 中为数据提供唯一的命名空间。 存储在 Azure 存储中的每个对象都有一个地址，其中包含唯一的帐户名称。 将帐户名称与 Azure 存储服务终结点组合在一起，即可构成适用于存储帐户的终结点。

为存储帐户命名时，请记住以下规则：

- 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。
- 存储帐户名称在 Azure 中必须是唯一的。 没有两个存储帐户可以有相同的名称。

下表列出了每项 Azure 存储服务的终结点格式。

| 存储服务 | 终结点 |
|--|--|
| Blob 存储 | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure 文件 | `https://<storage-account>.file.core.windows.net` |
| 队列存储 | `https://<storage-account>.queue.core.windows.net` |
| 表存储 | `https://<storage-account>.table.core.windows.net` |

构造用于访问存储帐户中某个对象的 URL，方法是：将对象在存储帐户中的位置追加到终结点。 例如，blob 的 URL 类似于：

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

也可将存储帐户配置为对 Blob 使用自定义域。 有关详细信息，请参阅[为 Azure 存储帐户配置自定义域名](../blobs/storage-custom-domain-name.md)。

## <a name="migrate-a-storage-account"></a>迁移存储帐户

下表汇总并指向有关如何移动、升级或迁移存储帐户的指导：

| 迁移方案 | 详细信息 |
|--|--|
| 将存储帐户移到其他订阅 | Azure 资源管理器提供将资源移到其他订阅的选项。 有关详细信息，请参阅[将资源移到新资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 |
| 将存储帐户移到其他资源组 | Azure 资源管理器提供将资源移到其他资源组的选项。 有关详细信息，请参阅[将资源移到新资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 |
| 将存储帐户移到其他区域 | 若要移动某个存储帐户，请在另一个区域中创建该存储帐户的副本。 然后，使用 AzCopy 或其他所选工具将数据移到该帐户。 有关详细信息，请参阅[将 Azure 存储帐户移到另一区域](storage-account-move.md)。 |
| 升级到常规用途 v2 存储帐户 | 你可以将常规用途 v1 存储帐户或 Blob 存储帐户升级到常规用途 v2 存储帐户。 注意，该操作无法撤消。 有关详细信息，请参阅[升级到常规用途 v2 存储帐户](storage-account-upgrade.md)。 |
| 将经典存储帐户迁移到 Azure 资源管理器 | 在功能、可伸缩性和安全性方面，Azure 资源管理器部署模型优于经典部署模型。 有关将经典存储帐户迁移到 Azure 资源管理器的详细信息，请参阅 [平台支持的从经典部署模型到 Azure 资源管理器的 IaaS 资源迁移](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) 中的“迁移存储帐户”部分。 |

## <a name="transfer-data-into-a-storage-account"></a>将数据传输到存储帐户

Microsoft 提供了用于从本地存储设备或第三方云存储提供程序导入数据的服务和实用工具。 使用哪种解决方案取决于要传输的数据量。 有关详细信息，请参阅 [Azure 存储迁移概述](storage-migration-overview.md)。

## <a name="storage-account-encryption"></a>存储帐户加密

存储帐户中的所有数据自动在服务端加密。 有关加密和密钥管理的详细信息，请参阅[静态数据的 Azure 存储加密](storage-service-encryption.md)。

## <a name="storage-account-billing"></a>存储帐户计费

Azure 存储根据存储帐户使用情况计费。 存储帐户中的所有对象会作为组共同计费。 存储成本根据以下因素计算：

- **区域** 是指帐户所基于的地理区域。
- **帐户类型** 是指所使用的存储帐户类型。
- **访问层** 是指你为常规用途 v2 或 Blob 存储帐户指定的数据使用模式。
- “容量”是指你使用多少存储帐户配额来存储数据。
- **冗余** 可以确定一次保留的数据副本的数量以及保留位置。
- **事务** 是指对 Azure 存储的所有读取和写入操作。
- **数据流出量** 指的是传出某个 Azure 区域的数据。 当存储帐户中的数据被不在同一区域中运行的应用程序访问时，需要为数据流出量付费。 有关使用资源组对同一区域内的数据和服务进行分组以限制数据流出费用的信息，请参阅[什么是 Azure 资源组？](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)。

[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)页提供基于帐户类型、存储容量、复制和交易的详细定价信息。 [数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)提供了针对数据流出量的详细定价信息。 可以使用 [Azure 存储定价计算器](https://azure.microsoft.com/pricing/calculator/?scenario=data-management)来帮助估算成本。

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>旧存储帐户类型

下表描述了旧存储帐户类型。 Microsoft 不推荐这些帐户类型，但在某些情况下可以使用：

| 旧存储帐户类型 | 支持的存储服务 | 冗余选项 | 部署模型 | 使用情况 |
|--|--|--|--|--|
| 标准常规用途 v1 | Blob、队列和表存储、Azure 文件存储 | LRS/GRS/RA-GRS | 资源管理器、经典 | 常规用途 v1 帐户可能没有最新功能，其每 GB 定价也可能不是最低的。 可考虑在以下场景中使用：<br /><ul><li>应用程序需要 Azure [经典部署模型](../../azure-portal/supportability/classic-deployment-model-quota-increase-requests.md)。</li><li>应用程序为事务密集型，或者使用很大的异地复制带宽，但不需要大的容量。 在这种情况下，常规用途 v1 可能是最经济的选择。</li><li>如果使用的 Azure 存储 REST API 版本早于 2014-02-14，或使用的客户端库版本低于 4.x，则无法升级应用程序。</li></ul> |
| 标准 Blob 存储 | Blob 存储（仅限块 Blob 和追加 Blob） | LRS/GRS/RA-GRS | 资源管理器 | Microsoft 建议尽可能使用标准常规用途 v2 帐户。 |

## <a name="next-steps"></a>后续步骤

- [创建存储帐户](storage-account-create.md)
- [升级到常规用途 v2 存储帐户](storage-account-upgrade.md)
- [恢复删除的存储帐户](storage-account-recover.md)
