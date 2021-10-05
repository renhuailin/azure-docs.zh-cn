---
title: 了解 Azure NetApp 文件备份 | Microsoft Docs
description: 介绍 Azure NetApp 文件备份的功能、支持的区域和成本模型。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: 8880b128add7e13a34d3b4e3b6b6ad23a8c5df08
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218350"
---
# <a name="understand-azure-netapp-files-backup"></a>了解 Azure NetApp 文件备份

Azure NetApp 文件备份通过为长期恢复、存档与合规性提供完全托管的备份解决方案，来扩展 Azure NetApp 文件的数据保护功能。 该服务创建的备份存储在 Azure 存储中，独立于用于近期恢复或克隆的卷快照。 该服务创建的备份可还原到区域中的新 Azure NetApp 文件卷。 Azure NetApp 文件备份支持基于策略（计划）的备份和手动（按需）备份。 有关更多信息，请参阅 [Azure NetApp 文件快照的工作原理](snapshots-introduction.md)。

> [!IMPORTANT]
> Azure NetApp 文件备份功能目前以预览版提供。 需要通过 [Azure NetApp 文件备份公共预览版](https://aka.ms/anfbackuppreviewsignup)页提交候补名单来请求访问该功能。 等待 Azure NetApp 文件团队的官方确认电子邮件，然后即可使用 Azure NetApp 文件备份功能。

## <a name="supported-regions"></a>支持的区域 

以下区域支持 Azure NetApp 文件备份：   

* 美国东部
* 美国东部 2
* 美国西部 
* 美国中南部
* 美国西部 2
* 北欧 
* 西欧
* 澳大利亚东部
* Japan East

## <a name="cost-model-for-azure-netapp-files-backup"></a>Azure NetApp 文件备份的成本模型

Azure NetApp 文件备份的定价基于备份使用的存储总量。 不会收取任何设置费用，也没有最低使用费。 备份还原的定价基于计费周期内还原的总备份容量。

下面通过一个虚构的示例解释了定价：

* 源卷来自 Azure NetApp 文件高级服务级别。 在当月第一天的开始时间，它的卷配额大小为 1000 GiB，卷已使用大小为 500 GiB。 该卷位于美国中南部区域。
* 你配置了一个每日快照策略，其中有 5 个要保留的本地快照；你还配置了一个每日备份策略来保留 30 个备份副本 。
* 为简单起见，假设源卷以每天 1% 的数据更改常量更改数据，但卷已使用总大小不会增长（保持为 500 GiB）。

将备份策略分配到卷后，将启动到服务管理的 Azure 存储的基线备份。 备份完成后，500 GiB 的基线备份将添加到卷的备份列表中。 基线传输后，每日备份仅备份已更改的块。 假设每天以 5-GiB 的增量添加备份，则使用的总备份存储量为 `500GiB + 30*5GiB = 650GiB`。

在月底，你将以每月 0.05 美元的费率根据备份使用的存储总量支付备份费用。  即，使用 650 GiB 需要支付每月备份总费用 `650*$0.05=$32.5`。 常规 Azure NetApp 文件存储容量适用于本地快照。 有关详细信息，请参阅 [Azure NetApp 文件定价](https://azure.microsoft.com/pricing/details/netapp/)页。

例如，如果你选择将 600 GiB 的备份还原到新卷，则需要按照还原每 GiB 备份容量 0.02 美元的费率付费。 在本例中，还原操作的费用为 `600*$0.02 = $12`。 

## <a name="next-steps"></a>后续步骤

* [Azure NetApp 文件的要求和注意事项](backup-requirements-considerations.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [配置基于策略的备份](backup-configure-policy-based.md)
* [配置手动备份](backup-configure-manual.md)
* [管理备份策略](backup-manage-policies.md)
* [搜索备份](backup-search.md)
* [将备份还原到新卷](backup-restore-new-volume.md)
* [禁用卷的备份功能](backup-disable.md)
* [删除卷的备份](backup-delete.md)
* [卷备份指标](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp 文件备份常见问题解答](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
* [Azure NetApp 文件快照的工作原理](snapshots-introduction.md)
