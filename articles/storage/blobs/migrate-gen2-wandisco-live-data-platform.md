---
title: '适用于 Azure (预览版的 Data Lake Storage 和 WANdisco 实时数据平台) '
description: 使用适用于 Azure 的 WANdisco 实时数据平台将本地 Hadoop 数据迁移到 Azure Data Lake Storage Gen2。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/06/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: db95f22a17c3776d84f12249693fb23b3d2d94e6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95914433"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>通过适用于 Azure (预览版的 WANdisco 实时数据平台满足苛刻的迁移要求) 

使用 [适用于 Azure 的 WANdisco 实时数据平台](https://docs.wandisco.com/live-data-platform/docs/landing/)将本地 Hadoop 数据迁移到 Azure Data Lake Storage Gen2。 此平台消除了应用程序停机时间的需求，消除了数据丢失的可能性，并确保了数据的一致性，即使在本地操作继续进行时也是如此。  

> [!NOTE]
> 适用于 Azure 的 WANdisco 实时数据平台提供公共预览版。 有关区域可用性，请参阅 [支持的区域](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions)。

该平台由以下两个服务组成： [用于 azure 的实时数据迁移程序](https://www.wandisco.com/products/livedata-migrator-for-azure) ，用于将主动使用的数据从本地环境迁移到 azure 存储，并可确保所有已 [修改的数据](https://www.wandisco.com/products/livedata-plane-for-azure) 或引入数据一致复制。 

> [!div class="mx-imgBorder"]
> ![实时数据平台概述示意图](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

您可以通过使用 Azure 门户和 Azure CLI 来管理这两个服务，并遵循与所有其他 Azure 服务相同的按流量计费的即用即付计费模式。 适用于 Azure 的实时数据平台将显示在相同的每月 Azure 帐单上，并提供一致且方便的方式来跟踪和监视你的使用情况。

不同 [于通过将静态信息复制到 Azure Data Box](./data-lake-storage-migrate-on-premises-hdfs-cluster.md)或使用 Hadoop 工具（如 [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html)）来 _脱机_ 迁移数据，你可以在使用 WANdisco 实时数据 for Azure 的 _联机_ 迁移期间维护业务系统的完全操作。 即使在将大数据环境移到 Azure 中，也要使大数据环境保持运行。

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>适用于 Azure 的 WANdisco 实时数据平台的主要功能

[适用于 Azure 的 WANdisco 实时数据平台](https://docs.wandisco.com/live-data-platform/docs/landing/) 使用唯一的广域网络能力共识引擎来实现数据一致性，并大规模执行数据复制，同时应用程序可以继续修改复制下的数据。  

平台的主要功能包括：

- **数据一致性**：解决了在环境之间迁移大数据卷的难题，并使这些数据在存储系统吞吐量迁移中保持一致，即使这些数据处于持续变化状态。 直接在 Azure 中使用 WANdisco 的独特的广域网络支持的共识引擎来实现数据一致性，并在数据发生更改时，通过一致性保证迁移数据。

- **维护操作**：由于应用程序可以在迁移过程中继续创建、修改、读取和删除数据，因此无需中断业务操作或引入中断时段来将大数据迁移到 Azure。 继续操作应用程序、分析基础结构、引入作业和其他处理。

- **验证结果**：端到端验证：迁移到 Azure 后，你的数据可以有效地使用，需要你对其运行生产应用程序工作负载。 只有实时数据服务可以通过保持数据一致性来提供此操作，而不考虑在迁移的源或目标上进行的更改。 测试和验证应用程序行为，而不会对进程和系统造成风险或更改。

- **降低复杂性**：无需创建和管理计划作业来通过自动化迁移数据来复制数据。 使用与 Azure 的深度集成作为控制平面来管理和监视迁移进度，包括选择性数据复制、Hive 元数据、数据安全和机密性。

- **效率**：保持高吞吐量和高性能，并轻松扩展到大数据量。 通过控制带宽消耗，你可以确保能够满足迁移目标，而不会影响其他系统操作。

## <a name="migrate-big-data-faster-without-risk"></a>无风险地迁移大数据

适用于 Azure 的 WANdisco 实时数据平台的第一个服务是 [适用于 azure 的实时数据迁移](https://www.wandisco.com/products/livedata-migrator-for-azure)程序;用于将主动使用的数据从本地环境迁移到 Azure 存储的解决方案。 适用于 Azure 的实时数据迁移程序完全从 Azure 门户或 Azure CLI 进行设置和管理，并在本地的 Hadoop 群集上运行，无需进行任何配置更改、应用程序修改或服务重启即可立即开始迁移数据。

> [!div class="mx-imgBorder"]
> ![适用于 Azure 体系结构的实时数据迁移程序](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

大数据迁移可能比较复杂，并且具有挑战性。 无需中断业务运营就能移动 pb 的信息，因此无法实现脱机数据复制技术。 [适用于 Azure 的实时数据迁移](https://www.wandisco.com/products/livedata-migrator-for-azure) 程序提供了简单的部署，可以在应用程序读取、写入和修改要迁移的数据的同时，建立包含连续数据迁移和复制的实时数据服务。

执行迁移非常简单，只是以下三个步骤：

1. 将实时数据迁移程序实例从 Azure 门户预配到本地 Hadoop 群集。 不需要群集更改或停机，应用程序可以继续运行。

   > [!div class="mx-imgBorder"]
   >![创建实时数据迁移迁移实例](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. 定义目标 Azure Data Lake Storage Gen2 支持的存储帐户。

   > [!div class="mx-imgBorder"]
   >![创建实时数据迁移目标](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. 定义要迁移的数据的位置，例如： `/user/hive/warehouse` ，并启动迁移。

   > [!div class="mx-imgBorder"]
   > ![创建实时数据迁移迁移](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

通过标准 Azure 工具（包括 Azure CLI 和 Azure 门户）监视迁移进度，并在整个本地环境中继续使用。 在开始之前，请查看这些 [先决条件](https://docs.wandisco.com/live-data-platform/docs/prereq/)。

## <a name="replicate-data-under-active-change"></a>复制活动更改下的数据

将本地数据 lake 大规模迁移到 Azure 需要进行应用程序测试和验证。 能够做到这一点，而不会带来引入数据更改的风险，这些数据更改将创建无法轻松协调的多个真假事实，这对于消除风险并使移动到 Azure 的成本降至最低至关重要。 [适用于 Azure 的实时数据平面](https://www.wandisco.com/products/livedata-plane-for-azure) 使用 WANdisco 的协调引擎技术来克服这些问题。

> [!div class="mx-imgBorder"]
> ![适用于 Azure 体系结构的实时数据平面](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

在初始迁移后，使数据在 Azure 的本地 Hadoop 群集和 Azure 存储中保持一致，并将实时数据平面用于 Azure：

1. 从 Azure 门户开始，为本地和 Azure 中的 Azure 预配实时数据平面。 无需更改应用程序。
2. 配置涵盖要保持一致的数据位置的复制规则，例如： `/user/contoso/sales/region/WA` 。
3. 在任意位置运行访问和修改数据的应用程序，作为与 Hadoop 兼容的文件系统。

Azure 的实时数据平面使数据保持一致，而不会对群集操作或应用程序性能产生严重开销。 修改或引入数据，同时复制所有更改。

## <a name="next-steps"></a>后续步骤

- 适用于 azure for Azure 的[实时数据平台](https://docs.wandisco.com/live-data-platform/docs/landing/)与其他任何 Azure 资源一起使用，现已在预览版中提供。 

- 了解 [先决条件](https://docs.wandisco.com/live-data-platform/docs/prereq/)，计划迁移，并使用 Azure 的实时数据迁移程序快速完成大规模迁移。

- 试用实时数据迁移程序，无需使用 [HDFS 沙盒](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/)进行本地 Hadoop 群集。

## <a name="see-also"></a>另请参阅

- [Azure Marketplace 上适用于 Azure 的实时数据迁移程序](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [Azure Marketplace 上的 Azure 实时数据平面](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [适用于 Azure 计划和定价的实时数据迁移程序](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [Azure 计划和定价的实时数据平面](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [适用于 Azure 的实时数据平台常见问题](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [适用于 Azure 的实时数据平台的已知问题](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Azure Data Lake Storage Gen2 简介](data-lake-storage-introduction.md)