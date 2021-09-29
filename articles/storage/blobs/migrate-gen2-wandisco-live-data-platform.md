---
title: Data Lake Storage 和 WANdisco LiveData Platform for Azure（预览版）
description: 使用 WANdisco LiveData Platform for Azure 将本地 Hadoop 数据迁移到 Azure Data Lake Storage Gen2。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/17/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: cee20d5104ceb09dc0ba3d826b28108e543cc3d1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664896"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>通过 WANdisco LiveData Platform for Azure（预览版）满足苛刻的迁移要求

使用 [WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) 将本地 Hadoop 数据迁移到 Azure Data Lake Storage Gen2。 此平台无需应用程序故障时间，消除了数据丢失的可能性，并且即使操作在本地继续进行也可确保数据一致性。

> [!NOTE]
> WANdisco LiveData Platform for Azure 以公共预览版提供。 有关区域可用性，请参阅[支持的区域](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions)。

该平台由以下两个服务组成：[LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure)，用于将主动使用的数据从本地环境迁移到 Azure 存储；[LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure)，可确保一致复制所有修改的数据或引入数据。

> [!div class="mx-imgBorder"]
> ![实时数据平台概述示意图](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

可以通过使用 Azure 门户和 Azure CLI 来管理这两个服务，并遵循与所有其他 Azure 服务相同的按流量计费的即用即付计费模式。 LiveData Platform for Azure 使用量将显示在相同的 Azure 月度帐单上，并提供跟踪和监视使用情况的一致便利方式。

与通过[将静态信息复制到 Azure Data Box](./data-lake-storage-migrate-on-premises-hdfs-cluster.md) 或使用 Hadoop 工具（如 [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html)）来“脱机”迁移数据不同，在使用 WANdisco LiveData for Azure“联机”迁移数据期间，你可以维护业务系统的全面运行。 即使在将数据迁移到 Azure 时，也要使大数据环境保持运行。

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>WANdisco LiveData Platform for Azure 的主要功能

[WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) 使用一个支持广域网的独特共识引擎来实现数据一致性，并大规模执行数据复制，同时应用程序可以继续修改正在复制的数据。 <br><br>

> [!VIDEO https://www.youtube.com/embed/KRrmcYPxEho]

该平台的主要功能包括以下内容：

- **数据一致性**：解决了在环境之间迁移大数据卷的难题，并且即使这些数据在不断变化，也能使其在存储系统吞吐量迁移中保持一致。 直接在 Azure 中使用 WANdisco 支持广域网的独特共识引擎来实现数据一致性，并在数据发生更改时，在保证一致性的情况下迁移数据。

- **维护操作**：由于应用程序可在迁移过程中继续创建、修改、读取和删除数据，因此无需中断业务运营或引入中断窗口，即可将大数据迁移到 Azure。 继续操作应用程序，分析基础结构，引入作业以及进行其他处理。

- **验证结果**：要端到端验证数据迁移到 Azure 后是否可有效使用，需要对其运行生产应用程序工作负载。 无论迁移源或目标是否发生更改，只有 LiveData Service 能够通过保持数据一致性来执行此操作，而不会带来数据差异风险。 测试并验证应用程序行为，而不会对进程和系统造成风险或使其发生更改。

- **降低复杂性**：无需创建和管理计划作业，通过利用自动化迁移数据来复制数据。 使用与 Azure 的深度集成作为控制平面来管理和监视迁移进度，包括选择性数据复制、Hive 元数据、数据安全和机密性。

- **效率**：保持高吞吐量和高性能，并轻松扩展到大数据量。 通过控制带宽消耗，可以确保实现迁移目标，而不会影响其他系统操作。

## <a name="migrate-big-data-faster-without-risk"></a>在没有风险的情况下更快地迁移大数据

WANdisco LiveData Platform for Azure 的第一个服务是 [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure)；将已使用的数据主动从本地环境迁移到 Azure 存储的解决方案。 LiveData Migrator for Azure 完全从 Azure 门户或 Azure CLI 预配和管理，并在本地与 Hadoop 群集一起运行，无需进行任何配置更改、应用程序修改或服务重启，可立即开始迁移数据。

> [!div class="mx-imgBorder"]
> ![LiveData Migrator for Azure 体系结构](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

大数据迁移既复杂，又具有挑战性。 采用脱机数据复制技术无法实现在不中断业务运营的情况下迁移数千兆信息。 [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) 提供简单的部署，并可在应用程序读取、写入和修改正在迁移的数据时，建立具有连续数据迁移和复制功能的 LiveData Service。

执行迁移非常简单，只需执行以下三个步骤：

1. 将 LiveData Migrator 实例从 Azure 门户预配到本地 Hadoop 群集。 无需停机或更改群集，应用程序可以继续运行。

   > [!div class="mx-imgBorder"]
   > ![创建 LiveData Migrator 实例](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. 定义启用了 Azure Data Lake Storage Gen2 的目标存储帐户。

   > [!div class="mx-imgBorder"]
   > ![创建 LiveData Migrator 目标](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. 定义要迁移的数据的位置，例如：`/user/hive/warehouse`，并开始迁移。

   > [!div class="mx-imgBorder"]
   > ![创建 LiveData Migrator 迁移](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

通过标准 Azure 工具（包括 Azure CLI 和 Azure 门户）监视迁移进度，并始终继续使用本地环境。 在开始之前，请查看这些[先决条件](https://docs.wandisco.com/live-data-platform/docs/prereq/)。

## <a name="replicate-data-under-active-change"></a>复制主动更改下的数据

将本地数据湖大规模迁移到 Azure 需要测试和验证应用程序。 能够做到这一点而不会带来数据更改的风险（此风险会创建多个不容易协调的数据源），对于消除迁移到 Azure 的风险并将其成本降至最低至关重要。 [LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure) 使用 WANdisco 的协调引擎技术来克服这些问题。

> [!div class="mx-imgBorder"]
> ![LiveData Plane for Azure 体系结构](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

在初始迁移后，利用 LiveData Plane for Azure 使数据在本地 Hadoop 群集和 Azure 存储之间保持一致：

1. 从 Azure 门户开始，在本地和 Azure 中预配 LiveData Plane for Azure。 无需更改应用程序。

2. 配置涵盖要保持一致的数据位置的复制规则，例如：`/user/contoso/sales/region/WA`。

3. 运行根据需要在任意位置作为与 Hadoop 兼容的文件系统访问并修改数据的应用程序。

LiveData Plane for Azure 使数据保持一致，而不会对群集操作或应用程序性能产生巨大开销。 在一致地复制所有更改时，修改或引入数据。

## <a name="next-steps"></a>后续步骤

- 适用于 Azure 的 [LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) 的使用方法和任何其他 Azure 资源一样，现提供预览版。

- 了解[先决条件](https://docs.wandisco.com/live-data-platform/docs/prereq/)，计划迁移，并使用 LiveData Migrator for Azure 快速完成大规模迁移。

- 无需本地 Hadoop 群集，通过使用 [HDFS 沙盒](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/)试用 LiveData Migrator。

## <a name="see-also"></a>另请参阅

- [Azure 市场上的 LiveData Migrator for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [Azure 市场上的 LiveData Plane for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [LiveData Migrator for Azure 计划和定价](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [LiveData Plane for Azure 计划和定价](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice)

- [LiveData Platform for Azure 常见问题](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [LiveData Platform for Azure 的已知问题](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Azure Data Lake Storage Gen2 简介](data-lake-storage-introduction.md)
