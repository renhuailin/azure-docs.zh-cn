---
title: 备份中心概述
description: 本文将概述 Azure 的备份中心。
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: b42bb2719d03108212f62428dc97ed814899c63c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506034"
---
# <a name="overview-of-backup-center"></a>备份中心概述

备份中心在 Azure 中提供单一的统一管理体验，使企业能够大规模治理、监视、操作和分析备份。 因此，它与 Azure 的本机管理体验是一致的。

备份中心的一些主要优势包括：

* **在单个面板中管理备份** - 备份中心旨在跨大型分布式 Azure 环境正常运行。 你可以使用备份中心跨多个工作负载类型、保管库、订阅、区域和 [Azure Lighthouse](../lighthouse/overview.md) 租户有效管理备份。
* **以数据源为中心进行管理** - 备份中心提供视图和筛选器，这些视图和筛选器以你要备份的数据源（例如 VM 和数据库）为中心。 这样，资源所有者或备份管理员便可监视和操作项的备份，而无需关注项将备份到哪个保管库。 此设计的一项重要功能是能够按特定于数据源的属性（如数据源订阅、数据源资源组和数据源标记）筛选视图。 例如，如果你的组织遵循为属于不同部门的 VM 分配不同标记的做法，则可使用备份中心根据要备份的基础 VM 的标记筛选备份信息，而无需关注保管库的标记。
* **连接体验** - 备份中心为实现大规模管理的现有 Azure 服务提供本机集成。 例如，备份中心使用 [Azure Policy](../governance/policy/overview.md) 体验来帮助治理备份。 它还利用 [Azure 工作簿](../azure-monitor/visualize/workbooks-overview.md)和 [Azure Monitor 日志](../azure-monitor/logs/data-platform-logs.md)来帮助查看有关备份的详细报告。 因此，你无需学习任何新的原则即可使用备份中心提供的各种功能。 还可以从备份中心发现社区资源。

## <a name="supported-scenarios"></a>支持的方案

* 备份中心目前支持 Azure VM 备份、Azure VM 中的 SQL 备份、Azure VM 中的 SAP HANA 备份、Azure 文件存储备份、Azure Blobs 备份、Azure 托管磁盘备份和 Azure Database for PostgreSQL 服务器备份。
* 有关支持和不支持的方案的详细列表，请参阅[支持矩阵](backup-center-support-matrix.md)。

## <a name="get-started"></a>入门

若要开始使用备份中心，请在 Azure 门户中搜索“备份中心”并导航到“备份中心”面板 。

![备份中心搜索](./media/backup-center-overview/backup-center-search.png)

你看到的第一个屏幕是“概述”。 它包含两个磁贴，即“作业”和“备份实例” 。

![备份中心磁贴](./media/backup-center-overview/backup-center-overview-widgets.png)

在“作业”磁贴中，可获得过去 24 小时内备份空间内触发的所有备份和还原相关作业的汇总视图。 你可查看有关已完成、已失败和正在进行中的作业数的信息。 选择此磁贴中的任何数字即可查看作业详细信息，了解特定数据源类型、操作类型和状态。

在“备份实例”磁贴中，可获得备份空间内所有备份实例的汇总视图。 例如，你可查看处于软删除状态的备份实例数与仍配置为保护状态的实例数的对比情况。 选择此磁贴中的任何数字即可查看备份详细信息，了解特定数据源类型和保护状态。 你还可查看其基础数据源未找到的所有备份实例（该数据源可能已删除，或你无权访问该数据源）。

观看以下视频，了解备份中心的功能：

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

按照[后续步骤](#next-steps)操作，了解备份中心提供的各种功能，并了解如何使用这些功能来有效地管理备份空间。

## <a name="next-steps"></a>后续步骤

* [监视和操作备份](backup-center-monitor-operate.md)
* [治理备份空间](backup-center-govern-environment.md)
* [获取有关备份的见解](backup-center-obtain-insights.md)
* [使用备份中心执行操作](backup-center-actions.md)